# block的原理

对于Block的理解：

> Block 是一个里面存储了指向定义 block 时的代码块的函数指针，以及block外部上下文变量信息的结构体。
>
> 简单来说就是：带有自动变量的匿名函数。

文章主要介绍的Block的分类、底层实现、内存相关，介绍了Block怎么捕获外部变量

-----

#### Block的分类

Block主要分三类：

1. 堆区Block：**NSMallocBlock**

   引用外部变量，并且经过copy、strong操作的block，常见于使用copy修饰的block，经过copy操作的block

2. 栈区Block：**NSStackBlock**

   引用外部变量，未经过copy、strong修饰的block，常见于函数内声明的block、作为函数参数的block、使用weak修饰的block

3. 全局区Block：**NSGlobalBlock**

   block 内部没有引用外部变量，或者引用的是全局变量、全局静态变量、局部静态变量的Block类型都是 NSGlobalBlock 类型，存储于全局数据区，由系统管理其内存，retain、copy、release操作都无效。

> Block 引用普通外部变量，都是在栈区创建的，只是用 strong、copy 修饰的 Block 会把它从栈区拷贝到堆区一份，而 weak 修饰的 Block 不会；

----

#### Block 源代码分析

##### 利用 Clang 将 Objective-C 代码转换成 C++ 代码

```shell
//1.最简单的命令：
clang -rewrite-objc mian.m

//2.但是如果遇到 main.m:9:9: fatal error: 'UIKit/UIKit.h' file not found 类似的错误需要我们指定下框架
xcrun -sdk iphonesimulator11.4 clang -S -rewrite-objc -fobjc-arc -fobjc-runtime=ios-11.4 main.m

//3.展示 SDK 版本命令
xcodebuild -showsdks
```

##### 通过源码断点调试 Block

可以下载[Block源码](https://opensource.apple.com/source/libclosure/libclosure-65/)，然后将源码中缺少的库添加进入工程，然后写Block相关代码

##### 分析 Block C++ 源代码

通过 clang 将 Block Objective-C 代码转换成以下 C++ 代码

```c++
struct __block_impl {
    void *isa;
    int Flags;
    int Reserved;
    void *FuncPtr;
};

static struct __block_desc_0 {
    size_t reserved;
    size_t Block_size;
} _block_desc_0_DATA = { 0, sizeof(struct __block_desc_0)};

struct _block_impl_0 {

    struct __block_impl impl;
    struct __block_desc_0* Desc;
    int i; // 这个是引用外部变量 i
    _block_impl_0(void *fp, struct __block_desc_0 *desc, int _i, int flags=0) :i(_i){

        impl.Flags = flags;
        impl.FuncPtr = fp;
        Desc = desc;
    }
};
```

1. 结构体中有 isa 指针，证明 Block 也是一个对象
2. Block 底层是用结构体来实现的，结构体 `_block_impl_0` 包含了`__block_impl` 结构体和 `__block_desc_0` 结构体。
3. `__block_impl` 结构体中的 `FuncPtr` 函数指针，指向的就是我们的 Block 的具体实现。真正调用 Block 就是利用这个函数指针去调用的。
4. 为什么能访问外部变量，就是因为将外部变量复制到了结构体中（上面的 int i），即自动变量会作为成员变量追加到 Block 结构体中。

##### 分析具有 __block 修饰符外部变量的 Block 源代码

我们知道 Block 截获外部变量是将外部变量作为成员变量追加到 Block 结构体中，但是匿名函数存在作用域的问题，这个就是为什么我们不能在 Block 内部去修改普通外部变量的原因。所有就出现了 __block 修饰符来解决这个问题。

下面我们来看下 __ block 修饰的变量转换成 C++ 代码是什么样子的。

```
//Objective-C 代码
 - (void)blockDataBlockFunction {
 __block int a = 100;  ///在栈区
 void (^blockDataBlock)(void) = ^{
 a = 1000;
 NSLog(@"%d", a);
 };  ///在堆区
 blockDataBlock();
 }

//C++ 代码
struct __Block_byref_a_0 {
  void *__isa;
__Block_byref_a_0 *__forwarding;
 int __flags;
 int __size;
 int a;
};

struct __BlockStructureViewController__blockDataBlockFunction_block_impl_0 {
  struct __block_impl impl;
  struct __BlockStructureViewController__blockDataBlockFunction_block_desc_0* Desc;
  __Block_byref_a_0 *a; // by ref
};
```

具有 __block 修饰的变量，会生成一个 Block_byref_a_0 结构体来表示外部变量，然后再追加到 Block 结构体中，这里生成 Block_byref_a_0 这个结构体大概有两个原因：一个是抽象出一个结构体，可以让多个 Block 同时引用这个外部变量；另外一个好管理，因为 Block_byref_a_0 中有个非常重要的成员变量 `forwarding` 指针，这个指针非常重要（这个指针指向 Block_byref_a_0 结构体），这里是保证当我们将 Block 从栈拷贝到堆中，修改的变量都是同一份。

##### Block 是如何解决存储域问题

首先我们知道 Block 底层是用结构体，Block 会转换成 block 结构体。

然后 block 没有截获外部变量、截获全局变量的都是属于全局区的 Block，即 GlobalBlock；其余的都是栈区的 Block，即 StackBlock。

对于全局区的 Block，是不存在作用域的问题，但是栈区 Block 不同，在作用域结束后就会 pop 出栈，__block 变量也是在栈区的，同理作用域结束也会 pop 出栈。

为了解决作用域的问题，Block 提供了 Copy 函数，将 Block 从栈复制到堆上，在 MRC 环境下需要我们自己调用 `Block_copy` 函数，这里就是为什么 MRC 下，我们为什么需要用 copy 来修饰 Block 的原因。

然而在 ARC 环境下，编译器会尽可能给我们自动添加 copy 操作，这里为什么说尽量呢，因为有些情况编译器无法判断的时候，就不会给我们添加 copy 操作，这里就需要我们自己主动调用 copy 方法了。

##### __block 变量的存储域

Block 从栈复制到堆上，_block 修饰的变量也会从栈复制到堆上；为了结构体 \_block 变量无论在栈上还是在堆上，都可以正确的访问变量，我们需要 forwarding 指针

在 Block 从栈复制到堆上的时候，原本栈上结构体的 forwarding 指针，会改变指向，直接指向堆上的结构体。这样子就可以保证之后我们都是访问同一个结构体中的变量，这里就是为什么 __block 修饰的变量，在 Block 内部中可以修改的原因了。

##### Block 截获对象需要管理对象的生命周期

我们知道 Block 引用外部变量会将其追加到结构体中，但是编译器是无法判断 C 语言结构体的初始化和废弃的，因此在 __block_desc_0 会增加成员变量 copy 和 dispose；以及 block_copy、block_dispose 函数。

用来 Block 从栈复制到堆、堆上的 Block 废弃的时候分别调用。

##### Block 会出现循环引用

对于 Block 循环引用算是经典问题了，当 A 持有 B，B 又持有 A，这个时候就会出现循环引用。Block 对于外部变量都会追加到结构体中，所以在实现 Block 时候需要注意这个问题。

ARC 环境一般我们用 _*weak 来打破，MRC 环境的话，我们可以使用 \*_block 来打破循环引用。

-----

#### Block 为什么用 Copy 修饰

对于这个问题，得区分 MRC 环境 和 ARC 环境；首先，通过上面小节可知，Block 引用了普通外部变量，都是创建在栈区的；对于分配在栈区的对象，我们很容易会在释放之后继续调用，导致程序奔溃，所以我们使用的时候需要将栈区的对象移到堆区，来延长该对象的生命周期。

对于 MRC 环境，使用 Copy 修饰 Block，会将栈区的 Block 拷贝到堆区。

对于 ARC 环境，使用 Strong、Copy 修饰 Block，都会将栈区的 Block 拷贝到堆区。

所以，Block 不是一定要用 Copy 来修饰的，在 ARC 环境下面 Strong 和 Copy 修饰效果是一样的。