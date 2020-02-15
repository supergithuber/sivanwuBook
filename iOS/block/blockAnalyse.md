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