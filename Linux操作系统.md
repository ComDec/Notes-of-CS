# Linux操作系统基础

[toc]

## 序章

| 项目 | 作业 | 综合项目 | 开卷考试 | Lab  | Quiz |
| ---- | ---- | -------- | -------- | :--: | :--: |
| 比例 | 30%  | 20%      | 20%      | 15%  | 15%  |

**课程技能要求**

* 对计算机的**硬件组成**有所了解

* 掌握基本的**数制**，并能灵活运用

* 学会用**Git**进行版本管理和协同开发

* 熟练**Markdown**编写笔记和课程报告

* 熟悉**Linux命令行**，懂如何写**man文档**

* 能用**Bash**编制生物信息学数据分析流程

* 熟悉Makefile，了解**并行计算和CUDA编程**

* 能**管理**Linux操作系统，熟悉系统安全准则

* **开发Python包**，用于生物信息学建模和分析



How to ask question? https://stackoverflow.com/help/how-to-ask

## 第一章 计算机软硬件基础知识

### 第一节 基础硬件介绍

1. **CPU**

![image-20210224102953357](C:\Users\18113\AppData\Roaming\Typora\typora-user-images\image-20210224102953357.png)

2. **存储器**

用于存储指令和数据，断电即无；

![image-20210224103942096](C:\Users\18113\AppData\Roaming\Typora\typora-user-images\image-20210224103942096.png)

==主存==： 可分为静态读写 (SRAM)和动态读写 (DRAM)储存；

==二级储存==： 保存两次运行之间的数据和程序；

3. **I/O设备**

键盘，显示器，鼠标等等；

### 第二节基础软件介绍

![image-20210224104826781](C:\Users\18113\AppData\Roaming\Typora\typora-user-images\image-20210224104826781.png)

1. 计算机中数据类型

* 数值型数据： 数字，矩阵，向量；
* 非数值型数据： 图像，音频；

2. 数据存储单位

* **比特位（**bit**）：二进制数据中的一位**

* 在**x86**计算机中，数据存储的基本单位是字节（**Byte**）

* **1**字节（**Byte**）= 8 bit

  **KB**（**Kilobyte**）：**2^10 = 1024 Byte**

### 第三节 计算机中的数制

* 对于b进制中的数值 $$ a_n \cdots a_1, a_0.c_1 \cdots c_m $$, 其十进制为：

$$
(a_n\cdots a_1, a_0.c_1 \cdots c_m)_b = \sum_{i=0}^n a_ib^i + \sum_{i=1}^mc_ib^{-i}
$$



* 常用的进制

![image-20210224105843106](C:\Users\18113\AppData\Roaming\Typora\typora-user-images\image-20210224105843106.png)



进制转换举例：bin->dec

![image-20210224110110795](C:\Users\18113\AppData\Roaming\Typora\typora-user-images\image-20210224110110795.png)

代码举例：任意进制转换

  输入：number 

​             进制

​             目标进制

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
 
using namespace std;
 
long  toTen(char a[], int bit)
{
    int length = strlen(a);
    int i, b=1,sum=0; //i要做数组a的下标，注意其起止范围
    for(i=length-1;i>=0;i-- )
    {
        if(a[i]>='A')
        {
            sum += (a[i]-'A'+10) *b;
            b *= bit;
        }
        else
        {
            sum += (a[i]-'0') *b;
            b *= bit;
        }
    }
    return sum;
}
 
void tentoN(long int tenn,int m){
    int r[500];
    int j=0;
    int shang=tenn;
    while(shang!=0){
        r[j++]=shang%m;
        shang=shang/m;
    }
    for(int i=j-1;i>=0;i--){
        if(r[i]>=10){
            printf("%c",r[i]-10+'A');
        }else{
            printf("%d",r[i]);
        }
    }
}
 
int main()
{
    int n;
    char a[500];
    char renyi[500];
    int m;
    long int tenn;
    scanf("%d",&n);
    scanf("%s",a);
    scanf("%d",&m);
    tenn=toTen(a,n);
    tentoN(tenn,m);
    //printf("%s",renyi);
    return 0;
}
 
```

### 第四节 二进制相关问题讨论

* 二进制的原码，反码和补码

  ![image-20210224112104482](C:\Users\18113\AppData\Roaming\Typora\typora-user-images\image-20210224112104482.png)

-121：

原码：1 1111001

反码：1 0000110

补码：1 0000111



* 二进制算术，逻辑运算

  ![image-20210224113226123](C:\Users\18113\AppData\Roaming\Typora\typora-user-images\image-20210224113226123.png)

* 二进制位运算

  ![image-20210224113709820](C:\Users\18113\AppData\Roaming\Typora\typora-user-images\image-20210224113709820.png)

* 实数的二进制表示

  ![image-20210224114220493](C:\Users\18113\AppData\Roaming\Typora\typora-user-images\image-20210224114220493.png)

## 第二章 Linux操作系统基础

### 第一节 文件系统

* 了解文件系统的概念
* 掌握标准文件目录树结构
* 掌握绝对路径和相对路径的概念
* 掌握文件权限管理

#### 2.1.1 Linux目录树

数据以文件的形式保存在磁盘分区中，而不同文件又以目录的形式分类保存。众多的目录在一起组成父子关系，如同一棵树一般。

常见的目录和用途如下表格：

| 目录   | 说明                                                         |
| ------ | ------------------------------------------------------------ |
| /bin   | 存放常用的外部命令                                           |
| /boot  | 包含计算机启动过程中所需文件，如内核，引导菜单，主磁盘映射文件 |
| /dev   | 存放设备文件目录                                             |
| /etc   | 包含系统配置文件，包含passwd，group，conf等                  |
| /lib   | 存放必须的共享库和内核模块，如动态C库linc.so.*               |
| /media | 可移动存储介质的挂载点，U盘等插入后系统自动挂载至此目录      |
| /opt   | 安装第三方机构提供的应用软件，不要手工创建                   |
| /root  | 超级用户root的家目录                                         |
| /sbin  | 包含了硬盘分区，格式化以及启动，关闭，恢复或修复系统所必须全部命令 |

| 目录         | 说明                                                  |
| ------------ | ----------------------------------------------------- |
| /sys         | 挂载sysfs类型的文件系统，其给应用程序提供统一访问接口 |
| /usr         | 存放共享的静态文件                                    |
| /usr/bin     | 存放大多数用户命令                                    |
| /usr/include | C/C++等语言的系统头文件存放地                         |
| /usr/share   | 存放与硬件架构无关的共享静态文件，如man文档           |
| /var         | 保存数据文件                                          |
| /var/log     | 保存系统运行日志文件                                  |
| /var/run     | 保存与进程执行有关的数据文件                          |
| /var/lock    | 程序运行时所需的锁文件                                |

#### 2.1.2 文件分类和权限

•**磁盘的最小存储单位**

•扇区（sector）—— 512字节

•**操作系统存取的最小单位**

•块（block）—— 4KB，格式化时设置

##### 2.1.2.1 目录和文件

* 绝对路径和相对路径

  在所有分区中仅存在一个根目录"/"，如"/ect/default", "/usr/share/doc"都是绝对路径，其起点都是根目录。

  而相对路径的起点是“./”, 相对路径和绝对路径可以互换。


在访问目录中，我们常用`cd`命令，下面的表格列举了一些实例。

| 命令                         | 说明                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| `cd /tmp`                    | 访问根目录下的tmp子目录                                      |
| `cd` or `cd ~` or `cd $HOME` | 返回家目录                                                   |
| `cd ../`                     | 返回上一级目录                                               |
| `cd ../etc/rc5.d`            | 访问父目录下etc子目录中的文件，使用相对路径方法              |
| `mkdir  /tmp/abc`            | 在tmp下创建子目录abc                                         |
| `mkdir -p a/b/c/d`           | 在当前目录下一次性创建多层目录                               |
| `mkdir -m 700 fold`          | 当前目录下创建fold目录，并授予700权限                        |
| `rm -rf fold`                | 不警告之间删除fold目录和其所有子目录                         |
| `cat >> filename`            | 创建名为filename的文件，以键盘输入为文件内容，以Ctrl+D为结束符 |
| `touch filename`             | 创建名为filename的文件，文件类型为系统默认                   |

##### 2.1.2.2 文件分类

一般使用命令`file <filename>`来判断文件类型

* 隐藏文件：文件名首字母为"."
* 设备文件：存放在/dev目录中，使用命令`mknod`创建，没有大小，附有设备号
* 管道文件：又称为命名管道，实现在同一台计算机上无关进程之间进行通信的进制；一个进程读取管道文件，另一个进程以写入的方式打开管道文件，此后两者可以互相通信。使用命令`mkfifo filename`进行创建
* 套接字文件：实现进程间通信的机制（通过TCP/IP协议栈）。和pipel不同，通信双方不一定是同一计算机。创建需要通过调用`socket`函数编程完成
* 链接文件：分为硬链接文件和符号链接文件。硬链接指向另一个文件体，符号链接指向另一个文件名

![文件链接类型](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\文件链接类型.png)

**符号链接**中，删除被链接文件后，文件内容清空，链接文件为空文件；

**硬链接**中，无论是删除被链接文件或链接文件，均不会直接删除文件内容。当被链接文件和全部链接文件被删除后，被链接文件内容才被删除；

创建链接文件的命令是：

```bash
ln -s(optional) <Linked file> <Link file>
```

举例，如`ln -s attach file`，创建一指向attach文件的符号链接文件file；去除parameter -s后即为创建硬链接。

> 符号链接可以跨磁盘分区（文件系统），也可以对目录进行符号链接。硬链接无法做到。

常见文件操作命令如下

| Command                                            | Introduction                   |
| -------------------------------------------------- | ------------------------------ |
| `cp <Original file> <Target file/content>`         | 复制源文件到新建目标文件或目录 |
| `cp -r /etc ./`                                    | 将整个etc目录复制到当前目录下  |
| `mv <Original file/content> <Target file/content>` | 将源文件移动到目标文件或目录   |
| `cat /etc/profile`                                 | 显示profile的内容              |
| `more /etc/profile`                                | 以分页形式显示profile内容      |

##### 2.1.2.3 文件权限

可以使用 `ls -la`列出当前目录下的文件具体信息

![文件属性](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\文件属性.png)

文件一共被分为0~7共八个域，每个域提示信息的内容如下

* 0：文件类型。d表示目录，-表示普通文件，l表示链接文件，b表示块设备文件，c表示字符设备，p代表管道文件，s表示套接字文件。
* 1：文件权限。r表示读入，w表示写入，x代表执行权限，-表示无权限，s|S表示权限临时切换，t|T表示任何用户都能存取文件。权限共9个字符，被分为三组，以“-”字符间隔，由左至右分别代表主人权限，组用户权限，其他用户权限。
* 2：目录中文件数或文件链接数目
* 3：文件的主人
* 4：文件的群组
* 5：文件大小
* 6：修改日期
* 7：文件或目录名

字母权限可以转换为数字权限，具体转换规则见下图。

![字母权限转换](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\字母权限转换.png)

文件权限管理可以使用如下命令

| 命令                              | 说明                                               |
| --------------------------------- | -------------------------------------------------- |
| `chmod [-R] <权限> <文件或目录>`  | 修改文件或目录的权限                               |
| `chmod 644 filename`              | 修改filename的权限为644                            |
| `chmod -R 755 ./fold`             | 将fold目录和其子目录权限修改为755                  |
| `chmod u+x filename`              | 赋予filename主人可执行权限；g表示组，o表示其他人； |
| `chmod o-w filename`              | 剥夺other的写入权限                                |
| `chown [-R] <account> <filename>` | 修改文件的主人                                     |
| `chown account:gq file`           | 将file的主人改为account，组群改为gq                |
| `chgrp [-R] <group> <filename>`   | 修改filename的组群                                 |

##### 2.1.2.4 文件的其他属性

1. **时间戳**

一个文件一共存在三个时间戳，分别为atime，mtime，ctime，具体解释如下：

- atime(access time):最近访问内容的时间
- mtime(modify time):最近修改内容的时间
- ctime(change time):最近更改文件的时间，包括文件名、大小、内容、权限、属主、属组等。

查看一个文件的这三个时间戳可以用stat命令：

```bash
root@860f3b529924:~/Desktop# stat Test2
  File: Test2
  Size: 64        	Blocks: 8          IO Block: 4096   regular file
Device: 88h/136d	Inode: 12315       Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2021-03-20 17:09:18.703369800 +0800 #atime发生变化
Modify: 2021-03-20 17:09:18.703369800 +0800
Change: 2021-03-20 17:09:18.713369800 +0800
 Birth: -
```

**查看文件可以更改文件的atime**，比如cat，more，less一个文件后，文件的atime会更新：

```bash
root@860f3b529924:~/Desktop# cat Test2
\u6211\u662f\u5218\u5fb7\u534e
\u6211\u662f\u5468\u6770\u4f26
\u6211\u662f\u6e29\u5bb6\u5b9d
\u6211\u662f\u8fc8\u514b\u5c14
root@860f3b529924:~/Desktop# stat Test2
  File: Test2
  Size: 64        	Blocks: 8          IO Block: 4096   regular file
Device: 88h/136d	Inode: 12315       Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2021-03-20 17:09:36.763369800 +0800 #see atime here
Modify: 2021-03-20 17:09:18.703369800 +0800
Change: 2021-03-20 17:09:18.713369800 +0800
 Birth: -

```

**更改文件的内容会同时更新文件的mtime和ctime**，还是上面的示例文件，改变文件内容，然后stat查看：

```bash
root@860f3b529924:~/Desktop# echo wow >> Test2
root@860f3b529924:~/Desktop# stat Test2
  File: Test2
  Size: 68        	Blocks: 8          IO Block: 4096   regular file
Device: 88h/136d	Inode: 12315       Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2021-03-20 17:09:36.763369800 +0800
Modify: 2021-03-20 17:11:03.443369800 +0800
Change: 2021-03-20 17:11:03.443369800 +0800
 Birth: -
```

可以看出文件内容更改后文件的mtime和ctime都更新了，atime保持不变。
**更改文件的名称，大小，权限等只会更新文件的ctime**，还是上面示例文件，更改下文件的文件名，然后stat查看：

```bash
root@860f3b529924:~/Desktop# mv Test2 Test2.txt
root@860f3b529924:~/Desktop# stat Test2.txt
  File: Test2.txt
  Size: 68        	Blocks: 8          IO Block: 4096   regular file
Device: 88h/136d	Inode: 12315       Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2021-03-20 17:09:36.763369800 +0800
Modify: 2021-03-20 17:11:03.443369800 +0800
Change: 2021-03-20 17:11:59.843369800 +0800
 Birth: -
```

#### 2.1.3 文件管理

##### 2.1.3.1 文件通配符

bash的匹配模式：bash依次扫描用户输入的参数，当存在未被转义或引号的字符，如"*", "?", "[...]"时，八十会匹配符合要求的文件名，并按照字母顺序排序。举例如下

```bash
rm -rf /tmp/1*2 "1*2" /etc/profile
```

这个命令将删除tmp下所有以1开头和2结尾的文件，当前目录下名为"1*2"的文件和etc下的profile文件。

除了“*”，可以匹配0或多个字符外，“?”可以匹配一个字符，"[...]"匹配其中一个字符，"[!...]"表示不匹配其中任何一个字符。括号中允许出现的表达式有以下几种：

1. 枚举表达式：直接列出需要匹配的全部字符，如[code, filename]
2. 范围表达式：[a-x]、[0-8]等。其中“-”出现在开头或结尾视为普通字符
3. 字符类表达式：[:class:] Linux中字符类有alnum(字母和数字)，alpha(字母)，blank，digit，upper，word，lower等等

举例如下：

```bash
rm -rf [a-g692W-Z]*[!jadf,8knG]
#删除a~g,6,9,2,W~Z开头的文件且不以j,a,d,f,8,k,n,G结尾的文件
#rm -rf [new file]将没有效果
```

```bash
rm -rf /tmp/[a-z]*[!0-9]
#等价于 [:upper:]*[!0-9]
#删除tmp目录下所有以小写字母开头且不是数字结尾的全部文件删除
```

##### 2.1.3.2 文件操作

已经介绍过文件和目录的创建，删除和查看。这里介绍文件列举和压缩操作。

| 命令                                        | 说明                                           |
| ------------------------------------------- | ---------------------------------------------- |
| `ls [parameters] [filename]`                | 列出文件或目录                                 |
| `ls`                                        | 列出当前目录下文件或目录                       |
| `ls -F`                                     | 添加用“/”标记目录                              |
| `ls -l`                                     | 列出当前目录下文件的详细信息                   |
| `ls -la`                                    | 同上，但也显示隐藏文件                         |
| `ls -lh`                                    | 文件大小转换成易读形式后列出                   |
| `ls -R`                                     | 递归显示下次目录中内容                         |
| `tar <parameter> <package name> <filename>` | 对目录或文件进行打包压缩或方向操作             |
| `tar -cvf p.tar /etc/*.conf`                | 将etc目录下conf结尾文件打包成p.tar存到当前目录 |
| `tar -xvf p.tar`                            | 解压p.tar文件并放入当前目录                    |
| `tar -czf f.tar.gz ./fold`                  | 打包fold文件夹为gzip格式                       |
| `tar -cjf file.tar.bz2 /etc/`               | 打包etc目录并压缩为bzip2格式                   |

解压缩补充：

| **格式**    | **压缩**                    | **解压缩**                        |
| ----------- | --------------------------- | --------------------------------- |
| file.gz     | gzip file                   | gzip –d file.gz                   |
| file.bz2    | bzip2 file                  | bzip2 –d file.bz2                 |
| file.zip    | zip file.zip file           | unzip file.zip                    |
| file.rar    | rar a file.rar file         | rar x file.rar                    |
| file.xz     | xz –z file                  | xz –d file.xz                     |
| dir.tar.gz  | tar czvf dir.tar.gz dir     | tar xzvf dir.tar.gz               |
| dir.tar.bz2 | tar cjvf dir.tar.bz2 dir    | tar xjvf dir.tar.bz2              |
| dir.tar.xz  | tar cvf dir/  xz –z dir.tar | xz –d dir.tar.xz  tar xvf dir.tar |
| dir.tar.Z   | tar cZvf dir.tar.Z dir      | tar xZvf dir.tar.Z                |

最常用的为tar命令，其相关参数如下

![image-20210403122825040](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210403122825040.png)

参考：https://www.linuxprobe.com/linux-tar.html

#### 2.1.4 新建文件系统

本节解决如何在Linux下添加新分区。包含分区，分区格式化，挂载和卸载分区。以在sdb硬盘上添加两个分区为例。

![新建文件分区操作](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\新建文件分区操作.png)

在新建文件系统时，需要指定文件系统类型。常见的类型有ext3、ext4、ext2、xfs、btrfs等。

Reference：[(15条消息) Linux文件系统类型简介及支持的文件系统汇总--Linux入门到精通系列 _maplele-CSDN博客_linux支持的文件系统类型](https://blog.csdn.net/lf8289/article/details/2146917?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-4.baidujs&dist_request_id=1328627.12295.16153814383061251&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-4.baidujs)

格式化命令也可以为：

```bash
mkfs -t <Filesystem Type> <Device Name>
```

格式化后的分区就可以挂载到根文件系统的某个空目录上，对此目录的读写操作即为对其分区的操作。

可以将分区按照一定格式加到/etc/fstab文件中，可实现开关机自动挂载和卸载。

* **拓展**

1. 查看分区文件系统类型

使用命令`df -T`,举例如下

```bash
[root@mylnx008 ~]# df -T /dev/sdb

Filesystem     Type 1K-blocks    Used Available Use% Mounted on

/dev/sdb       xfs  315467264 4356404 311110860   2% /mysql

[root@mylnx008 ~]# df -T

Filesystem     Type     1K-blocks     Used Available Use% Mounted on

/dev/sda2      xfs       30929148 22455300   8473848  73% /

devtmpfs       devtmpfs   1746644        0   1746644   0% /dev

tmpfs          tmpfs      1757220        0   1757220   0% /dev/shm

tmpfs          tmpfs      1757220    24868   1732352   2% /run

tmpfs          tmpfs      1757220        0   1757220   0% /sys/fs/cgroup

/dev/sda1      xfs         508580    63024    445556  13% /boot

/dev/sdc1      ext4     139203080  8699072 123409840   7% /mnt/resource

tmpfs          tmpfs       351448        0    351448   0% /run/user/1000

/dev/sdb       xfs      315467264  4356404 311110860   2% /mysql
```

也可以使用`parted -l`命令

```bash
[root@DB-Server ~]# parted -l

Model: ATA ST500DM002-1BD14 (scsi)

Disk /dev/sda: 500GB

Sector size (logical/physical): 512B/512B

Partition Table: msdos

 

Number  Start   End    Size   Type     File system  Flags

1      32.3kB  107MB  107MB  primary  ext3         boot

2      107MB   500GB  500GB  primary               lvm
```

文件系统信息还可以用如下命令进行查询：

```bash
[root@biocentos test]# dumpe2fs -h /dev/sda1
dumpe2fs 1.42.9 (28-Dec-2013)
Filesystem OS type:       Linux
Inode count:              3907584
Block count:              15624744
Reserved block count:     781237
Free blocks:              14583261
Free inodes:              3888234
First block:              0
Block size:               4096
…
Reserved blocks uid:      0 (user root)
Reserved blocks gid:      0 (group root)
First inode:              11
Inode size:               256
```

文件inode信息查询：

```bash
[root@biocentos ~]# stat anaconda-ks.cfg
  File: 'anaconda-ks.cfg'
  Size: 2710         Blocks: 8          IO Block: 4096   
  regular file
Device: 801h/2049d      Inode: 1577425     Links: 1
Access: (0600/-rw-------)  Uid: (0/root)   Gid: (0/root)
Access: 2015-12-15 15:54:03.000000000 +0000
Modify: 2015-12-15 15:54:03.000000000 +0000
Change: 2020-02-12 22:46:51.150143700 +0000
 Birth: -
```

其他命令：

```bash
root@860f3b529924:/# ls -i
 41662 bin      376 home      611 mnt    43015 run    43090 tmp
   215 boot   42543 lib       612 opt    43037 sbin   43091 usr
 30793 dev      608 lib64       1 proc     696 srv    98810 var
102026 etc      610 media  101998 root       1 sys
```

```bash
root@860f3b529924:/# df -i
Filesystem       Inodes  IUsed    IFree IUse% Mounted on
overlay        16777216 123407 16653809    1% /
tmpfs           1628181     16  1628165    1% /dev
tmpfs           1628181     15  1628166    1% /sys/fs/cgroup
shm             1628181      1  1628180    1% /dev/shm
/dev/sdc       16777216 123407 16653809    1% /etc/hosts
tmpfs           1628181      1  1628180    1% /proc/acpi
tmpfs           1628181      1  1628180    1% /sys/firmware
```

### 第二节 用户，组和身份认证

* 了解多用户系统的概念
* 掌握用户和组的概念，管理方法
* 了解环境变量

Linux操作系统允许多个用户同时登陆并启动多个任务。用户账户和用户组是进行身份鉴别和权限控制的基础。

#### 2.2.1 多用户系统

未启动的Linux系统称之为静态系统，其内容一般不发生变换。启动的Linux系统为动态系统，其由根分区上的文件、目录和虚拟内存中的进程组成，其内容时刻在发生变换。

![多用户系统](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\多用户系统.png)

#### 2.2.2 用户和组

##### 2.2.2.1 用户

所有使用Linux系统的人，信息必须在系统中先登记。

![用户登记信息](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\用户登记信息.png)

在Linux中创建一个新的用户需要提供如下信息。

1. 用户名：合法的账户名由字母，数字和下划线组成，长度为1~32
2. 密码：就是那个密码
3. 用户ID号：简称UID，类似于身份证号码，但允许不唯一。拥有相同UID的用户有相同的权限
4. 属组：每个用户只能归属于一个主要组群，但可以同时归属于多个附加组群。其主要用于管理同一类用户的权限
5. 家目录：用户登陆后默认进入的目录，即为`/home/<account>`
6. 登陆Shell：用户在登陆Linux过程中会自动执行一系列的程序，其中最后执行的程序为shell程序
7. 备注：对用户的描述

用户信息统一保存在/etc/passwd文件中，其格式如下：

**用户名:密码:UID:GID:备注:家目录:登陆Shell**

```bash
root@860f3b529924:/# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin #打印机服务
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
messagebus:x:101:101::/nonexistent:/usr/sbin/nologin
systemd-network:x:102:103:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin
systemd-resolve:x:103:104:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin
sshd:x:104:65534::/run/sshd:/usr/sbin/nologin
usbmux:x:105:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
pulse:x:106:107:PulseAudio daemon,,,:/var/run/pulse:/usr/sbin/nologin
rtkit:x:107:109:RealtimeKit,,,:/proc:/usr/sbin/nologin
xrdp:x:108:111::/var/run/xrdp:/usr/sbin/nologin
```

其中`/usr/sbin/nologin`表示系统拒绝登陆，所谓“无法登陆”指的仅是这个用户无法使用bash或其他shell来登陆系统而已，并不是说这个账号就无法使用系统资源。举例来说，各个系统账号中，打印作业有lp这个账号管理，www服务器有apache这个账号管理，他们都可以进行系统程序的工作，但就是无法登陆主机而已。

有时候有些服务，比如邮件服务，大部分都是用来接收主机的邮件而已，并不需要登陆。假如有账号试图连接我的主机取得shell，我们就可以拒绝。

其中`x`表示密码被加密，其存储在`/etc/shadow`中。其中信息的格式如下：

**账户：密码：上一次修改密码时间：密码有效期最小天数：密码有效期最大天数：密码修改警告期：密码非活动期：账户失效天数**

```bash
root@860f3b529924:/# cat /etc/shadow
root:$6$jvIrA9Y7$ifvUJGWBoUkC1FT0WP9AbSp2SMijwmH7Gh5uCYA7C4QfsuNT9/IBUigDgnz4iG/xjV996cNqDaX1a319Bo8KS/:18696:0:99999:7:::
daemon:*:18508:0:99999:7:::
```

在Linux系统中一共有三类用户：

1. 超级用户root：拥有最高权限，UID和GID都是0。超级用户负责管理系统，如创建用户，开关机，配置网络
2. 系统用户：用于启动服务和一些特殊权限控制。在Ubuntu下为UID小于1000的用户
3. 普通用户：有限的权限，UID大于1000

可以使用`su username`来切换用户。注意`su/su root/su -root`三者的区别。

```
su是默认切换到root用户
su只能获得root的执行权限，不能获得环境变量
而su -是切换到root并获得root的环境变量及执行权限
```

##### 2.2.2.2 组

登记一个组群需要提供三个信息：组群名、组群ID和组群用户成员。这些信息保存在`/etc/group`中。格式如下：

**组群名：密码：组号：组的用户成员**



<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\组群概念.png" alt="组群概念" style="zoom:70%;" />

0代表超级用户组群，1~999表示系统组群，1000+表示普通用户组群。

在最新的Linux中引入了sudo组，其成员可以用sudo命令执行root下的权限命令。

#### 2.2.3 用户和组管理

用户和组的管理包括创建，删除，修改属性、密码等等。可以使用命令来进行管理，格式如下：

**<命令><选项><目标>**

文件`/etc/login.defs`定义了组群和用户创建时的默认属性，其主要参数如下。

| 参数          | 默认值 |
| ------------- | ------ |
| PASS_MAX_DAYS | 99999  |
| PASS_WARN_AGE | 7      |
| PASS_MIN_LEN  | 5      |
| UID_MIN       | 1000   |

##### 2.2.3.1 组管理

###### 1.组创建

* 创建组群：`groupadd groupname`
* 创建指定GID的群组：`groupadd -g 500 groupname`
* 创建别名组群：`groupadd -g 0 -o groupname`

###### 2.组删除

`groupdel groupname`

###### 3.组群属性修改

* 修改组群GID：`groupmod -g 500 group`
* 修改组群名：`groupmod -n newname oldgroup`

###### 4.组群信息查询

查看文件`/etc/group`即可。

##### 2.2.3.2 用户管理

###### 1.用户创建

| 命令                                                         | 说明                                                     |
| ------------------------------------------------------------ | -------------------------------------------------------- |
| `useradd name`                                               | 创建用户name，使用默认配置                               |
| `useradd -u 1000 -d /home/content -g group1 -G attachgroups -s /bin/bash` `name` | 创建用户name，制定配置                                   |
| `useradd -u 1000 -o name1`                                   | 创建name用户的别名用户name1，注意需要相同的UID           |
| `useradd -r -s /bin/bash name`                               | 添加系统用户name，指定shell不指定家目录                  |
| `useradd  -m -d /home/name -s /bin/bash -G sudo name`        | 添加家目录(-m)为name(-d)的用户name，归属附加组群sudo(-G) |

###### 2.用户删除

| 命令                 | 说明                         |
| -------------------- | ---------------------------- |
| `userdel name`       | 删除用户name                 |
| `userdel -r name`    | 删除用户name，同时删除家目录 |
| `userdel -f -r name` | 强制删除用户name             |

###### 3.用户属性修改

| 命令                                      | 说明                |
| ----------------------------------------- | ------------------- |
| `usermod -d /home/name -s /bin/bash name` | 修改home目录和shell |
| `usermod -g group -G attachgroup name`    | 修改主要和附加组群  |
| `usermod -l newname name`                 | 修改用户名          |
| `usermod -u 1200 name`                    | 修改UID             |

###### 4.密码管理和信息查询

| 命令             | 说明                              |
| ---------------- | --------------------------------- |
| `passwd -l name` | 锁定用户                          |
| `passwd -u name` | 解锁用户                          |
| `passwd name`    | 修改用户密码                      |
| `passwd -d name` | 删除用户密码                      |
| `id name`        | 查询用户的UID和GID                |
| `finger name`    | 查询用户的家目录，Shell和登陆时间 |

#### 2.2.4 用户登陆和环境变量

##### 2.2.4.1 用户登陆过程

![用户登陆过程](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\用户登陆过程.png)

用户成功登陆后自动执行登陆Shell(/bin/bash)，此后命令行提示#或%，前者为超级用户，后者为普通用户，并等待用户输入命令。

##### 2.2.4.2 环境变量

用户登陆Linux系统后，系统会自动配置好工作环境--语言，家目录，邮箱目录，命令搜索路径，终端类型，命令提示符合等等。用户的工作环境则由一系列的环境变量定义，其格式为经典的**名值对**类型。

环境变量名由字母数字，下划线构成，**一般不使用小写字母**。环境变量值可以由任意字符组成，**若包含空格，则需要用引号约束**。

常见的用户环境变量如下：

| 环境变量                  | 说明                |
| ------------------------- | ------------------- |
| LANG=zh_CN.UTF-8          | 语言设定为中文UTF-8 |
| HOME=/home/name           | 定义家目录          |
| LOGNAME=name              | 定义用户名          |
| PATH=/usr/local/bin:/bin: | 定义命令搜索路径    |
| SHELL=/bin/bash           | 定义用户登陆Shell   |
| PWD                       | 追踪用户当前目录    |

显示用户环境变量采用命令`env`或`echo $环境变量名`。前者为全部显示，后者为显示特定的环境变量值。

```bash
LESSCLOSE=/usr/bin/lesspipe %s %s
LANG=zh_CN.UTF-8
TZ=Asia/Shanghai
DISPLAY=:0.0
VNCDESKTOP=860f3b529924:0 ()
HOSTNAME=860f3b529924
COLORTERM=truecolor
SSH_AUTH_SOCK=/tmp/ssh-9JSDbN06R87t/agent.31
QT4_IM_MODULE=xim
PWD=/root/Desktop
HOME=/root
SSH_AGENT_PID=93
QT_ACCESSIBILITY=1
DEBIAN_FRONTEND=noninteractive
VTE_VERSION=5202
TERM=xterm-256color
QT_IM_MODULE=xim
XMODIFIERS=@im=ibus
SHLVL=2
LANGUAGE=
WINDOWID=37748739
PASSWD=123456
DBUS_SESSION_BUS_ADDRESS=unix:abstract=/tmp/dbus-3sIlpXpG1E,guid=6fe0a7ab99a79943e76cf026604a1090
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
SIZE=1600x840
SESSION_MANAGER=local/860f3b529924:@/tmp/.ICE-unix/117,unix/860f3b529924:/tmp/.ICE-unix/117
LESSOPEN=| /usr/bin/lesspipe %s
GTK_IM_MODULE=ibus
_=/usr/bin/env
```

修改环境变量使用`export`命令，示例如下

```bash
#export 变量名=值
export PATH=$PATH:$HOME
```

其中`$XXX`是引用变量的值。删除环境变量使用`unset`命令，如`unset PATH`。

除环境变量外还存在Shell变量，用户环境变量是Shell变量的子集。Shell变量使用`set`命令定义，使用`unset`删除。

![Shell变量](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\Shell变量.png)

值得注意的是，export和set命令定义的环境变量是临时性的，在用户注销后环境变量消失。若希望变量永久生效，需要将其命令定义添加到登陆自动执行脚本上。

通常选择加在`/etc/profile`或`~/.bashrc`的末尾。`~`表示用户家目录。如：

`echo "export HISTSIZE=5000" >> /etc/profile`

参考：https://blog.csdn.net/xyw591238/article/details/51770247?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_baidulandingword-1&spm=1001.2101.3001.4242

## 第三章 基本命令和Shell的开发

### 第一节 基本命令语法和类型

#### 3.1.1 命令类型和语法

Linux的命令分为两类，一类是外部命令。外部命令对应磁盘上一二进制文件，用户在命令行输入命令后，由bash匹配参数，再去执行二进制文件。另外一类是内部命令。内部命令直接对应bash程序中的代码，可以理解为内置函数。其执行效率显然更高。也有一类如`test`命令，即为外部也为内部命令。

Linux命令的语法格式为：

```
<命令> [<参数>][<目标>]
#命令为必需，而参数和目标可以省略。
```

#### 3.1.2 在线帮助文档

使用`man`可以获取外部命令的帮助信息，而使用`help`可以获取内部命令的帮助信息。具体用法如下：

![man和help的用法](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\man和help的用法.png)

### 第二节 Bash快捷键，重定向和管道

#### 3.2.1 Bash快捷键

![bash快捷键](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\bash快捷键.png)

与历史命令有关的两个Shell变量是`HISTFILE`和`HISTFILESIZE`。前者定义HIST命令的存储位置，后者记录HIST命令的个数。其默认值分别是`HISTFILE=~/.bash_history`, `HISTFILESIZE=1000`。可以使用如下命令将HISTFILESIZE改为5000.

```bash
echo "export HISTFILESIZE=5000" >>/etc/profile
```

#### 3.2.2 命令重定向

对于Linux中的命令，都可以看作是一段代码。有输入输出。标准输入默认为键盘，而输出又分为错误输出和标准输出，输出设备是屏幕。

![Linux命令执行过程](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\Linux命令执行过程.png)

Linux执行命令的过程如下：

1. bash读取命令行上的命令

2. bash拓展命令中通配符参数

3. 复制**文件描述符**：

   1) 0号文件描述符，标准输入，默认指向/dev/stdin

   2) 1号文件描述符，标准输出，默认指向/dev/stdout

   3) 2号文件描述符，标准错误输出，默认指向/dev/stderr

   > 可以使用重定向方法，让文件指向不同地方。

4. 在用户变量PATH定义的目录中搜索命令对于的二进制可执行文件
5. 将二进制程序的退出状态保存到特殊变量`？`中
6. 显示命令行状态并等待下一个命令

0、1、2号文件的指向允许通过命令行上的特殊参数来改变，即输入输出重定向。常用的元字符有`< > & -`。

| 重定向模式        | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| `[n]>target`      | n号文件描述符重定向至目标（**先清空**）                      |
| `[n]>>target`     | n号文件描述符重定向至目标（**追加至末尾**）                  |
| `>>/tmp/file.txt` | 输出文件追加至`file.txt`的末尾                               |
| `>&Target.txt`    | 标准输出和错误输出都定向至`Target.txt`                       |
| `2>&1`            | 重定向符后为纯数字时，bash视为文件操作符。此中复制到1号文件  |
| `>file.log 2>&1`  | 将标准输出和错误输出定向到`file.log`。先将1号文件复制到2号，然后1号定向至file |

对于输入重定向，其具体操作命令和示例如下：

![输入重定向](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\输入重定向.png)

示例：

在使用`gcc`编译C语言程序时，会产生大量的错误信息，为方便查看可以重定向到文件

```bash
gcc -o program program.c 1>gcc.txt 2>gcc.err
```

#### 3.2.3 其他元字符

##### 3.2.3.1 管道

使用`|`或`|&`隔开的两个命令之间形成一个管道。左边命令的标准输出或错误输出作为右边命令的标准输入。示例如下：

```bash
make |& tee make.err
dmesg | more
```

##### 3.2.3.2 命令序列

使用`；& && ||`连接的命令为一个命令序列。

使用"`;`"连接的命令从左至右依次执行，最终执行命令的返回状态即为整个命令序列返回的状态。

在命令末尾追加“`&`”代表命令在后台(子bash)中执行。示例如下：

```bash
tar -jcf /tmp/etc.tar.bz2 /etc&
```

也可以使用Ctrl+Z来切换任务至后台。在命令执行结束前使用Ctrl+Z后，命令被切换到后台暂停执行。再执行命令`bg 1`即可让后台命令继续执行；`bg`后为任务号，可以通过`jobs`显示。

使用"`&&`"连接的命令序列是个布尔型表达式。左述任务成功执行后右侧命令再继续执行。

```bash
mkdir /tmp/abc && cd /tmp/abc
```

使用“`||`”连接的命令为选择式。**左侧任务执行失败后再执行右侧任务**。

```bash
cd /tmp/abc || mkdir /tmp/abc
```

综合运用：

```bash
tar -cjf /tmp/etc.tar.bz2 /etc 2&>1 >/dev/null && echo "Success!" || echo "Error!"
```

左侧`tar`命令成功执行后屏幕回显Success反之为Error。这里使用`/dev/null`对输出进行重定向，确保屏幕上没有其他输出。

### 第三节 常用命令

#### 3.3.1 关机和重启

![关机和重启命令](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\关机和重启命令.png)

#### 3.3.2 bash内部命令

![bash内部命令](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\bash内部命令.png)

#### 3.3.3系统信息命令

![系统信息命令](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\系统信息命令.png)

#### 3.3.4文件操作命令

![文件操作命令1](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\文件操作命令1.png)

![文件操作命令2](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\文件操作命令2.png)

#### 3.3.5进程和任务管理命令

进程有几种状态：

•**运行中状态**（R, **task_running**）

•**可中断的睡眠状态（**S, **task_sleep_but_interruptible**）

•**不可中断的睡眠状态（**D, **task_dormant_and_interruptible**）

•**暂停状态或跟踪状态（**T, **task_trace**）

•**退出**-**僵尸态（**Z, **exit_zombie**）

•**退出**-**即将销毁状态（**X, **exit_dead**）

![进程和任务管理1](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\进程和任务管理1.png)

![进程和任务管理2](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\进程和任务管理2.png)

进程命令补充：

| Command              | Instruments              |
| -------------------- | ------------------------ |
| `ps`                 | 输出当前进程快照         |
| `pstree`             | 输出当前进程树           |
| `top`                | 动态显示当前进程运行情况 |
| `nice -10 <command>` | 改变优先级运行程序       |

Kill命令补充：

| **编号** | **信号** | **意义**                         |
| -------- | -------- | -------------------------------- |
| 1        | SIGHUP   | 用户终端连接（正常或非正常）终止 |
| 2        | SIGINT   | 程序终止（CTRL+C）               |
| 3        | SIGQUIT  | 程序错误终止（CTRL+\）           |
| 4        | SIGILL   | 非法指令错误：可执行文件错误     |
| 5        | SIGTRAP  | 断点指令或其他trap指令           |
| 8        | SIGFPE   | 算术运算错误                     |
| 9        | SIGKILL  | 结束程序运行，不能被阻塞、忽略   |
| 11       | SIGSEGV  | 内存读/写访问权限错误            |
| 15       | SIGTERM  | 程序结束信号，能被阻塞和处理     |
| 17       | SIGCHLD  | 子进程结束后发送给父进程的信号   |

#### 3.3.6网络相关命令

![网络相关命令](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\网络相关命令.png)

### 第四节bash编程基础

#### 3.4.1 第一个Shell程序

```bash
echo "Hello World!"
```

默认创建文件后不具备执行权限，需要执行`chmod u+x filename.bash`命令。

#### 3.4.2 脚本语言介绍

用户拥有不用的Shell解释器，包括bash、sh、csh、tcsh、ksh等，不同解释器的语法存在细微差别。bash是Linux系统默认的用户登录Shell。通常需要在程序首行指明Shell解释器。

```bash
#!/bin/bash
```

可以使用`echo $Shell`查询自己的登陆Shell。

#### 3.4.3 Shell编程的结构和基本语法

![Shell程序结构](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\Shell程序结构.png)

#### 3.4.4 变量

##### 3.4.4.1普通变量

Shell的变量不存在类型，在赋值的同时即已然定义变量，由赋值的类型决定变量的类型。可以使用`${var}`来引用变量。示例如下：

![普通变量的引用](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\普通变量的引用.png)

##### 3.4.4.2数组变量

bash支持一维数组变量，数组下标从0开始。定义数组的语法如下：

```bash
Listname = (Value1 Value2 ...)
```

可以使用如下命令调用和修改数组元素

```bash
echo ${Listname[0]} #调用下标为0的元素
echo ${Listname[*]} #返回整个数组
Listname[0] = New_Value #修改数组中指定元素的值
```

若想实现数组间之间赋值，可以采用下面的方法

```bash
Newname = ("${Listname[*]}")
```

这样新定义了一个数组`Newname`，其中的元素和`Listname`完全相同。

##### 3.4.4.3特殊变量

特殊变量由bash自动定义和赋值，用户不可修改。常见的特殊变量如下。

| Parameters  | Instruments                                      |
| ----------- | ------------------------------------------------ |
| `$$`        | 当前进程的进程号(PID)                            |
| `$?`        | 表示前一个命令的执行情况。0表示成功执行，1为失败 |
| `$#`        | 命令行参数的数量。不包含命令本身                 |
| `$0`        | 代表命令本身                                     |
| `$1, ${10}` | 代表具体的命令行参数                             |
| `$*`        | 保存全部的命令行参数                             |

#### 3.4.5 用户参数输入和判断

**测试语句格式**：`[ 条件表达式 ]`

> 注意括号两侧都有空格！

按照测试内容来分，条件测试语句可以分为四种：

* 文件测试语句
* 逻辑测试语句
* 整数值比较语句
* 字符串比较语句

文件测试语句是用于判断文件是否存在或权限是否满足，具体参数如下：

![image-20210403125231153](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210403125231153.png)

逻辑语句用于对测试结果进行逻辑分析，不同的测试结果将返回不同的效果。

| 逻辑符 | 说明                       |
| ------ | -------------------------- |
| `&&`   | 前述命令成功执行和执行后续 |
| `||`   | 前述命令未执行后执行后续   |
| `!`    | 逻辑取反                   |

示例：

````bash
[ ! $USER = root ] && echo "user" || echo "root"
````

作用：若为root用户则返回root，否则返回user。

整数比较运算符仅仅可以对数字操作，不能将数字和字符串，文件等内容一起操作。由于大于小于号，等号和重定向符冲突，故整数大小的比较要使用特定的运算符。

![image-20210403130421656](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210403130421656.png)

字符串比较语句用于判断字符串是否为空值，或两个字符串是否相同。常常用于判断某个变量是否未被定义，常见的运算符如下：

![image-20210403130913182](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210403130913182.png)

示例：

```bash
if [ -z $String]
then
echo "Not define"
fi
```

#### 3.4.6 控制语句

##### 3.4.6.1顺序结构体

顺序结构体包含一系列的命令，从上而下执行。和C系语言不同，不需要分隔符。

```bash
#!/bin/bash
cd ~
mkdir test_1
cd /opt/abc
tar -jcf data.dat.bz2 /home/test_1
```

##### 3.4.6.2分支结构体

* 判断表达式的真假

```bash
test experssion #两种均可
[experssion]
```

若为真则返回“0”，反之返回“1”。下面列举出逻辑判断的例子。

![逻辑判断举例](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\逻辑判断举例.png)

举例：

```bash
#如果/opt/yield是目录，则进入。
[-d /opt/yield] && cd /opt/yield
```

* if分支和语法

```bash
if list;
  then
elif list1;
      then
   else
fi
```

值得注意的这里存在大量的“`；`”和“`fi`”终止符。

举例：

```bash
#!/bin/bash
if test -d /usr/local/a
then
   cd /usr/local/a    #若存在目录则进入
  elif [-f /usr/local/a]; then  #若存在同名文件则删除
   rm /usr/local/a
  else                  #若不存在，则创建目录
   mkdir /usr/local/a
fi
```

* case分支语句

```bash
case WORD in 
     [PATTEN1]) Command1 ;;
     [PATTEN2]) Command2 ;;
esac
     
```

举例：

```bash
#!/bin/bash
case $1 in

[0-9]) echo "Digital" ;;    #若为0~9，则返回digital
[a-z]) echo "Lower Char" ;;
[A-Z]) echo "Upper Char" ;;
"Good") echo "OK!" ;;      #若命令行参数为Good，则返回OK
*) cd /tmp                #顺序执行，若上述均未执行，则执行后续多行指令
echo "What are you saying?";;
esac
```

##### 3.4.6.3循环结构体

* for循环语句

在bash中，for循环的语法为：

```bash
for name [[ in [word...]] ; ] do list; done
for ((expr1; expr2; expr3)); do list; done
```

示例如下：

```bash
#Echo i
#!/bin/bash
for i in 1 2 3 4 5
do
  echo -n "Echo for $i times"
done
#Alter the expanding name
for file in * do
  [-f ${file}] $$ mv ${file} ${file}.old
  [-d ${file}] $$ break
done
```

* while循环

```bash
while list;
 do list;
done
```

注意while判断条件和语句后都存在分号。示例如下：

```bash
#!/bin/bash
cat /etc/passwd | while read line #Read via pipe
do
  user = 'awk - F:' {print $1}'<<< ${line}' #Set : as seperator, read the first word only.
  echo "Account: ${user}"
done
```

* select语句语法

```bash
select name [in word]; do list; done
```

目前只有ksh和bash支持此语句，其执行顺序如下。

1. 拓展word中的通配符
2. 以Shell变量的IFS值作为分隔符切割word成多个项目，每个项目添加序号。以列的形式显示到标准错误输出文件描述符。
3. 显示Shell变量PS3的值，并等待用户输入
4. 读取用户输入，若输入是“项”前序号，那么“项”被赋予name，反之name被赋予null
5. 执行List，遇break则退出，或从第一步开始执行

Select的特性让其十分适合用于开发字符界面菜单，示例如下：

```bash
#!/bin/bash
PS3="Please select your target:"
menus = "com|net|org|edu|quit"
IFS="|"
select item in ${menus}
do
 case ${item} in
     com) echo "Apply com domain";;
     net) echo "Apply net domain";;
     org) echo "Apply org domain";;
     edu) echo "Apply edu domain";;
     quit) break;;
  esac
done
```

* 循环控制语句

break和continue是最常用的循环控制语句，其用法和C系编程语言相同。

示例：

```bash
#!/bin/bash
read -p "Enter your score (0-100):" GRADE #这里用GRADE接受输入参数
if　[ $GRADE -ge 85 ] && [ $GRADE -le 100 ] ; then
echo "Excellent!"
elif [ $GRADE -ge 70 ] && [ $GRADE -le 84 ]; then
echo "Pass!"
else 
echo "Failed!"
fi         
```

### 第五节 Shell调试

Shell程序属于解释性语言，最好的调试方法即为执行`-x`程序，如下：

```bash
bash -x file.sh
```

如此执行会将执行到的语句全部显示，若程序很长，可在调试的程序块前后添加调试标记，如此调试时仅打印调试块中的执行路径。

```bash
...
set -v
Your program
set +v
...
```

### 第六节 Shell开发实战

#### 3.4.6.1文件读取

文件逐行读取主要有以下三种方式。

```bash
#!/bin/bash
while read line
do
echo $line
done < filename(待读取的文件)
```

```bash
#!/bin/bash
cat filename(待读取的文件) | while read line
do
echo $line
done
```

```bash
for line in `cat filename(待读取的文件)`
do
echo $line
done
```

参考：https://www.jb51.net/article/59041.htm

#### 3.4.6.2 遍历目录文件名存入数组

```bash
#!/bin/bash
j=0
k=0

for i in `ls $1`
do
	d1[j]=$i
	((j++))
done

for i in `ls $2`
do
	d2[k]=$i
	((k++))
done
```

#### 3.4.6.3 打分系统

给定一个分数，根据输入的成绩，输出其成绩等级（90+: A; 70-90: B; 60-70: C; 60-: F）

```
user@host ~ $ ./scorelev.sh 98
A
user@host ~ $ ./scorelev.sh 45
F
```

```bash
#!/bin/bash


while (true); do
read -p "Input your score: " score
if [[ $score =~ ^[0-9]+ ]]; then
	: 
else
	echo "Error! Retry your input!"
	continue
fi

if [ $score -gt 100 ] || [ $score -lt 0 ] ; then
	echo "Error! Retry your input!"
	else
	break
fi
done

if [ $score -ge 90 ]; then
	echo "A!"
elif [ $score -ge 80 ]; then
	echo "B!"
elif [ $score -ge 70 ]; then
	echo "C!"
elif [ $score -ge 60 ]; then
	echo "D!"
else
	echo "You Failed!"
fi
```

本代码实现了输入检查，输出结果如下：

![image-20210428184645001](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210428184645001.png)

#### 3.4.6.4 文件筛选和打包

写一个bash脚本，根据输入的目录名，对目录下的__普通文件__进行备份、打包

```bash
#!/bin/bash
#新建temp文件夹用于保存临时文件
[ ! -d ./temp ] && mkdir temp

d=`ls $1`
#这里注意必须用``将目录名保护否则无法赋值

for file in $d
do
  if test -f $1"/"$file; then
	#echo "Find common file: "$file
	cp $1"/"$file ./temp
  fi
done

tar -czvf backup.tgz ./temp
#rm -rf ./temp
```

![image-20210428192825033](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210428192825033.png)

#### 3.4.6.5 日期格式化

课上我们介绍过如何去处理一个字符串，获取其中特定的子串，这里需要大家写
一个函数，解析输入的格式为"12/28/2012"这种日期的字符串，分别获取年份、
月份、日，并将其处理成另外一种写法进行输出。

```bash
#!/bin/bash
##### 利用数组 #####
# 日期变量赋值
read -p "Input your data as mouth/day/year: " day
# 设定分隔符
IFS="/"
# 声明数组变量
declare -a dd
dd=($day)
# 用分隔符转换为数组
declare -p dd
# 输出年
for i in 0 1 2
do
if [[ ! ${dd[i]} =~ [0-9]+ ]];then
	exit "Error!"
fi
done
if [ ${dd[0]} -gt 12 ] || [ ${dd[0]} -lt 1 ]; then
	echo "Error! Input a correct mouth!"
	elif [ ${dd[1]} -gt 31 ] || [ ${dd[1]} -lt 1 ]; then
	echo "Error! Input a correct day!"
	else
      echo "Year: ${dd[2]}"
      echo "Month: ${dd[0]}"
      echo "Day: ${dd[1]}"
fi
```

![image-20210428194905741](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210428194905741.png)

本程序可以实现简单的输入检测，但尚未引入闰年检测。

#### 3.4.6.6 文本信息提取

针对`/etc/passwd`文件，写出所有默认登录shell为`/bin/bash`的
用户，并输出下面的语句。

```
root is bash user
bio is bash user
bioinfo is bash user
```

```bash
#!/bin/bash

declare -a nn
IFS=":"

cat /etc/passwd | while read name
do
	nn=($name)
	declare -p nn 
	if [[ ${nn[6]} =~ /bin/bash ]]; then
		echo "${nn[0]} is bash user"
	fi
done
```

![image-20210428201752748](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210428201752748.png)

执行后发现大量命令回显，影响可读性，因此将`declare`重定向到黑洞中

````bash
#!/bin/bash

declare -a nn
IFS=":"

cat /etc/passwd | while read name
do
	nn=($name)
	declare -p nn > /dev/null
	if [[ ${nn[6]} =~ /bin/bash ]]; then
		echo "${nn[0]} is bash user"
	fi
done
```

![image-20210429104332730](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210429104332730.png)

#### 3.4.6.7 最大值

从键盘输入三个数a、b、c，并输出其中的最大值和最小值，可以在一行上输入，
也可以分别获取。
  （提示：注意read的用法，或者直接在命令行输入获取）

```bash
max() {
max=$1
  if [ $2 -ge $1 ];then
	max=$2
  fi
   
  if [ $3 -ge $1 ];then
	max=$3
  fi
  
  echo $max

}

max $1 $2 $3
```

![image-20210428202840109](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210428202840109.png)

#### 3.4.6.8 阶乘

写一个函数，判断输入的值是否为整数，并计算给定整数阶乘（factorial）

```bash
#!/bin/bash
#shell程序计算n的阶乘
#1.从命令行接收参数n;
#2.在程序开始后立即判断n的合法性，即是否有参数，若有是否为正整数，若非法请给错误提示。
#3.最后出计算的结果

num=$1
expr $num + 1 &>/dev/null
[ $? -ne 0 ] && echo "please input a number." && exit 2
[ $# -ne 1 ] && echo 'Usage:$0 number' && exit 1
[ $num -le 0 ] && echo "please input a number bigger than 0" && exit 3
s=1
for i in `seq 1 $num`
do 

     s=$(($s*$i))

done
echo $s
```

![image-20210428203659835](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210428203659835.png)

#### 3.4.6.9 日期计算

计算1970年1月1日0时0分至当前时间的长度，以秒为单位。计算任意给定两
时间之间的天数。（提示：date命令获取当前时间）

```bash
#!/bin/bash

date "+%m/%d/%Y/%H/%M/%S" | while read d 
do

echo "Current Time:"
date "+%Y/%m/%d %H:%M:%S"

IFS="/"
declare -a dd 
dd=($d)
declare -p dd > /dev/null

year=${dd[2]}
mon=${dd[0]}
day=${dd[1]}
((sec=dd[3]*3600+dd[4]*60+dd[5]))

((mon=mon-2))

if [ $mon -le 0 ]; then
	((mon=mon+12))
	((year=year-1))
fi

((Y=year/4-year/100+year/400))
((Z=367*mon/12))
((W=year*365+day))
((X=Y+Z+W-719499))

((Total=X*86400+sec))

echo "Have passed $Total seconds"

done
```

这里使用了Gauss算法来进行日期计算。

![image-20210428211717445](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210428211717445.png)

#### 3.4.6.10 补丁制作

1. 有两个目录，其中一个目录是另外一个的拷贝（备份），根据其所有对应文件
   的差别对其制作出补丁（patch），全部输出到一个补丁文件`%DATE.patch`中，
   其中%DATE为当前的日期，比如
   20130327.patch

```bash
#!/bin/bash

cur=$(eval pwd)

date "+%Y%m%d" | while read time

do

	#文件补丁使用 -u 参数
	diff -ruN $cur/$1/ $cur/$2/ >> ${time}.patch
done
```

运行结果：

![image-20210429104018574](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210429104018574.png)

#### 3.4.6.11 文本大小写转化

假设现在有一个文本文件，能不能用一个bash脚本，将其中的所有大写字母转
换为小写字母，并将其写回该文件？

```bash
#!/bin/bash
sed -i 's/[A-Z]/\l&/g' test.txt
#\l是将下一个字符变为小写，\u是将下一个字符变为大写，&是代替已匹配到内容
```

![image-20210429101621829](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210429101621829.png)

#### 3.4.6.11 后缀名修改

将目录下的所有.c文件更名为.h文件。

```bash
#!/bin/bash
#指定新旧后缀名
oldext="c"
newext="h"
#获取当前目录位置
dir=$(eval pwd)
#Gerp到满足旧后缀的文件
for file in $(ls $dir | grep .$oldext)
        do
        #利用cut截取文件名
        name=$(ls $file | cut -d. -f1)
        mv $file ${name}.$newext
        done
```

这个方法存在一个问题，会将类似于clc，sh后缀的文件也改名，如下：

![image-20210428214224239](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210428214224239.png)

因此对`bash`进行修改如下：

```bash
#!/bin/bash
SUFFIX_SRC=$1
SUFFIX_DST=$2
for i in *.$SUFFIX_SRC
do
    if [ -e $i ]; then
        echo "mv $i to `basename $i .$SUFFIX_SRC`.$SUFFIX_DST"
        mv $i `basename $i .$SUFFIX_SRC`.$SUFFIX_DST
    else
        echo "file does not exist."
        exit -1
    fi
 
done
```

![image-20210428214026072](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210428214026072.png)

#### 3.4.6.12 平均值和SD计算

写一个函数sd.sh，计算下面文件中的数值的平均值和标准偏差。

```
# test.dat
12.33
11.67
12.16
13.01
12.56
```

下面是运行示例：

```
user@host ~$ ./sd.sh test.dat
Number of data points in "test.dat" = 5
Arithmetic mean (mu) = 12.1234
Standard Deviation (sigma) = .3456 
```

```bash
#!/bin/bash
cat test.dat > temp.dat
 awk '{x[NR]=$0; s+=$0; n++} END{a=s/n; for (i in x){ss += (x[i]-a)^2} sd = sqrt(ss/n); print "SD = "sd,"Average = "a}' temp.dat
```

![image-20210428215824563](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210428215824563.png)

#### 3.4.6.13 密码检查

写一个脚本，检查你输入的密码是“强（strong）”、“中等强（medium）”还是“弱（weak）”口令，根据下面的标准：

- 长度至少为8个字符；
- 至少包含一个大写字母，一个小写字母和一个数字
- 至少包含一个特殊字符：@, #, $, %, &, *, +, -, =

三项满足的是强口令，满足两项的为中等强度口令，否则为弱口令，分别写出对应实例进行验证。

```bash
#!/bin/bash

STAND=0

#定义一字符串存储特殊字符
str="@,#,\$,%,&,+,-,="

#将特殊字符字符串转换成数组
declare -a dd
IFS=","
dd=($str)
declare -p dd > /dev/null

#输入密码
read -p "Input Your Password: " pas

#获取密码长度
len=$(expr length ${pas})


#遍历特殊字符串数组，寻找密码中是否存在该字符
for((i=0;i<${#dd[@]};i++))
do
	#如果成功匹配，STAND变量自加1
	if [[ $pas =~ "${dd[i]}" ]];then
		((STAND=STAND+1))
	fi

done
	#由于字符串数组只能以\*的形式存储字符*，因此单独检验该字符
	if [ $STAND -ne 1 ] & [[ $pas =~ \* ]];then
		((STAND=STAND+1))
	fi
	
#检查，防止第一轮检查中STAND变量超过1
if [ $STAND -gt 1 ];then
	((STAND=STAND-1))
fi

#echo $len

#检查长度
if [ $len -ge 8 ];then
	((STAND=STAND+1))
fi

#检查大小写字符和数字
if [[ $pas =~ [A-Z] ]] && [[ $pas =~ [a-z] ]] && [[ $pas =~ [0-9] ]];then
	((STAND=STAND+1))
fi

#如果满足两个以上条件则为好密码，三个以上为强密码，其余为弱密码
if [ $STAND -eq 3 ];then
	echo "Strong Password!"
elif [ $STAND -eq 2 ];then
	echo "Good Password!"
elif [ $STAND -le 1 ];then
	echo "Bad Password!"
fi
```

运行结果：

![image-20210428230131827](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210428230131827.png)

## 第四章 Vi/Vim快速入门

### 第一节 Vim简介

Vim是目前使用最多，最强大的Linux下文本编辑器。其几乎可以编辑当前所有的文件，而且全程在命令行下操作。

![vim的工作模式](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\vim的工作模式.png)

在终端输入vim命令，界面转换为vim软件。在这个界面下，按下插入模式的任何模式，如"i,a,o..."等vim进入插入模式，在左下角将出现提示。

![vim1](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\vim1.png)

在插入模式下可以使用"Esc"返回命令模式。在命令模式下按下“：”可以进入底行模式。在底行模式下可以执行底行命令。

### 第二节 Vim的三种模式

#### 4.2.1 底行模式

##### 4.2.1.1 设置vim工作环境

![vim工作环境](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\vim工作环境.png)

我们可以每次进入底行模式设定如上模式。初次之外，我们还可以提前在`~/.vimrc`文件中设定好工作环境，如此每次启动vim，此文件中命令就会被自动执行。

##### 4.2.1.2 存盘和退出

![vim存盘和退出](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\vim存盘和退出.png)

#### 4.2.2 命令模式

##### 4.2.2.1 插入模式

在命令模式下，可以按下键盘上如下按键，选择不同的插入模式。

![vim插入模式](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\vim插入模式.png)

##### 4.2.2.2 光标移动

![vim光标移动](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\vim光标移动.png)

##### 4.2.2.3 查找和替换

![vim查找和替换](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\vim查找和替换.png)

##### 4.2.2.4 复制粘贴、删除

![vim复制粘贴](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\vim复制粘贴.png)

##### 4.2.2.5 编辑

![vim编辑1](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\vim编辑1.png)

![vim编辑2](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\vim编辑2.png)

### 第三节 多文件编辑

一个物理屏可以划分成多个编辑页，而单个编辑页又可以分成多个编辑窗口，每个窗口都可以编辑独立的文件。可以使用`Ctrl+W`, `w`在同一个页面的不同窗口切换，或者使用`gt`切换页。常用的命令如下：

![vim多文件编辑](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\vim多文件编辑.png)

## 第五章 Linux下高级编程方法

## 第六章 Linux下常用工具详解

### 文件下载工具：Wget

wget命令用来从指定的URL下载文件。wget非常稳定，它在带宽很窄的情况下和不稳定网络中有很强的适应性，如果是由于网络的原因下载失败，wget会不断的尝试，直到整个文件下载完毕。如果是服务器打断下载过程，它会再次联到服务器上从停止的地方继续下载。这对从那些限定了链接时间的服务器上下载大文件非常有用。

* 语法

```bash
wget <option> <parameter>
```

* 选项

选项	描述
`-a<日志文件>`	在指定的日志文件中记录资料的执行过程；
`-A<后缀名>`	指定要下载文件的后缀名，多个后缀名之间使用逗号进行分隔；
`-b`	进行后台的方式运行wget；
`-B<连接地址>`	设置参考的连接地址的基地地址；
`-c`	继续执行上次终端的任务；
`-C<标志>`	设置服务器数据块功能标志on为激活，off为关闭，默认值为on；
`-d`	调试模式运行指令；
`-D<域名列表>`	设置顺着的域名列表，域名之间用“，”分隔；
`-e<指令>`	作为文件“.wgetrc”中的一部分执行指定的指令；
`-h`	显示指令帮助信息；
`-i<文件>`	从指定文件获取要下载的URL地址；
`-l<目录列表>`	设置顺着的目录列表，多个目录用“，”分隔；
`-L`	仅顺着关联的连接；
`-r`	递归下载方式；
`-nc`	文件存在时，下载文件不覆盖原有文件；
`-nv`	下载时只显示更新和出错信息，不显示指令的详细执行过程；
`-q`	不显示指令执行过程；
`-nh`	不查询主机名称；
`-v`	显示详细执行过程；
`-V`	显示版本信息；
`–passive-ftp`	使用被动模式PASV连接FTP服务器；
`–follow-ftp`	从HTML文件中下载FTP连接文件。

* 示例

```bash
wget http://test.com/testfile.zip ->下载指定文件到当前文件夹
wget -O wordpress.zip http://test.com/download ->指定保存名字
wget --limit-rate=300k http://www.linuxde.net/testfile.zip ->限制下载速度
wget -c http://www.linuxde.net/testfile.zip ->断点续传
wget -b http://www.linuxde.net/testfile.zip ->后台下载

# 设置使用指定浏览器下载（伪装下载）
wget --user-agent="Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/534.16 (KHTML, like Gecko) Chrome/10.0.648.204 Safari/534.16" http://www.linuxde.net/testfile.zip

wget --spider url ->测试下载
wget --tries=40 URL ->设置重试次数为40
wget -i filelist.txt ->从filelist.txt获取下载地址

# 镜像网站
# --miror开户镜像下载。
# -p下载所有为了html页面显示正常的文件。
# --convert-links下载后，转换成本地的链接。
# -P ./LOCAL保存所有文件和目录到本地指定目录
wget --mirror -p --convert-links -P ./LOCAL URL

wget --reject=gif ur ->下载一个网站，但你不希望下载图片，可以使用这条命令
wget -o download.log URL ->把下载信息存入日志文件
wget -Q5m -i filelist.txt ->限制总下载文件大小
wget -r -A.pdf url ->下载指定格式文件

# FTP下载
wget ftp-url
wget --ftp-user=USERNAME --ftp-password=PASSWORD url
```

参考：https://blog.csdn.net/qq_27870421/article/details/91951402

### 多线程下载：Axel

Axel工具常用参数有：

axel ［选项］［下载目录］［下载地址］

-s ：指定每秒下载最大比特数

-n：指定同时打开的线程数

-o：指定本地输出文件

-S：搜索镜像并从X servers服务器下载

-N：不使用代理服务器

-v：打印更多状态信息

-a：打印进度信息

-h：该版本命令帮助

-V：查看版本信息号

### 高效搜索命令：Find

语法规则：

```bash
#option1: 指定查找目录途径
#option2: 指定查询参数
#parameter: 指定自定义参数
find <option1> <option2> <parameter>
```

先介绍下find**查找的目录途径**，分两种情况：

“.”代表是当前目录

“/”代表是根目录

find命令文件**查询常用的参数及意义**有以下几个：

1.`find -name`:直接根据文件名字来查找，比如，`find / -name a.txt`：在根目录下查找a.txt文件

2.`find -perm`：根据文件权限来查找，比如：`find / -perm 777`：查找根目录下权限为777（rwx-rwx-rwx）的文件

3.`find -mtine -n（+n）`：根据文件的更改时间来查找，其中-n代表n天之内的文件，+n代表n天之前的文件，比如：`find / -mtime -1`：查找1天内修改过的文件

4.`find -type`：查找某一类型的文件，后带一些参数：

​       `-d`：目录

​       `-p`：管道文件

​       `-f`：普通文件

​       `-i`：符号链接文件

用find命令查找到了文件，**输出的方式**有2种：

1.`find -print`：标准输出，比如：`find / ‘*.NCT’-print >a.txt`，代表将根目录下所的以nct命名的文件list输入到a.txt中

2.`find -exec`：对于find命令找到的文件执行该参数所给出的shell命令，比如：`find -name  test.txt -exec rm -f test.tx {} \；`找到test并删除此文件，该命令一定要注意{}和\之间和空格，不要遗漏最后的“；”

```bash
#exec方法 -exec后为shell命令
find /var/log -mtime -31 -exec cp -r {} /root/Desktop/bi296/lab3/log \;
```

注意其中`{}`表示搜索到文件，`\;`为转义结束符。

### 文件切割：Cut

**基本语法**：

```bash
cut [parameters] [filename]
```

cut命令从文件的每一行剪切字节，字符和字段并将此作为标准输出。

如果cut不指定Filename参数，那么cut命令将读取标准输入。利用此特点，可以使用pipe进行操作。

cut必须指定-b、-c、或-f标志之一，且cut以[Tab]作为分割符。

**参数说明**：

-b ：以字节为单位进行分割。这些字节位置将忽略多字节字符边界，除非也指定了 -n 标志。
-c ：以字符为单位进行分割。
-d ：自定义分隔符，默认为制表符。
-f ：与-d一起使用，指定显示哪个区域。
-n ：取消分割多字节字符。仅和 -b 标志一起使用。如果字符的最后一个字节落在由 -b 标志的 List 参数指示的范围之内，该字符被写出，否则将被排除。

**Cut的运用**：

cut命令主要接受三个定位方法

* 字节(Bytes)，使用选项`-b`
* 字符(Characters)，使用选项`-c`
* 域(Fields)，使用选项`-f`

1. **以字节模式定位**

```bash
#以Test文件为例
root@860f3b529924:~/Desktop# cat Test 
/usr/include/x86_64-linux-gnu/asm/bpf_perf_event.h
/usr/include/x86_64-linux-gnu/asm/mman.h
/usr/include/x86_64-linux-gnu/asm/hw_breakpoint.h
#提取每行第三个字节
root@860f3b529924:~/Desktop# cat Test | cut -b 3
s
s
s
#提取每行1-3个字节
root@860f3b529924:~/Desktop# cat Test | cut -b 1-3
/us
/us
/us
#从第三行提取到最后一行
root@860f3b529924:~/Desktop# cat Test | cut -b 3-
sr/include/x86_64-linux-gnu/asm/bpf_perf_event.h
sr/include/x86_64-linux-gnu/asm/mman.h
sr/include/x86_64-linux-gnu/asm/hw_breakpoint.h
```

2. **以字符定位**

字符定位与字节定位相似，不过在应对中文时，每个中文字符会被计算成两个字节单位。

 **-c会以字符为单位，输出正常；而-b只会傻傻的以字节（8位二进制位）来计算，输出就是乱码。**

比如下面这个例子：

```bash
root@860f3b529924:~/Desktop# cat Test2
我是刘德华
我是周杰伦
我是温家宝
我是迈克尔
root@860f3b529924:~/Desktop# cat Test2 | cut -b 3
\ufffd
\ufffd
\ufffd
\ufffd
```

解决方法：

当遇到多字节字符时，可以使用-n选项，-n用于告诉cut不要将多字节字符拆开。

如下例子：

```bash
[root@entel2 ~]# cat cut.txt 
周杰伦
邓紫棋
薛之谦
李荣浩
小工匠
[root@entel2 ~]# cat cut.txt |cut -nb 1,2,3
周
邓
薛
李
小
```

3. **以域为定位**

刚才提到的-b和-c只能在固定格式的文档中提取信息，而对于非固定格式的信息则束手无策。

这时候“域”就派上用场了。如果你观察过/etc/passwd文件，你会发现，它并不像who的输出信息那样具有固定格式，而是比较零散的排放。

但是，冒号在这个文件的每一行中都起到了非常重要的作用，冒号用来隔开每一个项。

我们很幸运，cut命令提供了这样的提取方式，具体的说就是设置“间隔符”，再设置“提取第几个域”，就可以解决！

举`/etc/passwd`为例子，假如我们需要提取所有的用户名。

```bash
root@860f3b529924:~/Desktop# head -5 /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
```

可以采用如下操作：

```bash
root@860f3b529924:~/Desktop# head -5 /etc/passwd | cut -d : -f 1
root
daemon
bin
sys
sync
```

当然也可以指定域，如提取用户名和shell。

```bash
root@860f3b529924:~/Desktop# head -5 /etc/passwd | cut -d : -f 1,7
root:/bin/bash
daemon:/usr/sbin/nologin
bin:/usr/sbin/nologin
sys:/usr/sbin/nologin
sync:/bin/sync
```

4. 碎碎念

cut默认以Tab为分割符，其显示为\t。如果需要指定空格为分割符，需要如下操作：

```bash
cut -d ' ' filename
```

### 文本处理：Grep

基本语法：

`grep [options] "Pattern" [File]`

常用选项：

`-E` ：开启扩展（Extend）的正则表达式。

`-i` ：忽略大小写（ignore case）。

`-v` ：反过来（invert），只打印没有匹配的，而匹配的反而不打印。

`-n` ：显示行号

`-w` ：被匹配的文本只能是单词，而不能是单词中的某一部分，如文本中有liker，而我搜寻的只是like，就可以使用-w选项来避免匹配liker

`-z`：多行匹配

`-c` ：显示总共有多少行被匹配到了，而不是显示被匹配到的内容，注意如果同时使用-cv选项是显示有多少行没有被匹配到。

`-o` ：只显示被模式匹配到的字符串。

`--color` :将匹配到的内容以颜色高亮显示。

`-A  n`：显示匹配到的字符串所在的行及其后n行，after

`-B  n`：显示匹配到的字符串所在的行及其前n行，before

`-C  n`：显示匹配到的字符串所在的行及其前后各n行，context

模式部分：

1. 直接输入要匹配的字符串，可以使用`fgrep`来提高运行速度。

2. 使用基本正则表达式。

### 流文件处理：Sed

> This file documents version 4.8 of GNU `sed`, a stream editor.

> Copyright © 1998–2020 Free Software Foundation, Inc.

Sed在处理数据前，需要预先提供一组规则，之后按照此规则来编辑数据。此命令执行数据的顺序如下：

1. 每次读取一行内容。
2. 根据规则命令匹配修改数据。此数据为缓冲区数据，而不是直接修改源文件。
3. 输出执行结果。

基本语法规则：

`sed [options] [scripts] Filename`

常用选项：

| 选项              | 含义                                                         |
| ----------------- | ------------------------------------------------------------ |
| `-e` 脚本命令     | 该选项会将其后跟的脚本命令添加到已有的命令中。               |
| `-f` 脚本命令文件 | 该选项会将其后文件中的脚本命令添加到已有的命令中。           |
| `-n`              | 默认情况下，sed 会在所有的脚本指定执行完毕后，会自动输出处理后的内容，而该选项会屏蔽启动输出，需使用 print 命令来完成输出。（防止重复输出） |
| `-i`              | 此选项会直接修改源文件，要慎用。                             |
| `-E` or `-r`      | Extended regexp mode                                         |

脚本语法：

syntax are followed：

```bash
[address][options]
```

比如下面的例子：删除30-35行并重定向到`output.txt`

```bash
sed '30,35d' input.txt > output.txt
```

也可以使用ReExp格式的地址：

```bash
sed '/root/p' /etc/passwd
```

ReExp表达式用`//`包含。

多行命令可以使用`-e`选项或`；`进行间隔

````bash
sed '/^root/d ; s/hello/world' input.txt > output.txt
sed -e '/^root/d' -e 's/hello/world' input.txt > output.txt
````

也可以使用脚本文件输入：

```bash
echo '/^foo/d' > script.sed
echo 's/hello/world/' >> script.sed
sed -f script.sed input.txt > output.txt
```

常用命令：

`a\ text` ：文本追加

`i\ text`：行前插入文本

`c\ text`：文本替换（完全替换地址上内容）

`d`: 文本删除

The `s` command：

Syntax：`'s/regexp/replacement/flags'`

The `s` command can be followed by zero or more of the following flags:

| flags 标记 | 功能                                                         |
| ---------- | ------------------------------------------------------------ |
| n          | 1~512 之间的数字，表示指定要替换的字符串出现第几次时才进行替换，例如，一行中有 3 个 A，但用户只想替换第二个 A，这是就用到这个标记； |
| g          | 对数据中所有匹配到的内容进行替换，如果没有 g，则只会在第一次匹配成功时做替换操作。例如，一行数据中有 3 个 A，则只会替换第一个 A； |
| p          | 会打印与替换命令中指定的模式匹配的行。此标记通常与 -n 选项一起使用。 |
| w file     | 将缓冲区中的内容写到指定的 file 文件中；                     |
| &          | 用正则表达式匹配的内容进行替换；                             |
| \n         | 匹配第 n 个子串，该子串之前在 pattern 中用 \(\) 指定。       |
| \          | 转义（转义替换部分包含：&、\ 等）。                          |

- `g`

  Apply the replacement to *all* matches to the regexp, not just the first.

- `number`

  Only replace the numberth match of the regexp.

  > interaction in `s` command Note: the POSIX standard does not specify what should happen when you mix the `g` and number modifiers, and currently there is no widely agreed upon meaning across `sed` implementations. For GNU `sed`, the interaction is defined to be: ignore matches before the numberth, and then match and replace all matches from the numberth on.

- `p`

  If the substitution was made, then print the new pattern space.

  > Note: when both the `p` and `e` options are specified, the relative ordering of the two produces very different results. In general, `ep` (evaluate then print) is what you want, but operating the other way round can be useful for debugging. For this reason, the current version of GNU `sed` interprets specially the presence of `p` options both before and after `e`, printing the pattern space before and after evaluation, while in general flags for the `s` command show their effect just once. This behavior, although documented, might change in future versions.

- `w filename`

  If the substitution was made, then write out the result to the named file. 

  > As a GNU `sed` extension, two special values of filename are supported: /dev/stderr, which writes the result to the standard error, and /dev/stdout, which writes to the standard output.[3](http://www.gnu.org/software/sed/manual/sed.html#FOOT3)

- `e`

  This command allows one to pipe input from a shell command into pattern space. If a substitution was made, the command that is found in pattern space is executed and pattern space is replaced with its output. A trailing newline is suppressed; results are undefined if the command to be executed contains a NUL character. This is a GNU `sed` extension.

- `I`

- `i`

  The `I` modifier to regular-expression matching is a GNU extension which makes `sed` match regexp in a case-insensitive manner.

- `M`

- `m`

  The `M` modifier to regular-expression matching is a GNU `sed` extension which directs GNU `sed` to match the regular expression in multi-line mode. The modifier causes `^` and `$` to match respectively (in addition to the normal behavior) the empty string after a newline, and the empty string before a newline. There are special character sequences (`\`` and `\'`) which always match the beginning or the end of the buffer. In addition, the period character does not match a new-line character in multi-line mode.


地址类型：

Addresses in a `sed` script can be in any of the following forms:

- `number`

  Specifying a line number will match only that line in the input. (Note that `sed` counts lines continuously across all input files unless -i or -s options are specified.)

- `$`

  This address matches the last line of the last file of input, or the last line of each file when the -i or -s options are specified.

- `first~step`

  This GNU extension matches every stepth line starting with line first. In particular, lines will be selected when there exists a non-negative n such that the current line-number equals first + (n * step). Thus, one would use `1~2` to select the odd-numbered lines and `0~2` for even-numbered lines; to pick every third line starting with the second, ‘2~3’ would be used; to pick every fifth line starting with the tenth, use ‘10~5’; and ‘50~0’ is just an obscure way of saying `50`.The following commands demonstrate the step address usage:`$ seq 10 | sed -n '0~4p' 4 8 $ seq 10 | sed -n '1~3p' 1 4 7 10`

* `/regexp/`

This will select any line which matches the regular expression regexp. If regexp itself includes any `/` characters, each must be escaped by a backslash (`\`).

The following command prints lines in /etc/passwd which end with ‘bash’[5](http://www.gnu.org/software/sed/manual/sed.html#FOOT5):

```
sed -n '/bash$/p' /etc/passwd
```

The empty regular expression ‘//’ repeats the last regular expression match (the same holds if the empty regular expression is passed to the `s` command). Note that modifiers to regular expressions are evaluated when the regular expression is compiled, thus it is invalid to specify them together with the empty regular expression.

The following examples demonstrate the difference between starting with address 1 and 0:

```
$ seq 10 | sed -n '1,/[0-9]/p'
1
2

$ seq 10 | sed -n '0,/[0-9]/p'
1
```

* `addr1,+N`

Matches addr1 and the N lines following addr1.

```
$ seq 10 | sed -n '6,+2p'
6
7
8
```

addr1 can be a line number or a regular expression.

* `addr1,~N`

Matches addr1 and the lines following addr1 until the next line whose input line number is a multiple of N. The following command prints starting at line 6, until the next line which is a multiple of 4 (i.e. line 8):

```
$ seq 10 | sed -n '6,~4p'
6
7
8
```

addr1 can be a line number or a regular expression.

其他脚本命令：

* **sed y 转换脚本命令**

y 转换命令是唯一可以处理单个字符的 sed 脚本命令，其基本格式如下：

`[address]y/inchars/outchars/`

转换命令会对 inchars 和 outchars 值进行一对一的映射，即 inchars 中的第一个字符会被转换为 outchars 中的第一个字符，第二个字符会被转换成 outchars 中的第二个字符...这个映射过程会一直持续到处理完指定字符。如果 inchars 和 outchars 的长度不同，则 sed 会产生一条错误消息。

举个简单例子：

```bash
cat data.txt
This is line number 1.
This is line number 2.
This is line number 3.
This is line number 4.
This is line number 3 again.
This is yet another line.
This is the last line in the file.
[root@localhost ~]# sed 'y/123/789/' data.txt
This is line number 7.
This is line number 8.
This is line number 9.
This is line number 4.
This is line number 9 again.
This is yet another line.
This is the last line in the file.
```

可以看到，inchars 模式中指定字符的每个实例都会被替换成 outchars 模式中相同位置的那个字符。

转换命令是一个全局命令，也就是说，它会文本行中找到的所有指定字符自动进行转换，而不会考虑它们出现的位置，再打个比方：

```bash
[root@localhost ~]# echo "This 1 is a test of 1 try." | sed 'y/123/456/'
This 4 is a test of 4 try.
```

sed 转换了在文本行中匹配到的字符 1 的两个实例，我们无法限定只转换在特定地方出现的字符。

- 针对内存空间的操作符

| Operator | Meaning                                 |
| -------- | --------------------------------------- |
| `d D`    | 删除模式空间中的内容至结束/第一个换行符 |
| `h H`    | 将模式空间中内容复制到hold space        |
| `g G`    | 将hold space中内容复制到模式空间        |
| `x`      | 交换hold space和模式空间中的内容        |
| `n N`    | 将数据流数据追加到模式空间中            |

![image-20210407112205062](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210407112205062.png)

Sed在正常情况下，将处理的行读入模式空间（pattern space），脚本中的命令就会被一行一行依次执行直至执行完毕，之后该行输出，pattern space被清空。此时sed再读入下一行，重复操作。

<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210414200846354.png" alt="image-20210414200846354" style="zoom:67%;" />

若有时用户希望保留模式空间用于后续指令，此时就需要使用sed高级命令来满足需求。

* **p和P命令**

p命令打印当前模式空间所有内容后追加到默认输出之后；P打印当前模式空间开头至\n换行符，再追加到默认输出之前。

* **n和N命令**

n命令**读取**下一行到pattern space。由于正常流程下pattern space读取了一行内容，添加n命令后pattern space添加了一行内容，但正常读取的行不会被删除。若此时再执行p命令，则输出n命令读取的一行。

正常的sed流程：

<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210414201409706.png" alt="image-20210414201409706" style="zoom:67%;" />

使用n命令后：

<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210414201430843.png" alt="image-20210414201430843" style="zoom:67%;" />

N命令时将下一行**添加**到pattern space中，将正常流程读入的一行和N命令添加的一行视作“一行”。

<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Linux操作系统.assets\image-20210414201544452.png" alt="image-20210414201544452" style="zoom:67%;" />

n命令是提前读取下一行，并“覆盖”已读取的一行（未删除）执行后续命令。之后再读取新的一行，对新读取的内容重新执行sed。

例如从文件中打印所有偶数行：

```bash
cat test.txt
line 1
line 2
line 3
line 4
line 5

sed -n 'n;p' test.txt
```

N命令是追加下一行，将两行视作一行，但是仍然保留两行之间的换行符。若命令执行失败则放弃后续所有指令并读取新的内容。

```bash
sed -n 'N;P' test.txt | cat

line 1
line 3

#在第五行执行N命令时未发现第六行，因此放弃后续所有指令
#可以修改为一下模式

sed -n '$!N;P' test.txt | cat
#若不是尾行则执行N和P，反之只执行P

line 1
line 3
line 5
```

* **d和D命令**

d命令是删除当前模式空间内容并放弃后续命令，对新的行重头执行sed命令。

```bash
sed 'n;d' test.txt | cat
line 1
line 3
line 5
```

D命令是删除当前模式空间至\n，放弃后续命令但对**剩余的模式空间**重新执行sed。

```bash
sed 'N;D' test.txt | cat
line 5
```

该命令的执行过程是：首先读取一行后成功执行N命令，此时模式空间中为`line 1 \n line 2`，此后执行D命令，**剩下的**模式空间为`line 2`，再继续对此执行N命令，得到模式空间`line 2 \n line 3`，依次类推....最终读取`line 5`时无法执行N命令，故`line 5`被保留下来。

* **h,H,g,G命令**

h命令是将当前模式空间中内容覆盖至缓存区，H命令是将当前模式空间中的内容追加至缓存区
g命令是将当前缓存区中内容覆盖至模式空间，G命令是将当前缓存区中的内容追加至模式空间

在sed处理文件时，每一行都被保存在模式空间的临时缓冲区中，也称为保留空间。一般的命令只能对保留空间操作。

如下例子：

```bash
#/bin/sed -f
h #复制当前模式空间
{
s/.*is\(.*\) and .*/\1/ #将每行替换为字母
y/abcde/ABCDE/  #将字母转化成大写
G  #追加缓冲区内容到当前模式空间后
s/\(.*\)\n\(.*is \).*\(and \).*\(is \)\(.*\)/\2\5 \3\5 \4\1/  
}
```

执行结果：

```bash
root@860f3b529924:~/Desktop/bi296/practice# cat test.txt 
This is a and a is 1
This is b and b is 2
This is c and c is 3
This is d and d is 4
This is e and e is 5
root@860f3b529924:~/Desktop/bi296/practice# sed -f command.sed test.txt 
This is 1 and 1 is  A
This is 2 and 2 is  B
This is 3 and 3 is  C
This is 4 and 4 is  D
This is 5 and 5 is  E
```

G命令执行后结果：

```bash
 A
This is a and a is 1
 B
This is b and b is 2
 C
This is c and c is 3
 D
This is d and d is 4
 E
This is e and e is 5
```

* **x命令**

x命令时将缓冲区和模式空间内容互换。

### 数据处理：Awk

Awk是一个文本分析工具。简单而言，awk将文件逐行读入并以空格为默认分割符将行切割，切开的部分再进行各种分析处理。

语法：`awk 'pattern{action}' {filename}`

其中`{}`无需始终标注，`/pattern/`中为正则表达式。

完整的awk脚本通常用于格式化文本文件中的信息，并以行为基本处理单位。awk每接受一行文件后再执行相应的命令从而处理文本。

* 内置变量

| Variables     | Instruments                               |
| ------------- | ----------------------------------------- |
| `ARGC`        | 命令行参数个数                            |
| `ARGV`        | 命令行参数                                |
| `ENVIRON`     | 支持队列中系统环境变量的使用              |
| `FILENAME`    | awk浏览的文件名                           |
| `FNR`         | 浏览文件记录数                            |
| `FS`          | 设置输入域分割符，等价于`-F`选项          |
| `NF`          | 浏览记录的域的个数                        |
| `NR`          | 已读入的记录数                            |
| `OFS`         | 输出域分隔符                              |
| `ORS`         | 输出记录分隔符                            |
| `RS`          | 控制记录分隔符                            |
| `$0,$1,$2...` | 0代表整个记录，1-n依次代表当前行的1-n个域 |
| `$NF`         | 表示最后一列的信息，不同于NF              |

* 常用命令举例

搜索含某关键字的行

```bash
awk '/root/' /etc/passwd
```

搜索含关键字的某行，并打印特定字段

```bash
awk -F ':' '/root/ {print $1}' /etc/passwd
```

统计文件名，每行行号，每列列数和对应行完整内容

```bash
awk -F ':' '/bash/{print "filename:" FILENAME "linenumber:" NR "columns:" NF "content:" $0}' /etc/passwd
```

还可以使用`printf`使代码更加简洁

```bash
awk -F ':' '/bash/ {printf("filename:%10s\nlinenumber:%3s\ncolumn:%3s\ncontent: %3f\n",FILENAME,NR,NF,$0)}' /etc/passwd
```

指定行数操作

```bash
awk -F ':' 'NR==2{print "filename:" FILENAME " content:" $0}' /etc/passwd
```

指定正则表达式的查询

```bash
awk -F ':' '/(bash)$/{print $1}' /etc/passwd
```

若不指定行过滤，则读入所有行

```bash
awk -F ':' '{print $1}' /etc/passwd
```

指定分隔符访问倒数第二列元素

```bash
awk -F ':' '{print $NF-1}' /etc/passwd
```

访问特定行数

```bash
awk -F ':' '{if (NR<10 && NR>1) print $1}' /etc/passwd
```

多分隔符的使用

```bash
awk -F '[/]' 'NR==4 {print $0, '\n',$1}' /etc/passwd
```

添加BEGIN和END（在处理数据之前和结束之后执行的action）

```bash
awk -F ':' 'BEGIN{print "Username will be extracted!\n Working..."} {print $1}  END{print"Done!"}' /etc/passwd
```

利用RegExp过滤空格

```bash
ifconfig | grep -P 'eth*' | awk -F '[ ]+' '{print $1}'
```

* awk编程

除了内置变量外，awk还允许引入自定义变量。同时可以使用条件，循环控制等，类似于C语言。如下例子：

```bash
#统计某个文件下所有大于1000k文件的总大小
ls -l | awk '{if($5>1000){count++; sum+=$5}} {print "Counts:"count" Total size:" sum}'
#添加End可以只表示一次循环内的输出
ls -l | awk '{if($5>1000){count++; sum+=$5}} END{print "Counts:"count" Total size:" sum}'
```

除了命令行的简单操作外，awk还支持文件输入命令。

```bash
#!/bin/awk -f
BEGIN {
   RS = "";    # blank line as record separator
   FS = "\n";
}
{
   print "Name: ", $1
   print "Zip: ", $NF
}
```

Awk也支持正则表达式匹配：

```bash
BEGIN{FS=","}
{
$3 ~/CA/ print($1":"$3) #如果$3满足后续reg则执行print，类似于if
}
```

#### 第一节：Awk语法

测试用`data.txt`文件：

```
Mike Harrington:[510] 548-1278:250:100:175

Christian Dobbins:[408] 538-2358:155:90:201

Susan Dalsass:[206] 654-6279:250:60:50

Archie McNichol:[206] 548-1348:250:100:175

Jody Savage:[206] 548-1278:15:188:150

Guy Quigley:[916] 343-6410:250:100:175

Dan Savage:[406] 298-7744:450:300:275

Nancy McNeil:[206] 548-1278:250:80:75

John Goldenrod:[916] 348-4278:250:100:175

Chet Main:[510] 548-5258:50:95:135
```

##### 1.1 Awk工作流程

* 从指定的数据文件中读取一个数据行
* 自动更新内建变量值
* 依次执行完程序中所有`pattern{action}`指令

* 检查是否有未读取数据，有则重复步骤1~3

##### 1.2 参数

* `-v` 定义变量，如 `awk -v a=test '{print a}' data.txt`
* `-F` 分隔符 `awk -F: '{print $1}' data.txt`
* `-f` 指定awk脚本

##### 1.3 模式

- 关系表达式 ：使用像 " A 关系运算符 B" 的表达式当成 Pattern
- 正则表达式 : 在两个斜杠中(//)使用正则表达式
- 模式的组合
- BEGIN 和 END 模式

常见运算符：

| 运算符                  | 描述                                        |
| ----------------------- | ------------------------------------------- |
| = += -= *= /= %= ^= **= | 赋值，注：=是赋值，==是判断符，即关系运算符 |
| ?:                      | C条件表达式                                 |
| \|\|                    | 逻辑或                                      |
| &&                      | 逻辑与                                      |
| ~   ~!                  | 匹配正则表达式和不匹配正则表达式            |
| < <= > >= != ==         | 关系运算符                                  |
| 空格                    | 连接                                        |
| + -                     | 加减                                        |
| * / &                   | 乘除与求余                                  |
| + - !                   | 一元加减和逻辑非                            |
| ^ ***                   | 求幂                                        |
| ++ --                   | 增加或减少，作为前缀或后缀                  |
| $                       | 字段引用                                    |
| in                      | 数组成员                                    |

##### 1.4 操作

操作由一个或多个命令，函数或表达式组成，之间由**换行符**或**分号**间隔，并位于大括号内。

##### 1.5 正则表达式

在两个斜杆中使用正则表达式，举例：

```bash
awk -F : '$1 ~/^Mike/{print $0}' data.txt
#若第一个元素满足Mike开头则打印整行
```

##### 1.6 模式的组合

`/reg1/,/reg2/`

匹配reg1和reg2的行和两者之间的行。

```bash
awk -F : '/^Mike/,/^Dan/{print NR":", $0}' data.txt
```

##### 1.7 BEGIN和END模式

BEGIN模块在awk处理任意输入文件前执行，其常常用于改变内建变量的值和打印标题。

```bash
awk 'BEGIN{FS=":"}{pring $1":"$3}' data.txt
```

END模块不匹配任何输入文件，但执行动作块中所有动作，其在**整个输入文件处理完成后**被执行。

```bash
#统计行数
awk 'END{print "The total lines are:"NR}' data.txt
```

#### 第二节：控制流

##### 2.1 if语句

awk的if语句和c语言类似，语法如下：

```bash
#/bin/awk -f
{
if (expression)
{action;}
else
{action;}
if (expression)
{action;}
else if (expression1)
{aciton;}
else{action;}
}
```

##### 2.2 while循环

while的使用类似于C语言，具体例子如下：

```bash
awk 'NR==1{print $0}' data.txt | awk -F : '{i=1;while(i<NF){print i,$i;i++}}'
```

##### 2.3 for循环

```bash
#/bin/awk -f
BEGIN{
FS=":"
}
{
for (i=1;i<NF;i++)
{print i,$i}
}
```

在awk中同样可以使用`break`和`continue`。

##### 2.4 printf函数

awk的`printf`使用和C相似度极高，语法如下：

```
printf("格式化字符串"，参量表)
```

示例：

```bash
awk -F [:] '{printf("%-19s,%-20s,%-5d\n",$1,$2,$3)}'
```

格式化字符串可以是一般字符串，修饰符和格式说明符。

* 修饰符

| 字符 | 定义                                                    |
| ---- | ------------------------------------------------------- |
| -    | 左对齐修饰符，常用                                      |
| #    | 显示8 进制整数时在前面加个0 显示16 进制整数时在前面加0x |
| +    | 显示使用d 、e 、f 和g 转换的整数时，加上正负号+或-      |
| 0    | 用0而不是空白符来填充所显示的值                         |

* 格式说明符

| 格式说明符 | 功能                                             |
| ---------- | ------------------------------------------------ |
| %d         | 十进制有符号整数                                 |
| %u         | 十进制无符号整数                                 |
| %f         | 浮点数                                           |
| %s         | 字符串                                           |
| %c         | 单个字符                                         |
| %p         | 指针的值                                         |
| %e         | 指数形式的浮点数                                 |
| %x         | %X 无符号以十六进制表示的整数                    |
| %0         | 无符号以八进制表示的整数                         |
| %g         | 自动选择合适的表示法                             |
| \n         | 换行                                             |
| \f         | 清屏并换页                                       |
| \r         | 回车                                             |
| \t         | Tab符                                            |
| \xhh       | 表示一个ASCII码用16进表示,其中hh是1到2个16进制数 |

说明：

(1). 可以在"%"和字母之间插进数字表示最大场宽。

例如:

- %3d 表示输出3位整型数, 不够3位右对齐。
- %9.2f 表示输出场宽为9的浮点数, 其中小数位为2, 整数位为6,小数点占一位, 不够9位右对齐。
- %8s 表示输出8个字符的字符串, 不够8个字符右对齐。

如果字符串的长度、或整型数位数超过说明的场宽, 将按其实际长度输出.但对浮点数, 若整数部分位数超过了说明的整数位宽度, 将按实际整数位输出;若小数部分位数超过了说明的小数位宽度, 则按说明的宽度以四舍五入输出.

(2).另外, 若想在输出值前加一些0, 就应在场宽项前加个0。

例如: %04d 表示在输出一个小于4位的数值时, 将在前面补0使其总宽度为4位。

如果用浮点数表示字符或整型量的输出格式, 小数点后的数字代表最大宽度,小数点前的数字代表最小宽度。

例如: %6.9s 表示显示一个长度不小于6且不大于9的字符串。若大于9, 则第9个字符以后的内容将被删除。

(3). 可以在"%"和字母之间加小写字母l, 表示输出的是长型数。

例如: %ld 表示输出long整数

%lf 表示输出double浮点数

(4). 可以控制输出左对齐或右对齐, 即在"%"和字母之间加入一个"-" 号可说明输出为左对齐, 否则为右对齐。

例如: %-7d 表示输出7位整数左对齐

%-10s 表示输出10个字符左对齐

### 第三节：其他特性

##### 3.1 数组

awk数组的使用类似于C语言中的数组，使用索引访问元素。

```bash
awk 'BEGIN{FS=":"}{name[x++]=$1}END{for(i=0;i<NR;i++)print i,name[i]}' data.txt
```

这里是将`$1`的值依次赋给`name[0]`, `name[1]`....在不指定x初值的时候，下标的初值为零。

如下数据`data1.txt`：

````
xiaoming Chinese English Mathematics
xiaoli English Mathematics
xiaohua Chinese Mathematics
xiaohong English
````

统计各个课程上课的人数：

```bash
awk '{for(i=2;i<=NF;i++)num[$i]++}END{for(course in num)printf("%-10s %d\n",course,num[course])}' data1.txt
```

##### 3.2 内置函数

* **sub(reg,string,target)**

将原字串中第一个(最左边)满足指定的正则表达式的子字串改以新字串取代. 第二个参数"将替換的新字串"中可用"&"来代表"满足条件的子字串"

* **gsub(reg,string,target)**

类似于`sub()`函数，但`gsub()`还会返回被取代的字符串个数。

* **length(string)**

返回字符串的长度。

* **substr函数**

截取字段函数。

返回字段1的第一个字符到第三个字符，如果超过字段1的实际长度，返回整个字段1

```bash
#从字段1的第五个字符开始截取3个元素
awk -F : '{print substr($1,5,3)}' data.txt
```

* **toupper和tolower函数**

这两个函数可以实现字符串大小写之间的转化，仅在gawk中生效。

```bash
awk -F: '{ print toupper($1), tolower($1) }' data.txt
```

* **split(string,store,delim)**

awk将依所指定的分隔字符(field separator)来分隔原字串成一个个的栏位(field),并以指定的数组（下标从1开始）记录各个被分隔的栏位。

如，以`：`为分隔符对`$2`进行分割并存储到`name[]`数组中。

```bash
awk -F: '{split($2,name," "); print name[1] }' data.txt
```

## 第七章 Shell开发进阶

