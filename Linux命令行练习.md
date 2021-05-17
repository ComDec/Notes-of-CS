# Lab 2: 初识Linux之命令行练习

- 学号：
- 姓名

[toc]

## 学习目的

1. 掌握常用的几个命令：`pwd`, `cd`, `ls`, `mkdir`, `cat`, `less`, `more`
2. 掌握查看`man`的一些简单技巧
3. 掌握编辑器`vim`的基本用法
4. 掌握将用户添加为sudoers的方法：`usermod`, `useradd`， `visudo`等





## 1. 几个常用命令的使用

先解决几个哲学问题：我在哪里？我是谁？我要到哪里去？

- 查看当前用户所在的工作路径：

  > `pwd`

  注意：所有的绝对路径都是从`/`开始的，这是所有路径的根路径（root directory）。文件目录系统就是一种典型的树状结构，有根节点就是`/`。

- 系统都有谁登录进来了？

  > `who`

- 改变当前用户的工作目录：

  > `cd`

  注意：

  1. `cd /etc`后面加上路径就是切换到该路径`/etc`
  2. `cd`后面不加参数就是切换到当前用户的家目录，每个用户都有自己的家目录，例如`root`默认为`/root`，而一般用户默认为`/home/$USERNAME`
  3. `cd ~`也是切换到当前用户的家目录，如果换一下`cd ~bio`就是切换到`bio`用户的家目录了，当然你必须有权限才可以。

- 查看目录下有什么？

  > `ls`

  注意：

  1. `ls`后面不加任何参数和选项的话，输出的就是当前工作路径下的内容的列表。
  2. `ls /etc`就是列出`/etc`目录下的内容。
  3. 观察一下，`ls -a`相比`ls`输出的内容有何区别？与`ls -a`呢？
  4. 我们再看看，`ls -l /etc`与`ls /etc`又存在什么区别？

- 新建目录：`mkdir`

- 新建文件：`touch`

- 查看文本文件的内容：`cat`，`less` 或者`more`

- 再来看一个：`uname`

好了，我们就来先简单看看这几个命令吧，把系统的命令行的学习留给下次课。

  

## 2. `man`的使用

首先我们来查看一下`man`的手册：

```bash
man man
```

了解一下怎么使用`man`。按下`q`键可以退出，按下`/keyword`可以在帮助文档中搜索关键词`keyword`。

请尝试回答一下下面的问题：

> 1. 一般的命令行手册主要分为几节？每节都有什么功能？
> 2. `man 3 printf`与`man 1 printf`有何区别？这里的数字的意义是什么？
> 3. `man -k`的功能是什么？
> 4. `man hier`可以输出文件系统层次架构中每个目录的存在意义，那么
>    - 目录`/etc`下面存放什么？
>    - `/usr/lib64`呢？`/usr/include`呢？`/usr/bin`呢？其实`usr`是`unix system resource`的缩写。
>    - `/boot`呢？
>    - `/home`呢？

另外，查看特定的命令，我们可以看到更详细的帮助信息：

```bash
man ls
```

得到的帮助信息简单如下：

```
LS(1P)                           POSIX Programmer's Manual                           LS(1P)

PROLOG
       This manual page is part of the POSIX Programmer's Manual.  The Linux implementation
       of this interface may differ  (consult  the  corresponding  Linux  manual  page  for
       details of Linux behavior), or the interface may not be implemented on Linux.

NAME
       ls - list directory contents

SYNOPSIS
       ls [-CFRacdilqrtu1][-H | -L ][-fgmnopsx][file...]
...
```

可以看到

- 第一行可以看出：`ls`命令属于`1P`节，也就是常规命令。
- 从`NAME`节可以看到该命令的作用。
- 从`SYNOPSIS`可以看到`ls`命令的语法，这里所有`[]`内的都是命令的可选项，也就是这个命令可以完全不带选项和参数。
- 后面的`OPTIONS`节可以看到每个选项的意义和用法。

回答下面的问题：

- 默认的情况下`ls`的输出结果是按照什么进行排序的？
- 如果想要按照日期进行降序排序，该加入哪些选项？
- 按照文件大小呢？
- 如果想要显示文件大小的时候单位改为其他单位，比如MB，比如KB，如何修改命令？
- ...



## 3. `vim`练习


```
## 3.1 ESC mode
___



|             Hint: if you accidentally start typing text, press Esc          
|       .-------.   to leave insert mode and u to undo any changes.           
|       |       |                                                             
\_______/       |                                                             
                |                                                             
                |                                                             
                \____.                                                        
                     |    .-----------------.                                 
                     |    |                 |                                 
           .---------+----+----.            |                                 
           \_________/    |    |            |                                 
                          |    |            |                                 
                          |    |            |                                 
                          |    \________.   \________ Well done! Now scroll   
                          |             |             down to section 1.2      
                          |             |             with j.                 
                          |             |                                     
                          \_____________/                                     
                                                                              
                                                                              
                                                                              
## 3.2 Text editing - deletion
Instructions: Correct the following lines of text below by deleting the
unneccesary characters with  x . Hint: use 0 to return to the left.

Faar ouxt inn trhe unchttarteed bakckwataers oof the unfashionable end of the 
Weeestern sSpiral arrm ohf thze Gallaxy lies ae sm*all unregarded yellow syun.

Iti ijs aaa trufth universalllly acknowledgegeged, that a sxingle manr iin 
possessibon of aw gopod fort^une muost bee in wantxxx of a) wife.&&

Int waes lourvve at first sighght.. The feeirst time Yossarian sa9w th$e 
chaplain heee fell maaadly in lov*ve with himmmm. 

Aaas Gregor Samsa awokke onne moorning frrom uneeasy ddreams hhe founnd 
himmself trransformed inn hiis bbed intoo a giiigantic insecct.

It was  a bright cold day in  April, and the clocks  were striking   thirteen.

Twwo householllds, bothe alikee inf dignityyyy,
IIIn fair% Verrrrona, whereq wiie lay# ou987r sc"ene,
Frrom ++ancient grudddge brrrreak tto #new mutiny,,,
Where7 ciiviil blo*od mak^es civil h=ands uncclean.

## 3.3 Text editing - insertion
Make the pairs of lines match up by inserting the missing text.
Hint: if you're having trouble with the escape key, try Ctrl-[ instead.


Oliver w the vim of matic course of trea decep.
Oliver was the victim of a systematic course of treachery and deception.

Now Bumble was at, and a choic;
Now, Mr. Bumble was a fat man, and a choleric;

Th as no great cons to the ch.
This was no very great consolation to the child.

'P, ir, I ome mo'
'Please, sir, I want some more.'

'We re o nction tse nture,' sd te old gntlmn
'We refuse to sanction these indentures,' said the old gentleman

ere poston of airs d n all impro.
Here the position of affairs had not at all improved.

## 3.4 Text editing - appending
Complete the lines by filling in the missing word, then pressing A 
to append to the line. Remember, return the cursor to the left with 0.


"I am afraid, my dear , that most of your conclusions we
"I am afraid, my dear Watson, that most of your conclusions were erroneous.

I laughed incredulously Sherlock Holmes leaned back in h
I laughed incredulously as Sherlock Holmes leaned back in his settee

"Mr. Holmes, they w the footprints of a gigantic h
"Mr. Holmes, they were the footprints of a gigantic hound!"

"Do you mean that wife and you wi
"Do you mean that your wife and you wish to leave?"

"I heard it distinctly, am sure that it was really the sob o
"I heard it distinctly, and I am sure that it was really the sob of a woman."


## 3.5: Deletion commands
Move the cursor to the first letter of the word to be deleted, then press
dw to delete the entire word. Rinse and repeat.


The rabbit-hole VIM went straight on like a IS tunnel for some way, and 
then GOOD dipped suddenly down, IT so suddenly that Alice had not a moment 
to WILL think MAKE about YOU stopping herself before she found herself falling 
down a very deep HAPPY well.

Either the well was GIVE very deep, or she fell very ALL slowly, for she 
had plenty of YOUR time as she went down to look about her MONEY and to 
wonder TO what was going to happen VIM next. First, she tried to look down 
and SAVE make out what THE she was coming to, but it was too dark to see 
anything; CHILDREN then she looked at the sides of the well, IN and noticed 
that UGANDA they were filled with cupboards STAY and book-shelves; here and 
there she AWAY saw maps and pictures hung FROM upon pegs. She took down a 
jar from EMACS one of the shelves as she passed; it was IT labelled 
'ORANGE MARMALADE', but WILL to her great disappointment it BREAK was empty: 
she did YOUR not like to drop the jar FINGERS for fear of killing somebody, 
so AND managed to put it into one of the cupboards THUMBS as she fell past it.

## 3.6: More deletion commands
Place the cursor on the first incorrect character of each line 
and press d$ to delete to the end of the line.



The Owl and the Pussycat went to sea[this is a bit that you need to delete]
In a beautiful pea-green boat,IT WASN'T MADE OF PEAS
They took some honey, and plenty of money,***Always important to take money***
Wrapped up in a five pound note. #Or $8.07 USD on 14 Nov 2010
The Owl looked up to the stars above,,,,,,,,,,,,,,,,,,,,,,,,,,,,,
And sang to a small guitar, /* With only 5 strings */
"O lovely Pussy! O Pussy, my love,(Owls are known to be very passionate)
What a beautiful Pussy you are, you are, you are,+++++++++++++
What a beautiful Pussy you are." {Delete me please, I don't belong here} 
Pussy said to the Owl "You elegant fowl, http://flic.kr/p/6chRZS 
How charmingly sweet you sing. @@Funfact - owls have the sweetest birdsong
O let us be married, too long we have tarried; A little TOO much tarrying
But what shall we do for a ring?" The eternal question.



## 3.7: Using a count for a motion

Try to move from the point marked 'a' to the point marked 'b' using 
one command (e.g. 3e or 2w).

       a           b
e stands for the end of the word.

                        a      b
w means word, that is, move to the next word.

                        a          b        c           d
punctation counts as a word, so this comma, and this one, count.

c                    a             b
use 0 to jump to the first character of the line.

a                                b              c
Zero One Two Three Four Five Six Seven Eight Nine Ten



## 3.8: Using a count to delete more
Delete the uppercase words using d{number}motion. To delete 2 words, use 
d2w, to delete 3, use d3w, etc. Always start on the first letter of the word.


Jack and Jill UP GOT JACK went up AND HOME DID TROT the Hill 
to fetch AS FAST AS HE COULD a pail of CAPER water
Jack HE WENT TO BED fell down and AND BOUND HIS HEAD broke his crown
And WITH VINEGAR Jill came AND BROWN PAPER tumbling after.

Old WHEN SHE GOT THERE Mother Hubbard
Went THE CUPBOARD to the WAS BARE cupboard
To SO THE POOR LITTLE DOGGIE get her poor HAD NONE doggie a bone,

Little Bo BUT WHEN SHE AWOKE peep has lost SHE FOUND IT A JOKE her sheep
And FOR THEY WERE doesn't know ALL STILL FLEETING where to find them.
Leave THEN UP SHE TOOK them alone and HER LITTLE CROOK they'll come home,
Bringing DETERMINED FOR their tails TO FIND THEM behind them.
Little Bo SHE FOUND THEM INDEED peep 
fell fast BUT IT MADE HER HEART BLEED asleep
And FOR THEY LEFT dreamt she THEIR TAILS BEHIND THEM heard them bleating,

## 3.9: Operating on lines
Delete the lines that don't belong below using dd. It's up to you to 
pick which ones don't belong!
Hint: Precede the command with a count to delete multiple lines,
e.g. 2dd - delete two lines.

I said a hip hop, hippie to the hippie 
I wandered lonely as a cloud
That floats on high o'er vales and hills,
When all at once I saw a crowd,
The hip, hip a hop, and you don't stop, a rock it
To the bang bang boogie, say, up jump the boogie
A host, of golden daffodils;
To the rhythm of the boogie, the beat
Beside the lake, beneath the trees,
Now, what you hear is not a test, I'm rapping to the beat
And me, the groove, and my friends are gonna try to move your feet
Fluttering and dancing in the breeze.




## 3.10: The undo command
Fix the errors below. Undo the corrections one-by-one with u, then try 
redoing them with CTRL-R. Also, try out undoing a whole line with U.


There    on**ce was a bu()gg$$y AI
Who d%*ecided ^^^ her su""bject & should d+ie.
W^hen t{he pl}ot w~as unc#overed,
The su|b|j|e|cted disc\o/vered
That s_ad=ly the c&ake w@as @ a l-i-e.	

There SOMEBODY once SET UP was a US man THE from BOMB deep space
And all MAIN you SCREEN could TURN see ON was his face
It ALL seemed YOUR that BASE ARE his BELONG gig TO US
Was YOU HAVE NO CHANCE to TO SURVIVE MAKE make YOUR TIME you move zig
Or FOR else he GREAT would take JUSTICE all your base

There ~once~ was (a) gal "from" [Peru]
whose #limericks# {stopped} on %%line%% two.

## 3.11: The block comment and uncomment
### Instructions:
Comment and then uncomment the following C code.
1. Press "CTRL+v" to enter the "Visual Block" mode;
2. Use "UP" or "DOWN" key to select the rows;
3. Press "I" to enter the insert mode (to uncomment, press "x" or "d")
4. Insert the comment sign "//" at the start of one line
5. Press "ESC" twice

#include <stdio.h>

int main(){
	int x = 5;
	int y[4] = {0,1,2,3};
	printf("The first one of y is %d\n", y[0]);
	printf("The length of y is %d\n", ADD HERE);
	printf("The size of x is %d\n", sizeof(x));

	return 0;
}
```

:wq 保存退出

:q! 不保存退出

i 编辑模式

o 新增行的编辑模式

esc 退回命令模式

## 4. `sudoers`

细心的同学可能已经发现，我们有时候运行一些命令如`yum`的时候，只有`root`才有权限执行，这样对于普通用户来说每次都得用`su - root`切换到`root`用户才能进一步工作，这样是一件比较繁琐的事。而用Ubuntu的同学就简单多了，可以直接使用`sudo`执行某些`root`用户才能执行的命令。那么有什么办法呢？有，我可以将某些用户设置为`sudoers`，这样就可以使用`sudo`命令了。

### 4.1 root用户用命令`visudo`修改文件`/etc/sudoers`

> 执行`visudo`的方式与`vim`的编辑方式完全一致。

将下面第二行行前面的注释`#`去掉：

```
## Same thing without a password
# %wheel    ALL=(ALL)   NOPASSWD: ALL
```

或者这里的第二行：

```
## Allows people in group wheel to run all commands
# %wheel  ALL=(ALL)   ALL
```

这表示`wheel`组的用户任何地方执行任何指令，前者不用输入用户自己的密码，后者则需要输入密码执行。



### 4.2 将用户加入`wheel`组：

```bash
usermod -a -G wheel bio
```

就是将`bio`用户加入`wheel`组。

大功告成！现在`bio`用户就可以执行`sudo`命令了。你可以自己试试看。

至于要不要输入密码，全凭你的喜好。



## 5. 学习总结