# Basic Dynamic Programming

## 1. 数字三角形问题

<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Dynamic Programming.assets\image-20210426112114147.png" alt="image-20210426112114147" style="zoom:50%;" />![image-20210426112142433](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Dynamic Programming.assets\image-20210426112142433.png)

每次只能走临近的两个位置。

思路一：递归方法

<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Dynamic Programming.assets\image-20210426112235598.png" alt="image-20210426112235598" style="zoom:67%;" />

```c
#include <stdio.h>
int D[100][100];
int n;
int max(int x, int y)
{
    if (x >= y)
        return x;
    else
        return y;
}
int Maxnum(int i, int j)
{
    if (i == n)
        return D[i][j];
    else
        return(max(Maxnum(i+1,j),Maxnum(i+1,j+1))+D[i][j]);
}
```

由于采用了递归的方法，在遍历数字三角形过程中涉及大量重复计算，时间复杂度为$O(n^2)$

<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Dynamic Programming.assets\image-20210426112621310.png" alt="image-20210426112621310" style="zoom:50%;" />

思考可以通过每次保存得到的$Maxnum(i,j)$，重复使用时直接调用即可避免重复计算。

```c
#include <stdio.h>
int D[100][100];
int MaxSum[100][100];
int n;
int max(int x, int y)
{
    if (x >= y)
        return x;
    else
        return y;
}
int Maxnum(int i, int j)
{
    if (MaxSum[i][j] != -1)
        return MaxSum[i][j];
    if (i == n)
       MaxSum[i][j] = D[i][j];
    else
        return(max(Maxnum(i+1,j),Maxnum(i+1,j+1))+D[i][j]);
}

int main()
{
    int i,j;
    scanf("%d",&n);
    for (i=1; i<=n; ++i)
        for (j=1; j<=n; ++j)
        {
            scanf("%d",&D[i][j]);
            MaxSum[i][j] = -1;
        }
    printf("%d\n",MaxSum[1][1]);
}

```

思路二：动态规划

```c
#include <stdio.h>

int max(int x, int y)
{
    if (x >= y)
        return x;
    else
        return y;
}

int main()
{
    int D[100][100];
    int MaxSum[100][100];
    int i,j,n;
    scanf("%d",&n);
    for (i=1; i<=n; ++i)
        for (j=1; j<=n; ++j)
        {
            scanf("%d",&D[i][j]);
        }
    //Initial Condition VITAL!!
    for (j=1; j<=n; ++j)
        MaxSum[n][j] = D[n][j];
    //Back forward caculation
    for (i=n-1; i>=1; --i)
        for (j=1; j<=i; ++j)
            //Optimization of Subquestion
            MaxSum[i][j] = max(MaxSum[i+1][j],MaxSum[i+1][j+1]) + D[i][j];
    printf("Result is: %d\n",MaxSum[1][1]);
    return 0;
}

```

## 2. 最小硬币数

<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Dynamic Programming.assets\image-20210426151500732.png" alt="image-20210426151500732" style="zoom:50%;" />

解决这个问题可以从结尾开始思考，即拼凑到27元的最后一步是什么？

假设$f(x)$表示$x$元所需的最小硬币数量，那么应该有：

$f(27) = f(27-a) + a$

$a$是最后一步所使用的硬币面额，在这个问题中$a$可以是2，5或7，为了让每一个子问题为最优解，那么$f(27)$应该为：

$f(27) = min(f(27-2), f(27-5), f(27-7)) + 1$

一般而言有如下公式：

$f(x) =  min(f(x-2), f(x-5), f(x-7)) + 1$

按照上文所介绍，这里可以使用递归进行求解，边界条件是$f(0) = 0$和$f(k) = ∞$。k是无法通过2，5，7拼凑出的面额。递归所耗费时间较长，这里使用动态规划的方法求解。

注意这里存在的边界条件：

<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Dynamic Programming.assets\image-20210426152955764.png" alt="image-20210426152955764" style="zoom:50%;" />

初始条件是：$f(0) = 0$

```c
#include <stdio.h>
//Coin value
int coin[3] = {2,5,7};
int min(int x, int y)
{
    if (x<=y)
        return x;
    else 
        return y;
}
int main()
{
    int i,j,TotalValue;
    int C[1000];
    int Infinite = 99999;
    scanf("%d",&TotalValue);
    //Rudimentary condition
    C[0] = 0;
    //Initizing array
    for (i=0; i<=TotalValue; ++i)
        C[i] = Infinite;
    for (i=0; i<=TotalValue; ++i)
    {
        //traversal of three coin
        for(j=0; j<3; ++j)
        {
            if (i-coin[j] >=0 && C[i-coin[j]] != Infinite) //Subvalue greater than zero and unequal to Infinite
                C[i] = min(C[i],C[i-coin[j]]+1);
        }
    }
    if (C[TotalValue] == Infinite)
        printf("Error!\n");
    else
        printf("Result is %d\n",C[TotalValue]);
        
}
```

Testing Result:

```bash
(base) root@860f3b529924:~/Desktop/Dynamic# ./program 
27
Result is 5
```

**最值类问题**动态规划求解思路：

1. 确定最后一步的状态和子问题（数字三角形中向左或向右移动，硬币问题中加2，5或7元）
2. 确定状态转移方程
3. 确定初始条件和边界情况（如数字三角形中最后一行的$MaxSum$等于其本身，硬币问题中0元和无法被凑齐的钱币数）
4. 从初始条件开始计算

## 3. 机器人移动

<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Dynamic Programming.assets\image-20210426154730761.png" alt="image-20210426154730761" style="zoom:50%;" />

机器人移动的最后一步一定是向右或者向下，如图所示：

![image-20210426154833273](C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Dynamic Programming.assets\image-20210426154833273.png)

设$f(i,j)$为移动到$i,j$位置的移动方式，那么$f(i,j) = f(i-1,j)+f(i,j-1)$

起始条件是$f(0,0) = 1$, 即移动到起点只有一种方式。

此外由于机器人只能向右或下移动，当i=0或j=0时，机器人只能从上方移动一位或左方移动一位，即只有一种移动方式，此时$f(i,j) = 1$

```c
#include <stdio.h>
int main()
{
    int m,n,i,j;
    scanf("%d",&m);
    scanf("%d",&n);
    int F[100][100];
    for(i=0; i<m; ++i)
        for(j=0; j<n; ++j)
        {
            if(i==0 || j==0 )
                F[i][j] = 1;
            else
                F[i][j] = F[i-1][j] + F[i][j-1];
        }
    printf("%d\n",F[m-1][n-1]);
    return 0;
}
```

## 4. 青蛙跳石头

<img src="C:\Users\18113\Desktop\生物计算编程语言和Linux系统\笔记\Dynamic Programming.assets\image-20210426163227710.png" alt="image-20210426163227710" style="zoom:50%;" />

同样从最后一步开始思考，假设青蛙在第$n-1$个石头上，那么上一步石头$i$满足以下条件：

* $i < n-1$
* $(n-1) - i >= a_i$
* 青蛙可以跳到石头$i$

状态f(j)表示青蛙是否可以跳到石头$j$，那么有：

$f(j) = OR( f(i) AND (i + a[i] >= j) )$

起始条件：$f(0) = True$

```c
#include <stdio.h>
int main()
{
    int A[100], n;
    int i,j;
    scanf("%d",&n);
    for(i=0; i<n; ++i)
        scanf("%d",&A[i]);
    bool F[100];
    F[0] = true;
    for(i=1; i<n; ++i)
    {
        F[i] = false;
        for(j=0; j<i; ++j)
        {
            if(F[j] && a[j]+j >=i)
            {
                F[i] = true;
                break;
            }
        }
    }
    if (F[n-1])
        printf("YES!\n");
    else
        printf("NO!\n");
    return 0;
}
```

## Reference List

1. [30分钟弄懂动态规划算法详细讲解(超详细)_sinat_19594515的博客-CSDN博客_动态规划算法](https://blog.csdn.net/sinat_19594515/article/details/102738781?utm_medium=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromMachineLearnPai2~default-2.control&dist_request_id=1332049.8628.16194045625279757&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromMachineLearnPai2~default-2.control)