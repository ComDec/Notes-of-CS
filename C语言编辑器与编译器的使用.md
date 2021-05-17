# 实验1：C语言编辑器与编译器的使用

- **学号**：519111910172	
- **姓名**：王喜



[toc]



## 学习目标

1. 掌握Emacs的基本使用，尤其是一些常用的快捷键的使用方法
2. 掌握GCC的使用，对编译过程有一个基本的了解
3. 掌握Linux的基本使用



## 1. Emacs的基本操作

首先，我们来了解Emacs中快捷键的使用和操作。在下面使用的快捷键中，

- `C`——代表·`CTRL`键
- `M`——代表`Meta`键，在我们的键盘上为`ALT`或`ESC`，而在MAC中则使用`OPTION`



1. 常用的与光标移动有关的快捷键

| 快捷键       | 意义                                                     | 快捷键       | 意义                                                       |
| ------------ | -------------------------------------------------------- | ------------ | ---------------------------------------------------------- |
| C-n          | next-line 下一行（Next line）                            |              |                                                            |
| C-p          | previous-line 前一行（Previous line）                    |              |                                                            |
| C-f, C-right | forward-char/right-char 向前/向右移动一个字符（Forward） | M-f, M-right | forward-word 向前移动一个单词（Forward）                   |
| C-b, C-left  | backward-char/left-char 向后移动一个字符（Backward）     | M-b, M-left  | backward-word 向后移动一个单词（Backward）                 |
| C-k          | kill-line 从光标位置删除到行尾（Kill）                   |              |                                                            |
| C-a          | move-beginning-of-line 移动到行首（A is the first char） | M-a          | c-beginning-of-statement 移动到段首（A is the first char） |
| C-e          | move-end-of-line 移动到行尾（End of line）               | M-e          | c-end-of-statement 移动到段尾（End of line）               |
|              |                                                          | M-<          | beginning-of-buffer 移动到缓冲区的开始位置（向左的箭头）   |
|              |                                                          | M->          | end-of-buffer 移动到缓冲区的最后位置（向右的箭头）         |
| C-v          | scroll-up-command 向下翻一屏（v像一个向下的箭头）        | M-v          | scroll-down-command 向上翻一屏                             |
| C-x ]        | forward-page 光标下移一页                                | M-}          | forward-paragraph 光标下移一个段落                         |
| C-x [        | backward-page 光标前移一页                               | M-{          | backward-paragraph 光标上移一个段落                        |



2. 文件操作

   | 快捷键  | 对应的命令              | 动作                                         |
   | ------- | ----------------------- | -------------------------------------------- |
   | C-x C-f | find-file               | 输入文件名，查找文件并在一个缓冲区中打开文件 |
   | C-x C-v | find-alternate-file     | 读入另一个文件，替换由`C-x c-f`导入的文件    |
   | C-x C-s | save-buffer             | 保存当前文件                                 |
   | C-x C-w | write-file              | 另存当前缓冲区内容为另一个文件               |
   | C-x i   | insert-file             | 把文件插入到当前光标位置                     |
   | C-x u   | undo                    | 撤销操作                                     |
   | C-_     | undo                    | 撤销操作                                     |
   | C-x C-c | save-buffers-kill-emacs | 退出emacs                                    |



3. 查看帮助文档

| 快捷键 | 对应命令           | 动作                                                 |
| ------ | ------------------ | ---------------------------------------------------- |
| C-h    | help-command       | 进入Emacs的在线帮助系统                              |
| C-h t  | help-with-tutorial | 查看文档（Tutorial）                                 |
| C-h k  | describe-key       | 输入快捷键，查看快捷键的意义（Keybind）              |
| C-h f  | describe-function  | 输入函数，查看函数的定义以及快捷键的绑定（Function） |

4. 文本编辑

   | 快捷键  | 对应命令             | 动作                   |
   | ------- | -------------------- | ---------------------- |
   | C-x h   | mark-whole-buffer    | 全选                   |
   | M-w     | kill-ring-save       | 复制                   |
   | C-w     | kill-region          | 剪切                   |
   | C-y     | yank                 | 粘贴                   |
   | M-y     | yank-pop             | 粘贴剪贴板前一项内容   |
   | C-k     | kill-line            | 剪切当前行从光标到行尾 |
   | M-k     | kill-sentence        | 删除光标到段落的结尾   |
   | C-d     | delete-char          | 删除光标后的一个字符   |
   | M-d     | kill-word            | 删除光标后的一个单词   |
   | DEL     | delete-backward-char | 往光标前删除一个字符   |
   | M-DEL   | backward-kill-word   | 往光标前删除一个单词   |
   | C-o     | open-line            | 插入空行               |
   | C-x C-o | delete-blank-line    | 删除空白行             |

   

5. 命令操作

   | 快捷键          | 命令                     | 动作               |
   | --------------- | ------------------------ | ------------------ |
   | M-x 命令        | execute-extended-command | 执行后面的命令     |
   | C-x z           | repeat                   | 重复前个命令       |
   | C-u n <command> | universal-argument       | 重复执行n次command |

6. 重复命令

   | 快捷键       | 动作                  |
   | ------------ | --------------------- |
   | M-10 C-n     | 光标下移10行          |
   | C-u 100 C-f  | 光标向后移动100个字符 |
   | C-u 10 SPACE | 输入10个连续空格      |
   | C-u 10 C-v   | 下移10段              |

   

7. 窗口操作

   | 快捷键 | 对应命令            | 意义               |
   | ------ | ------------------- | ------------------ |
   | C-x 1  | delete-other-window | 只显示一个窗口     |
   | C-x 2  | split-window-below  | 拆分为上下两个窗口 |
   | C-x 3  | split-window-right  | 拆分为左右两个窗口 |
   | C-x b  | switch-to-buffer    | 切换窗口           |

    

8. 标记段落

   | 快捷键 | 对应命令         | 意义                                         |
   | ------ | ---------------- | -------------------------------------------- |
   | C-@    | set-mark-command | 从当前光标开始，到选定的范围，标记选定的段落 |

   

9. 缓冲区操作

   | 快捷键  | 对应命令             | 意义                         |
   | ------- | -------------------- | ---------------------------- |
   | C-x b   | switch-to-buffer     | 切换到下一个缓冲区           |
   | C-x C-1 | delete-other-windows | 删除其他窗口                 |
   | C-x 2   | split-window-below   | 上下拆分窗口                 |
   | C-x o   | other-window         | 在多窗口模式下切换到其他窗口 |



10. 搜索

    | 快捷键 | 对应命令         | 意义           |
    | ------ | ---------------- | -------------- |
    | C-s    | isearch-forward  | 向前搜索关键词 |
    | C-r    | isearch-backward | 向后搜索关键词 |

    

11. 终止命令运行

    | 快捷键  | 对应命令      | 意义               |
    | ------- | ------------- | ------------------ |
    | C-g     | keyboard-quit | 退出正在执行的命令 |
    | ESC-ESC |               | 退出正在执行的命令 |

12. 切换到shell执行命令

    | 快捷键    | 对应命令 | 意义            |
    | --------- | -------- | --------------- |
    | M-x shell |          | 切换到shell缓存 |

    



### $\S$练习

1. 使用emacs编辑一个`helloWorld.c`并用`gcc`进行编译。
2. 将部分代码移入一个函数，并在main函数中调用该函数。
3. 实现上下两窗口显示代码（C-x 2），并将下面的窗口切换到shell（C-x o; M-x shell）。
4. 这样就可以实现在上面的窗口修改代码，在下面的窗口编译和调试代码了。

   

## 2. GCC的使用

在本次课中，我们学习GCC的简单命令对C程序进行编译。

首先，我们用Emacs编写两个简单的C程序：

```c
// factorial.c
#include <stdio.h>
#include <stdlib.h>
int factorial (int n) 
{
   if (n <= 1)
      return 1;
   else
      return factorial (n - 1) * n;
}
```

```c
// main.c
#include <stdio.h>
#include <stdlib.h>
int factorial (int n);
int main (int argc, char **argv)
{
   int n;
   if (argc < 2) {
      printf ("Usage: %s n\n", argv [0]);
      return -1;
   } else {
      n = atoi (argv[1]);
      printf ("Factorial of %d is %d.\n", n, factorial (n));
   }
   return 0;
}
```

我们可以运行：

```bash
gcc -c factorial.c
gcc -c main.c
gcc -o factorial factorial.o main.o
```

这样就出现了二进制文件`factorial`，我们可以运行该程序：

```bash
./factorial 5
```

或者我们也可以加入一个头文件`factorial.h`，然后同样尝试编译是否能够通过。



## 3. Makefile

我们还可以通过`Makefile`编写编译规则，然后运行：

```bash
make
```

就可以完成编译过程，然后可以用

```bash
make clean
```

清空可执行文件`factorial`和目标文件`*.o`了。

下面就是我们的`Makefile`文件：

```makefile
CC=gcc
CFLAGS=-Wall -std=gnu99

TARGET=factorial
SRCS = main.c factorial.c

OBJS = $(SRCS:.c=.o)

all: $(TARGET)

$(TARGET): $(OBJS)
   $(CC) -o $@ $^

.PHONY: clean
clean:
   rm -rf $(TARGET) $(OBJS)

%.o: %.c
   $(CC) $(CFLAGS) -o $@ -c $<
```

## 4. Linux中的常用命令

- `ls`：list the files in the directory
- `cd`：change directory
- `rm`：delete file or directory
- `mkdir`：create a directory
- `cat`：display the file content
- `less`
- `more`
- `uname`

## 实验总结

原子弹好！