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

