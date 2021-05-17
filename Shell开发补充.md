# 第五章：Bash脚本编程

[toc]



## 1. 学习目的

- 掌握基本的bash编程语法
- 用bash实现生物信息学分析流程



## 2. 课程脚本

### 2.1 常用shell

/etc/shells列举出常用的shell

- `sh`：Bourne SHell，纪念Steve Bourne
- `bash`：Bourne-Again SHell，结合了sh和csh、ksh的优点，事实标准
- `csh`：C-style SHell
- `ksh`：Korn SHell
- `zsh`：

### 2.2 第一个shell脚本

这里以RNAseq数据分析流程的一个脚本为例

第一行：#!/usr/bin/env bash

第二行：变量定义（definition）

第三行：数组声明与定义（array declaration and definition）



#### （1） 变量设置

- 变量定义：`var=value`，等号两端不允许空格
- 如果`value`中包含空格，则用单引号或者双引号包围字符串
- 本质上是将`value`视为字符串
- 命令输出进行赋值：`var=`\`command\` 或者 `var=$(command)`
- 交互式赋值：`read -p "Please input a value: " var`



#### （2） 类型声明

- 整型：`delare -i var`
- 数组：`declare -a var`
- 关联数组：`declare -A var`
- 只读：`declare -r var`，不可被修改，不可被unset
- 环境变量：`declare -x var`
- 函数声明：`declare -f func`



#### (3) 变量操作

- 变量扩展（variable expansion)：`${var}`或者`$var`
- 删除变量：`unset var`
- 列出所有变量：`set`
- 列出所有环境变量：`env`
- 将自定义变量转为环境变量：`export var`



#### （4） 环境变量与用户自定义变量的区别

- 继承问题：能否为子shell所继承



### 2.3 变量默认值的设置方式

| 变量设置方式       | unset str             | str=""               | str="blah"          |
| ------------------ | --------------------- | -------------------- | ------------------- |
| `var=${str-expr}`  | `var=expr`            | `var=`               | `var=$str`          |
| `var=${str:-expr}` | `var=expr`            | `var=expr`           | `var=$str`          |
| `var=${str+expr}`  | `var=`                | `var=expr`           | `var=expr`          |
| `var=${str:+expr}` | `var=`                | `var=`               | `var=expr`          |
| `var=${str=expr}`  | `str=expr;  var=expr` | `str=expr; var=""`   | `str不变；var=$str` |
| `var=${str:=expr}` | `str=expr; var=expr`  | `str=expr; var=expr` | `str不变；var=$str` |
| `var=${str?expr}`  | `expr > stderr`       | `var=""`             | `var=$str`          |
| `var=${str:?expr}` | `expr > stderr`       | `expr > stderr`      | `var=$str`          |



### 2.4 字符串的删除和取代

- 删除操作：
  - `${var#keyword}`：变量`var`从头向后非贪婪删除`keyword`
  - `${var##keyword}`：变量`var`从头向后贪婪删除`keyword`
  - `${var%keyword}`：变量`var`从后向前非贪婪删除`keyword`
  - `${var%%keyword}`：变量`var`从后向前贪婪删除`keyword`
- 替换操作
  - `${var/old/new}`：将`var`中的匹配第一个字符串`old`将会被替换为`new`
  - `${var//old//new}：将`var中的所有匹配字符串`old`替换为`new`
- 取子串操作
  - ${var:start:length}：从start位（0-based）开始取长度位length的子串

#### ### 2.5 常用特殊字符

| 符号    | 意义                                       |
| ------- | ------------------------------------------ |
| `#`     | 注释符号                                   |
| `\`     | 转义字符：将特殊符号转义为一般字符         |
| `|`     | 管道符：将前面命令的输出作为后续命令的输入 |
| `;`     | 分隔符：连续多个指令的分隔符               |
| `~`     | 用户家目录                                 |
| `$`     | 变量替换符                                 |
| `&`     | 将任务置于后台运行                         |
| `!`     | 逻辑非                                     |
| `/`     | 路径分隔符                                 |
| `>, >>` | 输出重定向符                               |
| `<, <<` | 输入重定向符                               |
| `''`    | 单引号，关闭`$`的变量替换功能              |
| `""`    | 双引号，打开`$`的变量替换功能              |
| ``      | 反引号，将命令运行结果输出，相当于`$()`    |
| `()`    | 开启子shell                                |
| `{}`    | shell代码块                                |

#### 通配符

| 通配符   | 意义                 |
| -------- | -------------------- |
| `*`      | 0+长度的任意字符     |
| `?`      | 1个任意字符          |
| `[abc]`  | 集合中的其中之一字符 |
| `[A-Z]`  | 范围内的其中之一字符 |
| `[^A-Z]` | 范围外的其中之一字符 |



### ### 2.6 条件表达式

- `[[  ]]`
- `[  ]`
- `test`
- 退出码（exit code）：`$?`为0表示正常退出（true），否则表示非正常退出（false）



### 条件检验

#### （1） 文件检验

| 条件表达式          | 意义                                     |
| ------------------- | ---------------------------------------- |
| [ -a FILE ]         | 检查文件是否存在（available）            |
| [ -b FILE ]         | 检查文件是否为块设备                     |
| [ -c FILE ]         | 检查文件是否为字符设备                   |
| [ -d FILE ]         | 检查文件是否为目录                       |
| [ -e FILE ]         | 检查文件是否存在（exist）                |
| [ -f FILE ]         | 检查文件是否为普通文件（regular）        |
| [ -g FILE ]         | 检查文件是否设置了SGID                   |
| [ -h FILE ]         | 检查文件是否为链接文件                   |
| [ -k FILE ]         | 检查文件是否设置了粘附位（StickyBit）    |
| [ -p FILE ]         | 检查文件是否为FIFO文件                   |
| [ -r FILE ]         | 检查文件是否对其可读                     |
| [ -s FILE ]         | 检查文件是否非空                         |
| [ -t FD ]           | 检查文件描述符FD是否指向终端（terminal） |
| [ -u FILE ]         | 检查文件是否设置了SUID                   |
| [ -w FILE ]         | 检查文件对其是否可写                     |
| [ -x FILE ]         | 检查文件是否对其可执行                   |
| [ FILE1 -nt FILE2 ] | 检查FILE1是否比FILE2新                   |
| [ FILE1 -ot FILE2 ] | 检查FILE1是否旧于FILE2                   |
| [ FILE1 -ef FILE2 ] | 检查两个文件是否互为硬链接               |

 #### （2）字符串的检验

| 表达式                 | 含义                                         |
| ---------------------- | -------------------------------------------- |
| [ -o OPTNAME ]         | 检查是否存在选项OPTNAME                      |
| [ -z STRING ]          | 检查字符串STRING是否为空串                   |
| [ -n STRING ]          | 检查字符串是否非空                           |
| [ STRING1 == STRING2 ] | 检查两个字符串是否相同                       |
| [ STRING1 != STRING2 ] | 检查两个字符串是否不同                       |
| [ STRING1 < STRING2 ]  | 检查字符串STRING1是否在字母顺序上限于STRING2 |
| [ STRING1 > STRING2 ]  | 检查字符串在字母顺序上的先后                 |

#### （3）数值比较

| 表达式            | 意义                     |
| ----------------- | ------------------------ |
| [ NUM1 -eq NUM2 ] | 检查两个数值是否相等     |
| [ NUM1 -ne NUM2 ] | 检查两个数值是否不同     |
| [ NUM1 -lt NUM2 ] | 检查NUM1是否小于NUM2     |
| [ NUM1 -le NUM2 ] | 检查NUM1是否小于等于NUM2 |
| [ NUM1 -gt NUM2 ] | 检查NUM1是否大于NUM2     |
| [ NUM1 -ge NUM2 ] | 检查NUM1是否大于等于NUM2 |

#### (4) 逻辑表达式

| 表达式                 | 意义         |
| ---------------------- | ------------ |
| [ ! EXPR ]             | 逻辑非       |
| [ ( EXPR ) ]           | 取表达式的值 |
| [ EXPR1 -a EXPR2 ]     | 逻辑与       |
| [ EXPR1 -o EXPR2 ]     | 逻辑或       |
| [[ EXPR1 && EXPR2 ]]   | 逻辑与       |
| [[ EXPR1 \|\| EXPR2 ]] | 逻辑或       |



## Bash特殊参数

| 参数 | 描述                                                         |
| ---- | ------------------------------------------------------------ |
| `$*` | 从`$1`开始的所有位置参数；如果放在双引号之间，则成为一个字符串："`$1c$2c...`"，其中`c`是变量`$IFS`的第一个字符；如果没有设置IFS，则用空格分隔 |
| `$@` | 扩展为从$1开始的所有位置参数；如果放在双引号之间，则成为多个字符串"\$1" "\$2" "\$3" |
| `$#` | 扩展为位置参数的个数                                         |
| `$?` | 扩展为最近执行的前端指令的退出码                             |
| `$-` | 扩展为由`set`内置命令调用时候的选项标识符（optional flags）  |
| `$$` | 扩展为当前shell的进程ID；当用`()`发起子shell的时候，扩展为当前shell的进程ID，而不是子shell的进程ID |
| `$!` | 扩展为最近执行的后台运行的命令的进程ID                       |
| `$_` | shell启动时，设置为调用shell或shell脚本的绝对路径；或者是上一个命令的最后一个参数 |



## Bash中的字符串扩展

#### 大括号扩展（brace expansion）

- `a{b,c,d}e` 扩展为`abe, ace, ade`三个字符串
- `a{1..5}f`扩展为`a1f, a2f, a3f, a4f, a5f`
- `a{1..5..2}f`扩展为`a1f, a3f, a5f`

#### 波浪符号扩展 （tilde expansion）

- `~`扩展为`$HOME`
- `~bio`扩展为用户bio的家目录
- `~+`扩展为`$PWD`
- `~-`扩展为`$OLDPWD`

#### 参数扩展（Parameter expansion）

- 直接扩展（direct expansion）：`${parameter}`
- 间接扩展（indirect expansion）：`${!parameter}`
- 使用默认值（use default values）：`${parameter:-word}`。如果parameter未设置或为空，则替换为word，否则替换为parameter的变量值。
- 赋默认值（assign default values）：`${parameter:=word}`。如果parameter未设置或为空，则将word赋值给parameter，并替换为parameter的值；否则直接替换为parameter的值。
- 显示错误（Display errors if Null or Unset）：`${parameter:?word}`。如果parameter未设置或为空，则将word写入标准错误，并退出非交互式的shell；否则用parameter替换。
- 采取替换值（Use alternative value）：`${parameter:+word}`。如果parameter为空或未设置，不发生任何替换；否则用word进行替换。

### [], [[]]和test等三种条件表达式有何区别

- `[]`不支持通配符扩展；而`[[]]`支持通配符扩展，但使用通配符的时候不要用引号：

  ```
  [ "output.png" == *.png ] && echo true || echo false # false
  [[ "output.png" == *.png ]] && echo true || echo false # true
  [[ "output.png" == "*.png" ]] && echo true || echo false # false
  ```

  

- `[]`的逻辑与和逻辑或用`-a`和`-o`；而`[[]]`的逻辑与和逻辑或则用`&&`和`||`

  ```
  [ 3 -lt 4 -a 5 -gt 4 ] && echo true || echo false # true
  [[ 3 -lt 4 -a 5 -gt 4 ]] && echo true || echo false # syntax error
  [[ 3 -lt 4 && 5 -gt 4 ]] && echo true || echo false # work: true
  ```

- `[[]]`支持算术扩展；而`[]`不支持：

  ```
  [[ 99+1 -eq 100 ]] && echo true || echo false
  [ 99+1 -eq 100 ] && echo true || echo false # -bash: [: 99+1: integer expression expanded
  ```

- `[]`如果其中存在`>`或者`<`进行字符串的比较操作，需要进行转移，否则将被视为输入输出重定向；而`[[]]`则不必转义：

  ```
  [ 3 > 4 ] && echo true || echo false  # output redirection to 4 (empty file), true
  [ 3 < 4 ] && echo true || echo false # -bash: 4: No such file or directory
  [ 3 \> 4 ] && echo true || echo false # work, false
  [ 3 \< 4 ] && echo true || echo false # work, true
  [[ 3 < 4 ]] && echo true || echo false # work, true
  [[ 3 > 4 ]] && echo true || echo false # work, false
  ```

- `[[]]` 在进行变量扩展的时候，会自动加上引号；而`[]`则不会：

  ```
  str="split word"
  [ $str == "split word" ] && echo true || echo false  # -bash: [: too many arguments 
  [ "$str" == "split word" ] && echo true || echo false # true
  [[ $str == "split word" ]] && echo true || echo false # true
  [[ "$str" == "split word" ]] && echo true || echo false # true
  ```

- `[[]]`支持正则表达式，而`[]`不支持正则表达式：

  ```
  newip='192.168.1.100'
  if [[ "$newip" =~ ^([0-9]{1,3}\.){3}[0-9]{1,3}$ ]];then
      echo 'IP address found.'
  fi
  ```

  

#### 注意

所有的操作符与操作数之间，必须用空格分隔。



### 2.7 流程控制

- if ... then ... fi

```
if [ condition ]; then
	do_something
fi
```

- if ... then ... elif ... then ... else ... fi

  ```
  if [ condition ]; then
  	do_something
  elif [ condition ]; then
  	do_elif_thing
  else
  	do_else_thing
  fi
  ```

- `case ... in A) ...;; B) ...;; *) ...;; esac`

  ```
  #!/bin/bash
  while true; do
  echo "Enter your favorite color: "
  read color
  case $color in 
  r*|R*)
    echo "Your favorite color is red."
    break
  ;;
  b*|B*)
    echo "Your favorite color is blue."
    break
  ;;
  g*|G*)
    echo "Your favorite color is green."
    break
  ;;
  *)
    echo "The color you entered is invalid."
    continue
  ;;
  esac
  done
  ```

  

- `select ... in ...`

  ```
  #!/bin/bash
  PS3="Choose your favorite dish (4 to exit): "
  select dish in "Roast Duck" "Mapo Tofu" "Sweet and Sour Ribs" "Quit"
  do
    case $dish in 
    R*)
      echo "You may be from Beijing."
      break
    ;;
    M*)
      echo "You may be from Sichuan."
      break
    ;;
    S*)
      echo "You may be from Shanghai."\
      break
    ;;
    Q*)
      break
    ;;
    *)
      echo "Let me guess."
      continue
    ;;
    esac
    break
  done
  ```

  

- `while ... do ... done`

  ```
  #!/bin/bash
  IFS=":"
  while read f1 f2 f3 f4 f5 f6 f7
  do
    if [ "$f7" == "/sbin/nologin" ]; then
      echo "$f1 cannot login to the system."
    fi
  done < /etc/passwd
  ```

- until ... do ... done

  ```
  #!/bin/bash
  x=3
  until [ $x -gt 10 ]; do
  	echo $x
  	let x++
  done
  ```

  

- `for ... in ... do ... done`

  ```
  #!/bin/bash
  declare -i j
  for i in `seq 20`
  do
    j=$i%5
    if [ $j -eq 0 ]; then
      continue
    fi
    echo $i
  done
  ```

  

- `for ((i=0; i<5; i++)) do ... done`

  ```
  #!/bin/bash
  declare -i j
  for ((i=1;i<=20;i++))
  do
    j=$i%5
    if [ $j -eq 0 ]; then
      continue
    fi
    echo $i
  done
  ```

  

- `next`

- `break`

- `exit`



#### Exercise

通过`df -h`监测硬盘的使用情况：

```
#!/bin/bash
# spaceAlert.sh: A very simple test for checking disk space.
space=`df -h | awk '{print $5}' | grep % | grep -v Use |\
	sort -n | tail -1 | cut -d "%" -f1`
alertvalue="80"
if [ "$space" -ge "$alertvalue" ]; then
  echo "At least one disk is nearly full!"
else
  echo "Disk space normal"
fi
```







### 逻辑流程

- `command1 && command2`：如果command1的$?为1，则执行command2；否则不执行command2
- `command1 || command2`：如果command1的$?为0，则执行command2；否则退出



### 2.8 数学计算

- `expr`命令

  ```
  expr 3 \& 5		# return 3
  expr 3 \& 0		# return 0
  expr 3 \| 5		# return 3
  expr 0 \| 5		# return 5
  expr 3 \< 5		# return 1
  expr 3 \> 5		# return 0
  expr 3 \>= 5	# return 0
  expr 3 \<= 5	# return 1
  expr 3 == 5		# return 0
  expr 3 + 5		# return 8
  expr 3+5			# return 3+5
  expr "3+5"		# return 3+5
  expr "3 + 5"	# return 3 + 5
  expr 3 - 5		# return -2
  expr 3 \* 5		# return 15
  expr 3 / 5		# return 0
  expr 3 % 5		# return 3
  expr 3 \*\* 5	# return 243
  expr substr depression 2 3	# return epr
  expr length depression		# return 10
  expr index depression re	# return 4
  expr match student "[stu]*" # return 3
  expr account.doc : "\(.*\)\.doc" # return account, capturing
  ```

  

- `$(())`计算

  ```
  #!/bin/bash
  for op in + - \* / % \*\*; do 
  	echo $((3 $op 5))
  done
  ```

- `let`命令

  - `let "x = x + 1"`
  - `let "x++"`
  - `let "x *= 5"`

- `bc`命令

  ```
  ## You need to preinstall bc using `yum install -y bc`
  ## compute exp(5)
  echo "scale=3; e(5)" | bc -l
  ## for more information, please refer to `man bc`
  echo "3.3*4" | bc	# 13.2
  echo "2.1^4" | bc # 19.4
  echo "scale=2; (2.1^2)^2" | bc # 19.44
  echo "scale=1; (2.1^2)^2" | bc # 19.3
  echo "scale=2; 1.5*100/2" | bc # 75.00
  # computing with function
  echo "sqrt(2)" | bc
  echo "sqrt(2)" | bc -l
  echo "a(1)" | bc -l
  pi=`echo "scale=3; 4*a(1)" | bc -l`
  ```

  

### 2.9 位置参数

```
#!/bin/bash
for i in {1..$#}; do
  echo ${!i}
done
```

#### <font color="red">位置参数练习题</font>

- 输出所有的位置参数，一行一个。
- 计算所有位置参数的乘积。
- 考虑到位置参数的个数是变动的，而且可能包含浮点数，要注意。
- 确保所有位置参数都是数值，如果包含非数值，要报错，并指明错误。

### 2.10 函数定义

在bash中，函数的定义有两种方式：

- 无关键词

  ```
  sayHello() {
  	name=${1:-xiaoming}
  	echo -e "Welcome, ${name}"
  }
  ```

- 有关键词function

  ```
  function sayHello() {
  
  }
  ```

  对于函数来说，与命令行存在相同的位置参数：

  - $0 - 函数本身
  - `$1, $2, ...` - 函数的第$i$个位置参数
  - `$#`：位置参数的个数
  - `$*,$@`：所有的位置参数以字符串和数组的形式存在

### 调用函数（call functions）

针对前面定义的`sayHello`函数，调用时候很简单：

```
sayHello John
sayHello
```

就可以了。



### 2.11 Bash小项目：查找质数

```
#!/bin/bash
# primefactors.sh
# USAGE: primefactors.sh <POSITIVE INTEGER>
# PURPOSE: Produces prime factors of a given number
####################################
# Argument checking
####################################
if [ $# -ne 1 ]; then
	echo "Usage: $0 <Positive Integer>"
	exit 1
fi
expr $1 + 1 &>/dev/null
if [ $? -ne 0 ]; then
	echo "Error for non-numerical value."
	exit 1
fi
[ $1 -lt 2 ] && echo "Value < 2 are not prime number" && exit 1
num=$1

########################################
# Functions
########################################
# script to find prime number
primenumber() {
  primenum=$1
  for ((cnt2=2;$((cnt2*cnt2))<=$primenum; cnt2++)); do
    [ $((primenum%cnt2)) -eq 0 ] && return 1
  done
  return 0
}
primefind() {
  primenumber $1 && echo "$1" && exit 0
  for ((cnt1=$2;cnt1<=$1;$cnt1++)); do
    primenumber $cnt1 && factorcheck $1 $cnt1 && break
  done
}
factorcheck()
{
  prime=$2
  newnum=$1
  remainder=$((newnum%prime))

  if [ $remainder -eq 0 ]; then
    printf "%dx" $prime
    newnum=$((newnum/prime))
    primefind $newnum 2
    return
  else
    let prime++
    primefind $newnum $prime
  fi
}

###############################################
# main
###############################################
echo -n "Prime Factor of $1: "
primefind $num 2
print "\b\n"

```





## 3. FAQ

1. 怎么判断你是在运行login shell还是non-login shell？

   A：很简单，只需要运行`echo $0`，如果返回的结果中前面包含`-`，则表明你在运行login shell，否则就是在运行non-login shell：

   ```
   PROMPT> echo $0
   -bash					# This is a login shell
   
   PROMPT> echo $0
   bash					# This is a non-login shell
   ```

   另外一种方式是在bash中运行`shopt login_shell`：

   ```
   PROMPT> shopt login_shell
   login_shell		on			# This is a login shell
   
   PROMPT> shopt login_shell
   login_shell		off		# This is a non-login shell
   ```

   在`man bash`中有

   >  A login shell is one whose first character of argument zero is  a  -,  or  one  started  with  the
   >  --login option.

2. root用户在bash中运行`su USERNAME` 与`su - USERNAME`有何不同？

   A：`su USERNAME`开启的是non-login shell，而`su - USERNAME`开启的是login shell。因此，`su USERNAME`会读取`~USERNAME/.bashrc`中的内容并执行，而`su - USERNAME`则会读取`~USERNAME/.bash_profile`中的内容并执行。

3. 在`/etc/profile`中有这么一段脚本：

   ```
   for i in /etc/profile.d/*.sh; do
   	if [ -r "$i" ]; then
   		if [ "${-#*i}" != "$-" ]; then
   			. "$i"
   		else
   			. "$i" >/dev/null 2>&1
   		fi
   	fi
   done
   ```

   能否逐行解释这个脚本的意义？

   A：

   ```
   for i in /etc/profile.d/*.sh; do				# 对于所有/etc/profile.d/*.sh文件
   	if [ -r "$i" ]; then							# 如果文件对用户可读
   		if [ "${-#*i}" != "$-" ]; then		# $-是bash的flags，查看其是否包含i（交互模式）
   			. "$i"									# 如果不是交互式的shell，则直接.执行
   		else
   			. "$i" >/dev/null 2>&1				# 否则将标准输出和标准错误重定向到黑洞文件
   		fi
   	fi
   done
   ```

   这里值得注意的有几点：

   - 用`echo $-`可以输出当前bash的flags
   - `${-#*i}`实现的是`$-`的前缀字符串`*i`的删除，所以这里用`[ "${-#*i}" != "$-" ]`来判断`$-`中是否包含`i`，从而判断是否为交互式shell
   - 在交互式shell中，需要将标准输出和标准错误重定向到`/dev/null`，以免影响脚本的运行。

4. 什么是Interactive shell？什么是Non-interactive shell？两者有何区别？

   A：在`man bash`中，是这么定义Interactive shell的：

   > An  interactive  shell is one started without non-option arguments and without the -c option whose
   > standard input and error are both connected to terminals (as  determined  by  isatty(3)),  or  one started with the -i option.

   所以，如果

   - 在命令行中运行不带参数的`bash`，启动了一个交互式的bash
   - 在命令行中运行`bash -c "command"`，启动的也是一个交互式的bash
   - 在命令行中运行`bash file.sh`，启动的是一个非交互式的bash
   - 在命令行运行`bash -i file.sh`，启动的是一个交互式的bash

5. 如果`a`是一个数组，则`${a}`是什么？正确输出数组的方式是什么？

   A：如果`a`是一个数组，则

   - `${a}`输出的结果就是其第一个元素的值，也就是`${a[0]}`，所以`${#a}`输出的是`${a[0]}`的字符长度；如果`${a[0]}`是数组，则输出数组的元素个数；
   - 如果想要完整输出数组，则需要`${a[@]}`；如果想要用IFS的第一个字符进行分隔，则需要`"${a[*]}"`，同样要输出数组的元素个数，需要采用`${#a[@]}`或者`${#a[*]}`

6. 如果一个关联数组的索引被组织为一个数组，则怎么进行间接扩展？

   A：如果key被组织为一个数组`a`，则对应的键值可以写成`${!a[@]}`或者`${!a[*]}`

7. 怎么在bash中进行浮点数的运算？

   A：大部分bash中的数学运算，包括`expr`, `$(())`还是`let`，都只支持整数的运算。如果要进行浮点数的运算，就必须用到`bc`这个命令或者计算环境。但是，如果要进行计算赋值的话，就必须通过管道实现：

   ```
   echo "scale=3; s(5)" | bc -l		# sin(5) function with 3 digits
   echo "scale=3; 4*a(1)" | bc -l	# pi
   ```

8. 在bash中怎么进行正则表达式匹配？

   A：在bash中正则表达式匹配可以通过`=~`操作符进行匹配：

   ```
   newip='192.168.1.100'
   pat="^([0-9]{1,3}\.){3}[0-9]{1,3}$"
   if [[ "$newip" =~ $pat ]];then
       echo 'IP address found.'
   fi
   ```

   这里的正则表达式是ERE，需要注意的是，[[]]中正则表达式不能加上引号，否则会导致不匹配。


