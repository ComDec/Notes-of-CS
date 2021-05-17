# C静态库和动态库的制作

* Xi Wang

#### 静态库的制作

静态库必须是`lib*.a`格式的文件。使用静态库可以不再依赖于外部函数库的支持，缺点是占用体积较大。可以将静态库看作多个`[.o]`文件打包而成的集合。

比如现在有一个`main.c`主程序：

```c
#include <stdio.h>  
#include <stdlib.h>  
#include <unistd.h>  
#include "sum.h"  
#include "get.h"  
  
  
//入口主函数  
int main() {  
    int x = 10;  
    int y = 20;  
    int z = sum(&x, &y);  
    puts("This is Main");  
    printf("Z:%d\n", z);  
    x = 20;  
    z = get(&x, &y);  
    printf("Z:%d\n", z);  
    return 1;  
} 
```

为使得例子较为复杂，引入三个头部文件。

1. `val.h & val.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

#ifndef __VAL__
#define __VAL__
	int val(int *x)
#endif
```

```c
#include "val.h"
 
int val(int *x) {
	puts("This is Value==");
	printf("X:%d \n", *x);
	return 0;
}
```

2. `sum.h & sum.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

#ifndef __SUM__
#define __SUM__
int sum(int *x, int *y);
#endif
```

```c
#include "sum.h"
#include "val.h"
 
int sum(int *x, int *y) {
	val(x);
	puts("This is SUM Method!=========HDH");
	return *x + *y;
}
```

3. `get.h & get.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

#ifndef __GET__
#define __GET__
int get(int *x, int *y);
#endif
```

```c
#include "get.h"
 
int get(int *x, int *y) {
	puts("This is get");
	return (*x) * (*y);
}
```

为完成静态库的制作和使用，这里使用make完成。

```makefile
CC = gcc
PATH_LIB = /root/Desktop/bi296/lab4/lib #存放库文件
PATH_SRC = /root/Desktop/bi296/lab4/src #存放函数库源代码
PATH_BIN = /root/Desktop/bi296/lab4/bin #存放main程序源码和可执行文件
PATH_HEAD = /root/Desktop/bi296/lab4/include #存放.h头文件
NAME_LIB = Test #指定函数库的名称

all: $(PATH_SRC)/*.c 
	$(CC) -c *.c -I $(PATH_HEAD) #编译SRC中所有函数库文件为.o文件，注意指明head路径
	ar -rsv $(PATH_LIB)/lib$(NAME_LIB).a $(PATH_SRC)/*.o #将这些.o文件打包至lib中
.PHONY: link
link:
	$(CC) -o $(PATH_BIN)/main $(PATH_BIN)/main.c -L $(PATH_LIB) -l$(NAME_LIB) -I $(PATH_HEAD) #将main函数放到BIN目录下，连接文件生成二进制可执行文件
.PHONY: clean
clean:
	rm -rf $(PATH_SRC)/*.o
```

#### 动态库的制作

动态库和静态库制作流程类似，同样为上述文件，`makefile`如下：

```makefile
CC = gcc
PATH_LIB = /root/Desktop/bi296/lab4/lib
PATH_SRC = /root/Desktop/bi296/lab4/src
PATH_BIN = /root/Desktop/bi296/lab4/bin
PATH_HEAD = /root/Desktop/bi296/lab4/include
NAME_LIB = Test

all: $(PATH_SRC)/*.c
	$(CC) -fPIC -c $(PATH_SRC)/*.c -I $(PATH_HEAD)
	$(CC) -shared -o $(PATH_LIB)/lib$(NAME_LIB).so $(PATH_SRC)/*.o
.PHONY:link
link:
	$(CC) $(PATH_BIN)/main.c -o $(PATH_BIN)/program -I $(PATH_HEAD) -L $(PATH_LIB) -l$(NAME_LIB)
.PHONY:clean
clean:
	rm -rf $(PATH_SRC)/*.o $(PATH_BIN)/program
```

