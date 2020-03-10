# Mach-O文件格式分析

Mach-O文件是OSX和iOS下的可执行文件，类似于linux下的ELF文件、windows下的PE32/PE32+文件，都是可执行文件的格式。在iOS上我们平时接触的库文件、DSYM文件、framework都属于这种格式的文件。

![mach-O_structure](./img/mach-O_structure.png)

可以用[MachOView](https://sourceforge.net/projects/machoview/)查看Mach-O文件的结构

#### 1. Header结构

`Mach－O`的头部，使得可以快速确认一些信息，比如当前文件用于32位还是64位，对应的处理器是什么、文件类型是什么

#### 2. Load commands 结构

#### 3. Segment & Section



