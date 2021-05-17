# 实验三：熟悉Linux命令行

* 姓名：王喜
* 学号：519111910172

[toc]

## 实验目的

1. 掌握常用的目录、权限管理的命令
2. 掌握用户管理的命令
3. 掌握文件操作的命令
4. 熟练man的使用



## 实验内容

### 一、目录、权限管理

1. 以普通用户的身份登录你的Linux操作系统，打开终端。你现在的命令提示符是什么？

   `[zhang@fb27e4a2246c /]$`

2. 输入`cd`，然后看看你当前所在的绝对路径是什么？

   ```
   [zhang@fb27e4a2246c ~]$ cd
   [zhang@fb27e4a2246c ~]$ pwd
   /home/zhang
   [zhang@fb27e4a2246c ~]$
   ```
   
3. 输入`mkdir -p bi296/lab3`创建多级目录`bi296/lab3`，这是我们这次实验的目录。

   ![image-20210319132422279](C:\Users\18113\AppData\Roaming\Typora\typora-user-images\image-20210319132422279.png)

4. 进入目录`bi296`，可以用`绝对路径`，也可以用`相对路径`的方式。

   ```
   root@860f3b529924:~/Desktop# cd bi296/
   root@860f3b529924:~/Desktop/bi296# 
   ```

5. 用`ls -ld`查看一下`lab3`这个目录的默认权限。

   ```
   drwxr-xr-x 2 root root 4096 3\月  10 10:03 lab3/
   #r:read; w:write; x:execute;
   #也可以使用二进制转十进制表示，如rwx为111，为十进制的7;
   #可以使用chmod修改权限
   chmod u+x filename
   chmod 777 filename
   ```

![file-permissions-rwx](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\img\file-permissions-rwx.jpg)

References: 

1. https://www.runoob.com/linux/linux-comm-chmod.html

2. https://blog.csdn.net/sinat_30071459/article/details/51191693?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.baidujs&dist_request_id=&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.baidujs

6. 在命令行中输入：
```bash
cat >lab3/hello.sh <<EOF
#!/bin/bash
echo "What's your name?"
read name
echo "Welcome to Linux, \${name}"
EOF
```
7. 这是通过重定向的方式将EOF之间的内容输出到文件`lab3/hello.sh`中。用`cat`查看该文件的内容，看看写入是否成功。

   ```
   root@860f3b529924:~/Desktop/bi296# cd lab3/
   root@860f3b529924:~/Desktop/bi296/lab3# cat hello.sh 
   #!/bin/bash
   echo "What's your name?"
   read name
   echo "Welcome to Linux, ${name}"
   ```

8. 然后我们试图运行`lab3/hello.sh`，会有什么样的输出/错误产生？

   ```
   root@860f3b529924:~/Desktop/bi296/lab3# ./hello.sh
   bash: ./hello.sh: Permission denied
   #提示无权限
   ```

9. 怎么样去修改权限才能让上面的脚本得以运行，有哪些不同方式？

   ```
   #使用bash命令执行
   root@860f3b529924:~/Desktop/bi296/lab3# bash hello.sh
   What's your name?
   xi wang
   Welcome to Linux, xi wang
   #使用chmod添加execute权限
   chmod u+x hello.sh
   ```

10. 权限修改完成后，重新运行该脚本，得到什么样的输出？

    ```
    root@860f3b529924:~/Desktop/bi296/lab3# chmod u+x hello.sh 
    root@860f3b529924:~/Desktop/bi296/lab3# ./hello.sh
    What's your name?
    xi wang 
    Welcome to Linux, xi wang
    ```

11. 如果将该输出/错误重定向到一个文件`lab3/hello.log`，该怎么运行这个脚本？

    ```bash
    ./hello.log
    ```

    依然可以继续执行，Linux中文件扩展名仅作为标识符，系统不识别。

12. 尝试修改lab3目录的权限，比如去除读（`r`）的权限，你还能进入这个目录么，你还能运行上面这个脚本么？如果去除的是执行（`x`）的权限呢？读取文件的权限呢？

    ```bash
    #去除读写权限仍能进入目录，不可以执行文件
    root@860f3b529924:~/Desktop/bi296# chmod -r lab3
    root@860f3b529924:~/Desktop/bi296# cd lab3
    root@860f3b529924:~/Desktop/bi296/lab3# ./hello.sh
    bash: ./hello.sh: Permission denied
    root@860f3b529924:~/Desktop/bi296/lab3# 
    #去除执行权限可以进入目录，不可执行文件
    root@860f3b529924:~/Desktop/bi296# chmod -x lab3
    root@860f3b529924:~/Desktop/bi296# cd lab3
    root@860f3b529924:~/Desktop/bi296/lab3# ./hello.sh
    bash: ./hello.sh: Permission denied
    ```

13. 用`ln`为上面的脚本在当前目录下创建一个硬链接（hard link）和一个符号链接（symbolic link），用`ls -i`查看这两个文件的i节点编号，与源文件`lab3/hello.sh`相比，是否相同？这两个文件的文件类型分别是什么（给出文件类型位信息）？

    ```bash
    #e是符号连接，e2是硬连接
    root@860f3b529924:~/Desktop/bi296/lab3# ls -i
    11594 e  11585 e2  11585 hello.sh
    root@860f3b529924:~/Desktop/bi296/lab3# file e
    e: symbolic link to hello.sh
    root@860f3b529924:~/Desktop/bi296/lab3# file e2
    e2: Bourne-Again shell script, ASCII text executable 
    ```

    硬连接和源文件的I节点编号相同，而符号连接不同。

    符号连接的文件类型是`symbolic link to hello.sh`，而硬连接的文件类型是`Bourne-Again shell script, ASCII text executable` 。

14. 是否可以执行这两个文件？如果不行，缺少了哪个步骤呢？

    ```bash
    #均不可执行
    root@860f3b529924:~/Desktop/bi296/lab3# ./e
    bash: ./e: Permission denied
    root@860f3b529924:~/Desktop/bi296/lab3# ./e2
    bash: ./e2: Permission denied
    #赋予可执行权限
    root@860f3b529924:~/Desktop/bi296/lab3# chmod +x e e2
    root@860f3b529924:~/Desktop/bi296/lab3# ./e
    What's your name?
    wx
    Welcome to Linux, wx
    root@860f3b529924:~/Desktop/bi296/lab3# ./e2
    What's your name?
    wx
    Welcome to Linux, wx
    ```

15. 用`mv`将`lab3/hello.sh`移出`lab3`目录，看看这个文件的i节点是否会发生改变。

    ```bash
    #文件节点未变化
    root@860f3b529924:~/Desktop/bi296/lab3# mv hello.sh /root/Desktop
    root@860f3b529924:~/Desktop/bi296/lab3# ls -i
    11594 e  11585 e2
    ```

16. 这时候硬链接和符号链接哪个还能够执行？为什么？

    ```bash
    #符号连接无法执行，硬连接可以执行
    root@860f3b529924:~/Desktop/bi296/lab3# ./e
    bash: ./e: No such file or directory
    root@860f3b529924:~/Desktop/bi296/lab3# ./e2
    What's your name?
    xi
    Welcome to Linux, xi
    ```

    **符号链接**中，删除被链接文件后，文件内容清空，链接文件为空文件； 

    **硬链接**中，无论是删除被链接文件或链接文件，均不会直接删除文件内容。当被链接文件和全部链接文件被删除后，被链接文件内容才被删除；

17. 将该脚本文件移动到目录`lab3`中，这时候符号链接文件的有效性是否发生改变？

````bash
#符号链接文件的有效性未发生变化
root@860f3b529924:~/Desktop# mv hello.sh bi296/lab3/
root@860f3b529924:~/Desktop# cd bi296/lab3/
root@860f3b529924:~/Desktop/bi296/lab3# ./e
What's your name?
xi
Welcome to Linux, xi
root@860f3b529924:~/Desktop/bi296/lab3# ls -i
11594 e  11585 e2  11585 hello.sh
````

18. 修改上述脚本文件名为`test.sh`，这时候符号链接文件是否失效？

```bash
#符号链接文件失效
root@860f3b529924:~/Desktop/bi296/lab3# mv hello.sh test.sh
root@860f3b529924:~/Desktop/bi296/lab3# ls -i
11594 e  11585 e2  11585 test.sh
root@860f3b529924:~/Desktop/bi296/lab3# ./e
bash: ./e: No such file or directory
```

19. 这时候问一个问题，修改文件名、移动一个文件、复制一个文件是否会改变一个文件的i节点信息？

    不会改变。

    ```bash
    root@860f3b529924:~/Desktop/bi296/lab3# ls -i
    11594 e  11585 e2  11585 test.sh
    root@860f3b529924:~/Desktop/bi296/lab3# mv test.sh /root/Desktop/
    root@860f3b529924:~/Desktop# ls -i
     11586 bi296  137181 hello.c~   12076 Makefile   11585 test.sh
    120113 Code    12109 lab3       12120 test1.c~   12066 test.sh~
    ```

20. 用`cp`命令为目录`lab3`创建一个拷贝`lab3.backup`，需要加入哪个选项？

    ```bash
    root@860f3b529924:~/Desktop/bi296# cp -r lab3 lab3.copy
    root@860f3b529924:~/Desktop/bi296# ls
    lab3  lab3.copy
    ```

21. 尝试用`rm`或者`rmdir`删除这个目录`lab3.backup`，如何执行？

    ```bash
    root@860f3b529924:~/Desktop/bi296# rm -rf lab3.copy/
    root@860f3b529924:~/Desktop/bi296# ls
    lab3
    ```

22. 能否为目录`lab3`创建硬链接`lab3.default`？符号链接呢？

    不可以创建硬链接。

    ```bash
    root@860f3b529924:~/Desktop/bi296# ln lab3
    ln: lab3: hard link not allowed for directory
    ```

    可以创建符号链接。

    ```bash
    root@860f3b529924:~/Desktop/bi296# ln -s lab3 lab3.default
    root@860f3b529924:~/Desktop/bi296# ls
    lab3  lab3.copy  lab3.default
    ```

通过上面的练习，尝试总结一下文件、目录的各种权限的作用，以及硬链接和符号链接的区别，尤其是索引节点也就是i结点的意义。

![image-20210319152325803](C:\Users\18113\AppData\Roaming\Typora\typora-user-images\image-20210319152325803.png)

文件储存在硬盘上，硬盘的最小存储单位叫做"扇区"（Sector）。每个扇区储存512字节（相当于0.5KB）。    

操作系统读取硬盘的时候，不会一个个扇区地读取，这样效率太低，而是一次性连续读取多个扇区，即一次性读取一个"块"（block）。这种由多个扇区组成的"块"，是文件存取的最小单位。"块"的大小，最常见的是4KB，即连续八个 sector组成一个 block。    

文件数据都储存在"块"中，那么很显然，我们还必须找到一个地方储存文件的元信息，比如文件的创建者、文件的创建日期、文件的大小等等。这种储存文件元信息的区域就叫做`inode`，中文译名为"索引节点"。 

### 二、用户管理

1. 用`sudo useradd`命令为你的系统增加另一个用户，确保该用户与你（非root）在同一个组。

   ```bash
   root@860f3b529924:~/Desktop# useradd -d /home/zhangsan -s /bin/bash zhangsan
   ```

2. 这样你能在新用户的家目录下写文件或者修改该用户的文件么？为什么？你认为这样用户的隔离性是合理的么？

   不可以进行修改。不拥有权限，如此可以保护每一个用户的隐私。

   ```bash
   root@860f3b529924:/# ls
   bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
   boot  etc  lib   media  opt  root  sbin  sys  usr
   root@860f3b529924:/# cd home/
   root@860f3b529924:/home# ls
   root@860f3b529924:/home# 
   ```

3. 你能修改这个用户的文件的权限么？有哪些用户是可以修改权限的？

   可以修改，root用户和系统用户拥有权限。

4. `useradd`命令在执行的时候需要用到系统的设置如`/etc/default/useradd`文件和`/etc/skel`目录的信息，这样你在创建文件的时候才能给这个用户设置一些默认的参数。查看这两个文件，并尝试说说其中包含哪些信息。

   ```bash
   root@860f3b529924:~# cat /etc/default/useradd
   # Default values for useradd(8)
   #
   # The SHELL variable specifies the default login shell on your
   # system.
   # Similar to DHSELL in adduser. However, we use "sh" here because
   # useradd is a low level utility and should be as general
   # as possible
   SHELL=/bin/sh
   #
   # The default group for users
   # 100=users on Debian systems
   # Same as USERS_GID in adduser
   # This argument is used when the -n flag is specified.
   # The default behavior (when -n and -g are not specified) is to create a
   # primary user group with the same name as the user being added to the
   # system.
   # GROUP=100
   #
   # The default home directory. Same as DHOME for adduser
   # HOME=/home
   #
   # The number of days after a password expires until the account 
   # is permanently disabled
   # INACTIVE=-1
   #
   # The default expire date
   # EXPIRE=
   #
   # The SKEL variable specifies the directory containing "skeletal" user
   # files; in other words, files such as a sample .profile that will be
   # copied to the new user's home directory when it is created.
   # SKEL=/etc/skel
   #
   # Defines whether the mail spool should be created while
   # creating the account
   # CREATE_MAIL_SPOOL=yes
   ```

   Shell变量是指定创建用户时默认shell，GROUP变量时指定默认或首个组的编号。HOME是指定默认家目录，INACTIVE是指定用户密码过期时间。EXPIRE是指定默认用户过期时间。

   SKEL：

   skel是skeleton的缩写，意为骨骼、框架。故此目录的作用是在建立新用户时，用于初始化用户根目录。系统会将此目录下的所有文件、目录都复制到新建用户的根目录，并且将用户属主与用户组调整为与此根目录相同。所以可将用户配置文件预置到/etc/skel目录下，比如说.bashrc、.profile与.vimrc等

### 三、文件查找

1. 在目录`/usr/bin`下用`find`命令查找所有用户都具有读、写和执行权限的文件，并输出这些文件的信息。

   ```bash
   root@860f3b529924:/usr/bin# find . -perm 777 -print >a.txt
   ```

2. 从目录`/var/log`中搜索修改时间超过一个月的文件，并把这些文件备份到`lab3/log`目录中，然后给这个目录打包成`tar.gz`、`tar.bz2`和`tar.xz`等类型的文件后，删除`lab3/log`这个目录。

   首先将find的结果重定向至`back.txt`

   ```bash
   find /var/log -mtime +31 -print >/root/Desktop/bi296/lab3/log/back.txt
   ```

   ![image-20210320153205331](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\lab4.assets\image-20210320153205331.png)

   之后利用Shell脚本移动这些文件至`log`文件夹。

   ```bash
   #!/bin/bash
   cat back.txt | while read line
   do
   echo $line
   cp $line /root/Desktop/bi296/lab3/log
   done
   ```

   ![image-20210320154046029](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\lab4.assets\image-20210320154046029.png)

   打包目录：

   ```bash
   tar -cjvf log.tar.bz2 ./log
   ```

   ![image-20210320154540558](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\lab4.assets\image-20210320154540558.png)

3. 这些可以通过`find`命令的`-exec`或`-ok`选项实现，也可以通过管道发送给`xargs`实现。你可以仔细查看`man find`和`man xargs`。

   ```bash
   #exec方法
   find /var/log -mtime -31 -exec cp -r {} /root/Desktop/bi296/lab3/log \;
   ```

   注意其中`{}`表示搜索到文件，`\;`为转义结束符。

4. 搜索文件的时候，有时候可用文件名进行搜索，这时候常常会用到通配符（wildcard characters）。通配符主要包含这些：
  - `?`：表示单个字符

  - `*`：表示任意个字符
    搜索`/usr/include`目录下的所有头文件并将其输出。

  ```bash
  find /usr/include -name *.h -print > /root/Desktop/file.txt
  ```

  ![image-20210320160540390](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\lab4.assets\image-20210320160540390.png)
5. 某些文件中可能会包含一些特殊字符（比如空格），在输入的时候必须注意用反斜杠符号（backslash）进行转义，例如`\ `表示空格等，或者加入引号也是可以的，如`"program files"`。

### 四、文件操作

1. 用`cat`、`less`和`more`分别查看文件`/etc/passwd`，可以看到这个文件包含了系统的所有用户信息，比如用户名、用户ID、用户的组ID、用户的别名、用户的家目录、用户默认的shell等信息。每个用户一行，每个信息一列，相互之间用`:`分隔。

   ![image-20210320160702772](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\lab4.assets\image-20210320160702772.png)

   ![image-20210320160728346](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\lab4.assets\image-20210320160728346.png)

2. 对于具有固定分隔符号的文件，我们常常用`cut`命令进行一些基本处理。例如你能否提取本系统中所有用户的用户名，并将其保存到一个文件`lab3/user.list`中。

   ![image-20210320161226867](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\lab4.assets\image-20210320161226867.png)

3. 如何查看`/etc/passwd`的前5个用户，最后5个用户，15-20行的用户？可以用`head`与`tail`通过管道（pipe）进行结合。

   查询15-20行：

   ```bash
   sed -n '15,20p' /etc/passwd | cut -d : -f 1 > ./bi296/lab3/user2.list
   ```

   查询最后5行：

   ```bash
   tail -n 5 /etc/passwd | cut -d : -f 1 > ./bi296/lab3/user2.list
   ```

4. 一个文件同时有几个时间戳：atime, ctime, mtime。这几个时间戳分别代表什么含义，默认`ls -l`输出的是哪个时间戳？如何改变其输出？而`touch`命令修改的是哪个时间戳？

一个文件一共存在三个时间戳，分别为atime，mtime，ctime，具体解释如下：

- atime(access time):最近访问内容的时间
- mtime(modify time):最近修改内容的时间
- ctime(change time):最近更改文件的时间，包括文件名、大小、内容、权限、属主、属组等。

```bash
#ls -l输出为mtime
root@860f3b529924:~/Desktop# ls -l Test2
-rw-r--r-- 1 root root 68 3\u6708  20 17:11 Test2
root@860f3b529924:~/Desktop# stat Test2
  File: Test2
  Size: 68        	Blocks: 8          IO Block: 4096   regular file
Device: 88h/136d	Inode: 12315       Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2021-03-20 17:09:36.763369800 +0800
Modify: 2021-03-20 17:11:03.443369800 +0800
Change: 2021-03-20 17:14:19.023369800 +0800
 Birth: -
```

可以使用`time-style`参数改变`ls -l`的输出。

```bash
root@860f3b529924:~/Desktop# ls -l --time-style '+%Y/%m/%d %H:%M:%S' Test2
-rw-r--r-- 1 root root 68 2021/03/20 17:15:21 Test2
```


```bash
#touch将修改所有time
root@860f3b529924:~/Desktop# stat Test2
  File: Test2
  Size: 68        	Blocks: 8          IO Block: 4096   regular file
Device: 88h/136d	Inode: 12315       Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2021-03-20 17:09:36.763369800 +0800
Modify: 2021-03-20 17:11:03.443369800 +0800
Change: 2021-03-20 17:14:19.023369800 +0800
 Birth: -
root@860f3b529924:~/Desktop# touch Test2
root@860f3b529924:~/Desktop# stat Test2
  File: Test2
  Size: 68        	Blocks: 8          IO Block: 4096   regular file
Device: 88h/136d	Inode: 12315       Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2021-03-20 17:15:21.693369800 +0800
Modify: 2021-03-20 17:15:21.693369800 +0800
Change: 2021-03-20 17:15:21.693369800 +0800
 Birth: -
```



### 五、帮助信息和`vim`的使用

1. 用`tree`命令获取根目录/下的两层文件目录结构，将其重定向到一个文件`lab3/directory_tree`中。

   ![image-20210320171950889](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\lab4.assets\image-20210320171950889.png)

2. 从搜索引擎获取关于bioinformatics在wikipedia上的页面，将其源码用vim命令编辑为bioinformatics，看看在vim下如何跳转到末行，首行，行首、行末，如何在光标行下一行插入，如何复制5行，删除10行，查找bioinformatics的字符，把bioinformatics替换为bioinfo science。

   使用`^`和`$`进行跳转至行首和行尾。

   使用`o`在光标下一行插入文字。

   `5yy`为复制光标后五行，`10dd`为删除光标后十行。

   替换命令

   ```
   :g/bioinformatics/ s /bioinformatics science/ g
   ```

3. 用`man hier`获取每个目录的基本信息，将其用`vim`写入文件`directory_tree`中。

   ```bash
   root@860f3b529924:~/Desktop# man hier >> ./bi296/lab3/directory_tree 
   ```

4. 用`man builtins`获取所有bash内置命令的列表，将其写入文件`lab3/builtins，将其修改为每行包含一个命令的记录，仅保留每个命令的功能信息。

   ```bash
   man builtins > ./lab3/builtins
   ```

   再使用vim删除命令介绍后所有行即可。

5. 用命令`ip`或者`ifconfig`查看当前系统的网络配置信息，并将其存入文件`lab3/network.info`中。

   ```bash
   root@860f3b529924:~/Desktop/bi296/lab3# ifconfig > network.info
   ```

6. 把你当前的日期信息写入文件`lab3/finished.log`中。

![image-20210320174002159](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\lab4.assets\image-20210320174002159.png)

### 六、YUM的使用

1. 用`su - root`命令切换到root，并手动配置`yum`交大的镜像源文件`/etc/yum.repos.d/sjtu.repo`：

   ```
   [base]
   name=CentOS-7 - Base
   baseurl=http://ftp.sjtu.edu.cn/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey= http://ftp.sjtu.edu.cn/centos/RPM-GPG-KEY-CentOS-7
   
   [update]
   name=CentOS-7 - Updates
   baseurl= http://ftp.sjtu.edu. cn/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey= http://ftp.sjtu.edu.cn/centos/RPM-GPG-KEY-CentOS-7
   
   [extras]
   name=CentOS-7 - Extras
   baseurl= http://ftp.sjtu.edu. cn/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey= http://ftp.sjtu.edu.cn/centos/RPM-GPG-KEY-CentOS-7
   
   [centosplus]
   name=CentOS-7 - Plus
   baseurl= http://ftp.sjtu.edu. cn/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey= http://ftp.sjtu.edu.cn/centos/RPM-GPG-KEY-CentOS-7
   ```

   注释：\$releasever——发行版的版本；\$arch —— cpu体系；\$basearch —— cpu的基本体系。

   - 然后执行`yum update`更新镜像源的缓存
   - 运行`yum install epel-release`，这里的EPEL（Extra Packages for Enterprise Linux）是由Fedora推出的项目，其中包含许多软件包
   - `yum install wget`安装`wget`工具，这是常用的在线下载工具。
   - 用`yum --help`查看`yum`的帮助信息

2. 用`yum`安装`figlet`软件包，并用`man`查看其帮助信息，尝试用其在命令行下写下下面的图形文字：

```
  _     _       _        __                            _   _
 | |__ (_) ___ (_)_ __  / _| ___  _ __ _ __ ___   __ _| |_(_) ___ ___
 | '_ \| |/ _ \| | '_ \| |_ / _ \| '__| '_ ` _ \ / _` | __| |/ __/ __|
 | |_) | | (_) | | | | |  _| (_) | |  | | | | | | (_| | |_| | (__\__ \
 |_.__/|_|\___/|_|_| |_|_|  \___/|_|  |_| |_| |_|\__,_|\__|_|\___|___/
```

![image-20210320174139118](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\lab4.assets\image-20210320174139118.png)

### 七、基因组序列文件的简单操作

1. 进入`lab3`这个目录，然后创建`genomes/Saccharomyces cerevisiae`，注意这里的目录名包含了一个空格，但是以后我们在工作中要尽量避免在文件名、目录名中出现空格或者其他一些特殊字符，尽量只在文件名中出现字母、数值和下划线。

   ![image-20210320174304308](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\lab4.assets\image-20210320174304308.png)

2. 进入刚创建的目录，用`wget`命令从`Ensembl`的在线ftp中下载基因组序列

   ```
   http://ftp.ensembl.org/pub/current_fasta/saccharomyces_cerevisiae/dna/Saccharomyces_cerevisiae.R64-1-1.dna.toplevel.fa.gz
   ```

   ![image-20210320174446210](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\lab4.assets\image-20210320174446210.png)

3. 这个文件是用`gzip`压缩的`FASTA`文件，你可以用`ls`查看一下下载文件的大小，输出单位为KB。

   ```bash
   root@860f3b529924:~/Desktop/bi296/lab3/genomes/Saccharomyces_cerevisiae# ls -l Saccharomyces_cerevisiae.R64-1-1.dna.toplevel.fa.gz 
   -rw-r--r-- 1 root root 3784201 12\u6708 22 07:13 Saccharomyces_cerevisiae.R64-1-1.dna.toplevel.fa.gz
   ```

4. 将改文件进行解压缩，现在文件的大小是多少？可以粗略计算一下这个gzip文件的压缩比。

   ```bash
   root@860f3b529924:~/Desktop/bi296/lab3/genomes/Saccharomyces_cerevisiae# gzip -d Saccharomyces_cerevisiae.R64-1-1.dna.toplevel.fa.gz 
   root@860f3b529924:~/Desktop/bi296/lab3/genomes/Saccharomyces_cerevisiae# ls -l Saccharomyces_cerevisiae.R64-1-1.dna.toplevel.fa 
   -rw-r--r-- 1 root root 12360704 12\u6708 22 07:13 Saccharomyces_cerevisiae.R64-1-1.dna.toplevel.fa
   ```

   压缩比：3.266397318747075

5. 可以打开文件查看一下内容，了解一下基因组序列的格式。

   ![image-20210320174819790](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\lab4.assets\image-20210320174819790.png)



## 上机作业提交

最后别忘记了把`lab3`目录打包成`lab3_yourID.tar.gz`，并删除目录lab3。

