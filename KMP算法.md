# KMP算法

[字符串匹配](http://en.wikipedia.org/wiki/String_searching_algorithm)是计算机的基本任务之一。

举例来说，有一个字符串"BBC ABCDAB ABCDABCDABDE"，我想知道，里面是否包含另一个字符串"ABCDABD"？

许多算法可以完成这个任务，[Knuth-Morris-Pratt算法](http://en.wikipedia.org/wiki/Knuth–Morris–Pratt_algorithm)（简称KMP）是最常用的之一。它以三个发明者命名，起头的那个K就是著名科学家Donald Knuth。

KMP算法本身不难理解，其Next数组(失配表)的求解，特别是递归方法就稍难理解。

Next数组存储的是模式字符串某个位置前的子字符串中最大相同前后缀的字符长度。

<img src="C:\Users\18113\Desktop\KMP算法.assets\image-20210412201849939.png" alt="image-20210412201849939" style="zoom:67%;" />

如上所示，next[k]的值就是4，其代表其前0-k-1的元素中，最大前后缀长度的字符数为4，那么对于KMP算法中，模式字符串的下一个移动位置即为`j=next[k]`,即相对于原字符串向后移动`最大长度-next[k]`个长度。

基于此，我们可以使用简单的暴力搜索方法来完成`next`数组。

求解Next数组（暴力搜索）

```c++
void get_next_arr(char* t, int* next)
{
	// next[i]的求解方法是，找到从t[0]~t[i-1]的公共最长匹配前缀和后缀的长度
	next[0]=-1;  // next[0]定义为-1
	next[1]=0;	 // next[1]肯定是0
	// 只要待匹配串还没到底，都要求相应位的next[i]值
	for (int i = 2;t[i] != '\0';i++) 
	{
		int max_len=i-1;		// 最长长度为i-1
		int len,j;
		for (len = max_len;len >= 1;len--)  // 从最长的情况开始搜索
		{
			for (j = 0;j < len;j++)
			{
				if(t[j]!=t[j+i-len])  // 只要有任何一位不对应相等，那么当前len就不成立，试探下一个len 
					break;
			}
			// 如果上一个循环是正常退出，即都对应相等了，那就把当前的len赋给next[i],并不再往下试探了
			if(j==len)  
			{
				next[i]=len;
				break;
			}
		}
		if (len < 1)  // 如果len=1的情况都不成立，那next[i]肯定是0了
		{
			next[i]=0;
		}

	}
}
```

显然这种方法的时间复杂度较高，可以采用类似于KMP的思想，最大程度利用已有信息来使用递归算法实现`next`的构建。

<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\KMP算法.assets\image-20210412201849939.png" alt="image-20210412201849939" style="zoom:67%;" />

先看第一种scenario，黑色框表示已经寻找找的最大前后缀字串。需要找到下一个位置的next值，可以比较`f(n)`位置和`n`位置字符。若相同，则`next[n+1]=next[n]+1`。

<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\KMP算法.assets\image-20210412213353318.png" alt="image-20210412213353318" style="zoom:67%;" />

如果下一个位置的字符不匹配，如上的`k`位置和`j`位置。蓝条表示已经找到的最长前后缀字串。那我们的思路应当是，充分利用已经寻找到的前后缀信息，将k回溯到前面的位置，而这个位置类似于第一张图中的`f(n)`位置。

<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\KMP算法.assets\image-20210412213749225.png" alt="image-20210412213749225" style="zoom:67%;" />

即图中的k‘位置，然后再比较k’和k位置字符串即可。

在代码中实现为：`k=next[k]`并且嵌套while循环。这一步就相当于一步步缩小k前面位置“框”的大小，这就相当于在模式字符串中搜索新的字串。

> while循环是一次次移动“子字符串”以寻找到匹配的位置。

<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\KMP算法.assets\image-20210412214301871.png" alt="image-20210412214301871" style="zoom:67%;" />

求解Next数组（递归）

```c
void get_next_arr_2 (char *t, int *next)
{
    next[0] = -1;
    next[1] = 0;
    int j,k;
    for (j = 2; t[j] != '\0'; ++j)
       {
        k = next[j-1];
        if (k == -1)  //递归边界
        {
            next[j] = 0;
            continue;
        }
        else
        {
            while (t[j-1] != t[k] && k != -1)
                k = next[k]; //如果发现不相同的位置，则移动k指针到前述的最大相同前缀后面的位置。
            if(t[j-1] == t[k] && k != -1)
                next[j] = k+1; // next[j] = next[k]+1 “窗口”的下一个位置字符相同
            else
                next[j] = 0; //“窗口大小缩小为0”
        }    
    }
}
```

完整的KMP

```c
int KMPSearch (char *s, char *p, int *next)
{
  int i=0; 
  int j=0;
  int slen = strlen(s);
  int plen = strlen(p);
  while (i < slen && j < slen)
    {
     if (j == -1 || s[i] == p[j]) // j == -1 是next数组中标记的第零个位置
         {
           ++i;
	       ++j;
         } 
     else
         {
           j = next[j];
         }
    }
   if (j == plen)
      return i-j;
   else
      return -1;
}
```

Reference List：

1. http://www.ruanyifeng.com/blog/2013/05/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm.html

2. https://blog.csdn.net/qq_37174526/article/details/90141833

3. https://www.cnblogs.com/zhangtianq/p/5839909.html

4. https://blog.sengxian.com/algorithms/kmp