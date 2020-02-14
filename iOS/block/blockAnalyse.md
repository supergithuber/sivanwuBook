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
2. 栈区Block：**NSStackBlock**
3. 全局区Block：**NSGlobalBlock**