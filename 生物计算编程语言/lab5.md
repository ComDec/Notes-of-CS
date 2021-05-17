# 上机实验3 GDB的使用

[toc]



## 学习目标

1. 掌握gcc的基本使用，熟悉gcc编译的全过程
2. 掌握部分如`C_INCLUDE_PATH`, `LIBRARY_PATH`, `LD_LIBRARY_PATH`等编译相关环境变量的使用
3. 熟悉objdump和readelf的使用
4. 熟悉GDB调试程序的过程和常用的指令



## 1. `gcc`介绍与使用

- C语言标准库文件：/usr/lib64/libc.so.6

  ```bash
  [root@bioinfo ~]# rpm -qa | grep glibc
  glibc-headers-2.17-307.el7.1.x86_64
  glibc-devel-2.17-307.el7.1.x86_64
  glibc-common-2.17-307.el7.1.x86_64
  glibc-2.17-307.el7.1.x86_64
  ```

- C++语言标准库文件：/usr/lib64/libstdc++.so.6

- Linux下的目标文件格式为**ELF（Executable and Linkable Format）**有三种类型：

  - **可重定位（relocatable）目标文件**：包含二进制代码与数据，可在编译时与其他可重定位目标文件合并，创建一个可执行目标文件。
  - **可执行目标文件**：包含二进制代码与数据，可直接加载到内存中运行。
  - **共享目标文件**：一种特殊的可重定位目标文件，可在加载时或者运行时被动态的加载进内存并链接，在linux中为`*.so`文件。

- 一个典型的ELF头以16字节的序列开始，序列描述了生成该文件系统

  - 字的大小（Word size）
  - 字节顺序（Byte-order）
  - ELF头的大小（ELF Header size)
  - 目标文件的类型（File Type）
  - 机器类型（Machine type）
  - 节头部表的文件偏移（offset）
  - 节头部表的大小和数量

- **字节序**：什么是**LSB**？

  > LSB stands for least-significant byte first, or we can call it little-endian.

  假设存在第一个无符号十六进制数值0x1234，内存中至少需要两个字节来表示。内存中是以字节为单位进行编址的，假设也就是存放在地址`a`和地址`a+1`中。这时候就有两种存放的策略

  - 如果采用大尾方式存储，则`a`存储`12`，`a+1`存储`34`;
  - 如果采用小尾策略存储，则`a`存储`34`，`a+1`存储`12`。

- 夹在ELF头和节头部表之间的节：

  - **.text**：已编译的机器代码
  - **.rodata**：只读数据，如`printf`的格式化字符串
  - **.data**：已初始化的全局和静态变量。局部变量在运行的栈中，不出现在节中。
  - **.bss**：未初始化的全局和静态变量，以及所有被初始化为0的全局和静态变量。目标文件这个节不占据实际的空间，仅作为一个占位符。
  - **.symtab**：一个符号表，存放程序中定义和应用的**函数**以及**全局变量**的信息。
  - **.rel.text**：一个在.text节中位置的列表，当链接器把这个目标文件与其他目标文件结合时，需要修改这些位置。
  - **.rel.data**：被模块引用或者定义的所有全局变量的重定位信息。
  - **.debug**：一个调试符号表，其条目是程序中定义的局部变量和类型定义，程序中定义和应用的全局变量，以及初识的C源程序。只有使用`-g`选项编译才会得到这张表。
  - **.strtab**：一个字符串表，其内容包含**.symtab**和**.debug**中的符号表，以及节头部表中的节名称。

- 什么是**重定位**？

  > 重定位将每个符号引用和符号定义关联起来，并且为每个符号分配运行时地址。

### （1）GCC编译过程

一般来说，gcc的编译过程分为下面几个主要步骤 ：

```
 cpp -> cc1 -> as -> ld
```

1. 预处理（Preprocessing）

   ```bash
   gcc -E test.c -o test.i
   ```

2. 编译（Compiling）

   ```bash
   gcc -S test.i -o test.s
   ```

3. 汇编（Assembling）

   ```bash
   gcc -c test.s -o test.o
   ```

   怎么理解下面这个输出结果？

   ```bash
   $ file test.o
   test.o: ELF 64-bit LSB relocatable, x86-64, version 1 (SYSV), not stripped
   ```

   - ELF文件
   - 64-bit
   - LSB：字节序
   - relocatable：可重定位目标文件，与可执行目标文件不同

4. 链接（Linking）

   ```bash
   gcc test.o -o test
   ```

   链接需要完成的两个任务为：

   > 1. 符号解析：将每个符号的**引用** 与每个符号的**定义**关联起来。
   > 2. 重定位：编译和汇编生成从地址0开始的代码和数据节。链接器通过把每个**符号定义**与一个**内存位置**关联起来，然后修改所有对这些符号的引用，使得它们指向相应的内存位置。

   这里所说的符号对应汇编文件中的一个个标签（label），包含

   - 一个**函数**
   - 一个**全局变量**
   - 一个**静态变量**

### （2）GCC常用编译选项

| 选项          | 含义                                              |
| ------------- | ------------------------------------------------- |
| -static       | 链接静态库，禁止使用动态共享库                    |
| -shared       | 进行动态库编译，链接动态库                        |
| -Ldir         | 在动态库的搜索路径中增加dir目录                   |
| -lname        | 链接静态库(libname.a)或动态库(libname.so)的库文件 |
| -fPIC(或fpic) | 生成使用相对地址无关的目标代码                    |
|               |                                                   |

#### 注解

1. **PIC**是位置无关代码（Position-independent code）的缩写；
2. 用`-static`的话，势必禁止使用动态共享库，但这往往是有副作用的，因为某些库往往都是动态共享库而没有静态库，这时候要注意。



### （3）静态库与动态共享库

- 静态库的文件名一般是`libname.a`

- 动态共享库的文件名一般是`libname.so`

  ```c
  // test.c
  #include "test.h"
  void test() {
     printf("hello, test!\n");
  }
  
  // test.h
  #ifndef __TEST__
  #define __TEST__
  #include <stdio.h>
  void test();
  #endif
  ```

- 如何制作静态库：

  ```bash
  $ gcc -c test.c
  $ ar rcs libtest.a test.o
  ```

- 如何编译共享库：

  ```bash
  $ gcc -fPIC -c test.c
  $ gcc -shared -o libtest.so test.o
  ```

  ```c
  // hello.c
  #include <test.h>
  int main() {
     test();
     
     return 0;
  }
  ```

- 如何利用动态共享库

  ```bash
  $ gcc -o hello hello.c -L. -ltest -I.
  $ ./hello
  hello, test!
  ```

  可以看看这时候的文件大小：

  ```bash
  $ ls -lh
  -rwxrwxr-x 1 bio bio 8.3K 5月  17 23:01 hello
  ```

  但是，动态共享库的一个问题是一旦共享库丢失或者版本变换，可能会出现问题：

  ```bash
  $ mv libtest.o libtest.so.bak
  $ ./hello
  ./hello: error while loading shared libraries: libtest.so: cannot open shared object file: No such file or directory
  ```

- 如何利用静态库

  ```bash
  $ gcc -static -o hello hello.c -L. -ltest -I.
  ```

  如果出现下面的错误

  ```
  /bin/ld: cannot find -lc
  ```

  则说明缺少C的标准静态库，则需要安装：

  ```bash
  $ yum install glibc-static 
  ```

  在动态共享库与静态库都存在的情况下，如果不指定`-static`的话，则优先使用动态共享库；如果要强制使用静态库，则需要指定`-static`；一旦只存在静态库或者动态共享库，则使用存在的一种。

- 静态编译将静态库整合入可执行文件，因此，其文件也通常会更大一些

  ```bash
  $ ls -lh hello
  -rwxrwxr-x 1 bio bio 845K 5月  17 23:24 hello
  ```

  但这也有个好处，即使库文件丢失也不影响可执行目标文件：

  ```bash
  $ mv libtest.a libtest.a.bak
  $ ./hello
  hello, test!
  ```

  但是，静态库也有两个非常大的缺点：

  > 1. 每次程序运行时都会在内存中加载一个静态库的副本
  > 2. 每次更新静态库都需要重新链接程序与静态库



## 2. `objdump`的使用

`objdump`是`binutils`包的重要组成部分，其目的是可以进行反汇编，将二进制可执行目标文件或者可重定位目标（.o）文件反汇编为汇编代码的工具。

常规上将可执行文件反汇编为汇编语言和对应的源代码部分。这里以一个二进制可执行文件反汇编为例。-哦

这是源代码：

```c
/* test.c */
#include <stdio.h>

int main() {
        int x, y, z;
        x = 3;
        y = 5;
        z = x + y;
        printf("%d\n", z);

        return 0;
}
```

进行编译：

```bash
gcc -g -o test test.c
```

查看符号信息：

```bash
readelf --sym test
```

得到

```
Symbol table '.dynsym' contains 4 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND printf@GLIBC_2.2.5 (2)
     2: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND __libc_start_main@GLIBC_2.2.5 (2)
     3: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__

Symbol table '.symtab' contains 67 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     ...
    27: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
    ...
    36: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS test2.c
    ...
    48: 0000000000601030     0 NOTYPE  WEAK   DEFAULT   24 data_start
    49: 0000000000601038     0 NOTYPE  GLOBAL DEFAULT   24 _edata
    50: 00000000004005e4     0 FUNC    GLOBAL DEFAULT   14 _fini
    51: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND printf@@GLIBC_2.2.5
    52: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND __libc_start_main@@GLIBC_
    53: 0000000000601030     0 NOTYPE  GLOBAL DEFAULT   24 __data_start
    54: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
    55: 00000000004005f8     0 OBJECT  GLOBAL HIDDEN    15 __dso_handle
    56: 00000000004005f0     4 OBJECT  GLOBAL DEFAULT   15 _IO_stdin_used
    57: 0000000000400570   101 FUNC    GLOBAL DEFAULT   13 __libc_csu_init
    58: 0000000000601040     0 NOTYPE  GLOBAL DEFAULT   25 _end
    59: 0000000000400440     0 FUNC    GLOBAL DEFAULT   13 _start
    60: 000000000060103c     4 OBJECT  GLOBAL DEFAULT   25 a
    61: 0000000000601038     0 NOTYPE  GLOBAL DEFAULT   25 __bss_start
    62: 0000000000400530    56 FUNC    GLOBAL DEFAULT   13 main
    63: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _Jv_RegisterClasses
    64: 0000000000601038     0 OBJECT  GLOBAL HIDDEN    24 __TMC_END__
    65: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMCloneTable
    66: 00000000004003e0     0 FUNC    GLOBAL DEFAULT   11 _init
```

可以看到main的符号，其运行时在内存中的相对位移地址是400530（**main**函数），main函数的代码大小为56字节。



然后反汇编：

```bash
objdump -S test >testcode
```

查看反汇编的结果：

```assembly
0000000000400530 <main>:
#include <stdio.h>

int main() {
  400530:       55                      push   %rbp
  400531:       48 89 e5                mov    %rsp,%rbp
  400534:       48 83 ec 10             sub    $0x10,%rsp
        int x, y, z;
        x = 3;
  400538:       c7 45 fc 03 00 00 00    movl   $0x3,-0x4(%rbp)
        y = 5;
  40053f:       c7 45 f8 05 00 00 00    movl   $0x5,-0x8(%rbp)
        z = x + y;
  400546:       8b 45 f8                mov    -0x8(%rbp),%eax
  400549:       8b 55 fc                mov    -0x4(%rbp),%edx
  40054c:       01 d0                   add    %edx,%eax
  40054e:       89 45 f4                mov    %eax,-0xc(%rbp)
        printf("%d\n", z);
  400551:       8b 45 f4                mov    -0xc(%rbp),%eax
  400554:       89 c6                   mov    %eax,%esi
  400556:       bf 00 06 40 00          mov    $0x400600,%edi
  40055b:       b8 00 00 00 00          mov    $0x0,%eax
  400560:       e8 ab fe ff ff          callq  400410 <printf@plt>

        return 0;
  400565:       b8 00 00 00 00          mov    $0x0,%eax
}
```



## 3. GDB调试程序

gdb的调试步骤一般分为6步，实际调试过程当然并不是每一步都必须用到，需要在使用中灵活进行组合。

### （1）GDB的调试步骤

1. 启动gdb，加载要调试的可执行文件，这有两种常用的方式

   | 命令                     | 作用                                                 |
   | ------------------------ | ---------------------------------------------------- |
   | gdb [可执行文件名]       | 启动gdb调试工具，并加载可执行文件                    |
   | gdb; file [可执行文件名] | 启动gdb调试工具；在gdb环境中用file命令加载可执行文件 |

   

2. 用break为程序设置断点，设置的方式有很多，这里就介绍两种

   | 命令           | 作用                             |
   | -------------- | -------------------------------- |
   | break [函数名] | 在函数的入口处设置断点，比如main |
   | break test.c:3 | 在test.c源程序的第3行处设置断点  |

   

3. 用run启动并运行已经加载的程序

   | 命令 | 作用                             |
   | ---- | -------------------------------- |
   | run  | 启动程序运行，程序会在断点处停下 |

   

4. 查看程序运行的当前状态

   - 程序的当前断点位置信息

     反映程序已经执行了哪些指令，下一步需要执行哪一条指令。其中，寄存器rip（return instruction pointer）保存了下一条将要执行的指令地址：

     | 命令    | 功能                                     |
     | ------- | ---------------------------------------- |
     | i r     | info registers，显示所有寄存器的内容     |
     | i r rip | info registers rip 只显示rip寄存器的内容 |

     

   - 当前的通用寄存器内容

     | 命令                | 功能 |
     | ------------------- | ---- |
     | i r rax rbx rcx rdx |      |

     

   - 当前存储器中存储的数据信息

     | 命令                 | 功能                                                         |
     | -------------------- | ------------------------------------------------------------ |
     | x/8xb 0x7fffffffe3a0 | 显示从这个地址单元开始的8个bytes内存中的内容，并用16进制表示 |
     | x/2xw 0x7fffffffe3a0 | 功能同上，但显示的是2个words的内容                           |

     

   - 当前的栈帧信息等

     x86-64用栈来支持过程的嵌套调用，过程的入口参数、返回地址、被保存寄存器的值、被调用过程中的非静态局部变量都会被保存到栈中。所谓栈帧，就是系统为每一个执行的过程分配的栈空间称为栈帧。

     | 命令                           | 作用                 |
     | ------------------------------ | -------------------- |
     | `i r rsp rbp`                  | 查看当前栈帧范围     |
     | ```y=R[rbp]-R[rsp]+4; z=y/4​``` | 当前栈帧字节数和字数 |
     | `x/yxb $esp; x/zxw $esp`       | 当前栈帧中保存的信息 |

     #### 注

     - 这里的rbp是栈底指针，rsp是栈顶指针
     - 由栈底向栈顶生长，栈底是高地址，栈顶是低地址

     | 指针 | 地址      | 值           | 说明        |
     | ---- | --------- | ------------ | ----------- |
     | rbp  | ffffd2c8H | 原rbp值      | 栈底        |
     |      | ...       | ...          | ...         |
     |      | ...       | ...          | ...         |
     |      | ffffd2bcH | z=0000 0008H | -0x0c(%rbp) |
     |      | ffffd2b8H | y=0000 0005H | -0x10(%rbp) |
     |      | ffffd2b4H | x=0000 0003H | -0x14(%rbp) |
     | rsp  | ffffd2b0H |              | 栈顶        |

     

5. 继续执行下一条指令

   | 命令 | 作用               |
   | ---- | ------------------ |
   | si   | 执行下一条机器指令 |
   | s    | 执行下一条C语句    |

   

6. quit退出调试



### （1）简单实例

这里以下面这个简单程序为例说明：

```c
#include <stdio.h>

int main() {
   int x, y, z;
   x = 3;
   y = 4;
   z = x + y;
   print("x + y = %d\n", z);
   
   return 0;
}
```

我们编译的时候，加上`-g`的调试选项

```bash
gcc -g -o test test.c
```

然后进入到调试模式：

```bash
gdb ./test
```

这样我们可以设置断点在main 函数入口处：

```gdb
(gdb) break main
Breakpoint 1 at 0x400538: file test.c, line 5.
```

然后输入run，让程序停止在断点处：

```gdb
(gdb) run
Starting program: /home/bio/bi296/chap6/code/test

Breakpoint 1, main () at test.c:5
5               x = 3;
```

查看寄存器的信息：

```gdb
(gdb) info registers
rax            0x400530 4195632						// accumulator寄存器64位版本
rbx            0x0      0								// base寄存器的64位版本
rcx            0x400570 4195696						// counter寄存器的64位版本
rdx            0x7fffffffe418   140737488348184	// data寄存器的64位版本
rsi            0x7fffffffe408   140737488348168 // source index
rdi            0x1      1								// destination index
rbp            0x7fffffffe320   0x7fffffffe320    // bp寄存器的64位版本
rsp            0x7fffffffe310   0x7fffffffe310	  // 堆栈指针sp寄存器的64位版本
r8             0x7ffff7dd5e80   140737351868032
r9             0x0      0
r10            0x7fffffffde20   140737488346656
r11            0x7ffff7a2f460   140737348039776
r12            0x400440 4195392
r13            0x7fffffffe400   140737488348160
r14            0x0      0
r15            0x0      0
rip            0x400538 0x400538 <main+8>		// IP寄存器的64位版本，下一条指令地址
eflags         0x202    [ IF ]					// 32位的标识寄存器
cs             0x33     51							// 代码段寄存器code
ss             0x2b     43							// 堆栈段寄存器stack
ds             0x0      0							// 数据段寄存器data
es             0x0      0							// 附加段寄存器extra
fs             0x0      0							// 标识段寄存器flag
gs             0x0      0							// 全局段寄存器global
```

可以看到的是一系列寄存器中的信息：

![](D:\course\bi296\images\general_registers.gif)

```
# 常用的gdb命令及其意义
break/b 行号				  在行号后设置断点
break/b 函数名				 在函数名处设置断点
breaktrace/bt				查看各级函数调用及参数
continue/c					继续往下执行
info/i						查看当前栈局部变量的值
info break					查看断点信息
finish						执行到当前函数返回，然后停下来等待命令
list/l + 行号/函数名		输出C代码
next/n						执行下一句代码，然后停住
print/p						输出一个变量或表达式的值
quit/q						退出gdb
run/r							从头开始执行程序
set var						修改变量的值
step/s						进入函数调用，到下一条语句或指令
x								查看内存地址中的内容
```

### 查看内存地址内容的语法

```gdb
x/nfu addr
x addr
x
```

- `n` - 内存单元的数量
- `f` - 显示形式
  - `x` - 十六进制hexademical
  - `d` - 十进制decimal integer
  - `u` - 无符号十进制整数unsigned decimal integer
  - `o`- 八进制octal
  - `t` - 二进制
  - `a` - 无符号十六进制
  - `c` - 字符character
  - `f` - 浮点数float
  - `s` - 字符串string
- `u`- 单元的单位
  - `b` - 字节bytes
  - `h` - 两个字节halfwords
  - `w`- 四个字节words
  - `g` - 八个字节giantwords

#### GDB警告

1. Missing separate debuginfos, use: debuginfo-install glibc-2.17-307.el7.1.x86_64

   现在的centos7发行版内核中是不带debuginfo的，需要自行安装。你可以从http://debuginfo.centos.org/7/x86_64/中寻找到跟内核版本匹配的两个包：

   - kernel-debuginfo-$(uname ).rpm
   - kernel-debug-debuginfo-$(uname).rpm

   安装以后就可以用：

   ```bash
   debuginfo-install glibc-2.17-307.el7.1.x86_64
   ```

   


## 4. ==实例代码==与==调试过程==

```c
#include <stdio.h>

int main() {
        float x, y, z;
        x = 3.25;
        y = 1.12;
        z = x - y;
        printf("x - y = %f\n", z);

        return 0;
}
```

这是对应的`objdump`反汇编出来的代码：

```assembly
0000000000400530 <main>:
#include <stdio.h>

int main() {
  400530:       55                      push   %rbp
  400531:       48 89 e5                mov    %rsp,%rbp
  400534:       48 83 ec 10             sub    $0x10,%rsp
        float x, y, z;
        x = 3.25;
  400538:       8b 05 de 00 00 00       mov    0xde(%rip),%eax        # 40061c <__dso_handle+0x14>
  40053e:       89 45 fc                mov    %eax,-0x4(%rbp)
        y = 1.12;
  400541:       8b 05 d9 00 00 00       mov    0xd9(%rip),%eax        # 400620 <__dso_handle+0x18>
  400547:       89 45 f8                mov    %eax,-0x8(%rbp)
        z = x - y;
  40054a:       f3 0f 10 45 fc          movss  -0x4(%rbp),%xmm0
  40054f:       f3 0f 5c 45 f8          subss  -0x8(%rbp),%xmm0
  400554:       f3 0f 11 45 f4          movss  %xmm0,-0xc(%rbp)
        printf("x - y = %f\n", z);
  400559:       f3 0f 10 45 f4          movss  -0xc(%rbp),%xmm0
  40055e:       0f 5a c0                cvtps2pd %xmm0,%xmm0
  400561:       bf 10 06 40 00          mov    $0x400610,%edi
  400566:       b8 01 00 00 00          mov    $0x1,%eax
  40056b:       e8 a0 fe ff ff          callq  400410 <printf@plt>

        return 0;
  400570:       b8 00 00 00 00          mov    $0x0,%eax
}
```

然后执行gdb进行调试，可以看到

```gdb
(gdb) file test2								// 加载test2这个可执行文件
Reading symbols from /home/bio/bi296/chap6/code/test2...done.
(gdb) break main								// 在main函数处设置断点
Breakpoint 1 at 0x400538: file test2.c, line 5.
(gdb) run										// 执行到断点处
Starting program: /home/bio/bi296/chap6/code/test2
Breakpoint 1, main () at test2.c:5
5               x = 3.25;
(gdb) x/1fw $rbp - 4							// 查看单精度浮点数的数值，一个word，用float形式
0x7fffffffe3bc: 3.25
(gdb) x/1xw $rbp - 4							// 查看单精度浮点数的IEEE-754表示，一个word，用十六进制
0x7fffffffe3bc: 0x40500000
(gdb) x/1tw $rbp - 4							// 查看二进制形式
0x7fffffffe3bc: 01000000010100000000000000000000
(gdb) s											// 停在下一条指令后
7               z = x - y;
(gdb) x/1fw $rbp - 8							// 查看y的浮点数值，$rbp - 8为其内存中的地址
0x7fffffffe3b8: 1.12
```



## 学习小结

本节课我们

- 学习了如何用gcc进行c程序的编译；
- 对c程序的编译过程——预处理（Preprocessing）、汇编（Compilation）、编译（Assembly）、链接（linking）等四个步骤进行了了解，获悉了这几个步骤的操作命令；
- 掌握了基本的反汇编过程——binutils中的objdump工具；
- 对于gdb的调试过程和常用的调试命令有了更多的了解。

剩下的当然就是好好总结，完成我们的练习，在后面的实践中掌握如何去做更好的调试。



gcc -g -o test test.c

gdb ./test

list

break run/next

显示变量值: var/n-x-b &var