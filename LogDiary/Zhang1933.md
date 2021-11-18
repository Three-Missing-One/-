# ACMLog
## 10.31

### 下午
1. Educational Codeforces Round 116 (Rated for Div. 2) C. Banknotes

&ensp;&ensp;补题之前打比赛的时候没做出来的题。大概意思就是，给你n种面额为$10^{0-9}$的钞票，然后对于固定的数量k，求不能用至少k张钞票凑出来的最小的数S。
&ensp;&ensp;先考虑对于一个数S，如何用最小的钞票张数凑出来,简单贪心可以发现并证明有一种策略的取法可以用最少的数量凑出S。然后用这种策略对于一定的k,来填满S，小于S的数值一定可以用小于等于k的钞票凑出。用反证法可以证明这种策略是对的.

2. Codeforces Round #752 (Div. 2) B. XOR Specia-LIS-t 

&ensp;&ensp;水题,这场昨天晚上打得很烂。B也能想偏,后面一直没想回来,可能应该把电脑抱到床上打，室友在耳朵旁边吵得一匹，360度杜比音效环绕,太搞心态了。
&ensp;&ensp;题目大意就是需要把一段序列分成若干部分，使得每个部分的最长严格递增子序列的长度异或和为0。
序列长度偶数的情况,一定可以，就一个数分一个序列就行。

如果为奇数长度，那么需要找到满足$a[i+1] \eaq a[i]$的i，然后把他们分出一段,可以证明,这样种情况是充分必要条件。

### 晚上

下午看了几个小时昨天晚上比赛的C题官方题解，越看越迷,最后问了下小朱，发现自己题目看错了。准备后面再补，先把这周3要做的人工智能pre弄了。找了篇讲神经网络做LeNet的文章，一晚上看了一半，后面接着看。

## 11.1
### 下午
&ensp;&ensp;把10.31没看完的一篇神经网络文章读完了，开始制作ppt,准备人工智能的pre。
### 晚上
* 做了一晚上，ppt差不多做了3/4了。预计还需要做几个小时做完,可能明天边刷点题边做ppt吧。回到宿舍把之前打的一场CF的C题补了。好吧，我是傻逼,题目之前一直没读对，正确读取题意之后，挺简单的。
* Di-visible Confusion Codeforces Round #752 (Div. 2)

&ensp;&ensp;题目大意就是给你一串数，然后当a[i]%(i+1)!=0时，可以把那个ai给删去，然后问是否可以通过这种策略来把序列中的所有数删完。

&ensp;&ensp;容易想到一个结论就是当有一个数满足有一个k可以不整除a[i]时，该序列可以,$2 \eaq k \eaq i+1$。用归纳法可以证明改结论成立。然后就好做了。

* 明天再补以下D那道可能是数论的题。
## 11.2
### 下午
### 晚上
&ensp;&ensp;上午+下午把人工智能ppt弄完了。然后组队打了一场5h的多校。好吧，没有榜，陷入到数论中去了，那个数论确实不会,后面补一下。

## 11.3
###下午：
复习了一下计算几何到凸包部分，写了到板子题,然后看了一道多校凸包好题，明白咋写了，还没开始写,明天套下板子写一下。
### 晚上
晚上把操作系统实验写完了。
## 11.4
## 下午
下午满课，操作系统
## 晚上
晚上补了之前CF的一道数学题。
[D. Moderate Modular Mode](https://codeforces.com/contest/1604/problem/D)
这道题关键是分类讨论出一个n的限制
然后开始弄编译原理了，周一截止。
## 11.5
### 下午：
下午通识课
### 晚上：
看了一晚上编译原理实验，周一截止了。
## 11.6
### 下午：
下午打了一场比赛，数论专题训练还需要加强。没看出来那道爬楼梯的题，背锅。
### 晚上：
晚上回宿舍看edg夺冠了。

## 11.7
### 下午
下午刷了几道CF数学题,开始专题练下数学,准备从1600开始一直刷数学把。
* [C. Circle of Monsters](https://codeforces.com/problemset/problem/1334/C)
    需要写出答案的公式，破环成链，用公式证明选那个位置开始最优。
* [C. Celex Update](C. Celex Update)
    关键是找出可能的最小值与最大值可以怎么走，然后可以怎么加1
### 晚上
&ensp;&ensp;准备编译原理的实验展示。
## 11.8
### 下午
编译原理
### 晚上
* [Orac and LCM](https://codeforces.com/problemset/problem/1349/A)
    质数因子分解,用lcm，gcd性质解题。
* [Yet Another Counting Problem](https://codeforces.com/problemset/problem/1342/C)
    这道题想了一个思路，写了很久,写崩了，边界条件处理，越写越复杂。最后看了下官方题解，没这么复杂,明天再补把。
## 11.9
### 上午
* [Yet Another Counting Problem](https://codeforces.com/problemset/problem/1342/C)
    确实没那么复杂，一个简单循环就行了，最开始是用自己推的一个公式算，边界实在是太难处理了
* [C2. k-LCM (hard version)](https://codeforces.com/problemset/problem/1497/C2)
    构造题,先想出最小的范围的情况怎么解，然后应用到大范围。
* [Row GCD](https://codeforces.com/problemset/problem/1458/A)
    用一个gcd的公式，注意算出gcd为负数的情况，取决对值即可
* [D. Almost All Divisors](https://codeforces.com/problemset/problem/1165/D)
    最小因数*最大因数，再判断即可

### 晚上

* [C. Kuroni and Impossible Calculation](https://codeforces.com/problemset/problem/1305/C)
    小范围暴力，大范围不用算
* [Mafia](https://codeforces.com/problemset/problem/348/A)
    有点坑,注意会整数溢出，需要特殊处理一下
    然后，就去干编译原理的实验汇报了。
## 11.10
### 上午
忘了周三上午还有课，索性懒得去了。
* [D.Maximum Sum of Products](https://codeforces.com/problemset/problem/1519/D)
    区间dp水题,注意边界判断
### 下午
组队打了场比赛,写了道大模拟的题，大模拟写崩了。
### 晚上
写操作系统作业，明天交了。

## 11.11
### 上午
[C. Mixing Water](https://codeforces.com/problemset/problem/1359/C)
    三分求奇数部分的极值点,顺便整理了一下三分板子
### 下午
上课操作系统
### 晚上
把网络安全第八章看完了
## 11.12
### 下午&晚上
整理数论板子,整了一个带目录的板子

## 11.14

&ensp;&ensp;icpc济南站，有两个问题：
* 一个问题是没意识到不能用高精度,导致浪费了很多时间。
* 还有个问题是有一个条件没用上。
## 11.15
### 下午
编译原理，然后挂了场数学思维方面的题，还没有想出来
### 晚上
 晚上看了5道题,想出了一道
* [Neko does Maths CodeForces - 1152C ](https://vjudge.net/contest/468896#problem/E)
    GCD用辗转相除法一次，然后枚举因子

## 11.16
### 下午：
* [CodeForces 988D	Points and Powers of Two]:
挂的题中,昨晚没想到,后面才想到。用反证法证明出了其实最大情况答案为3,枚举就行了。

然后整理了一个高斯消元的取模板子，之前板子有点问题。
### 晚上
补题：
* [D. Zookeeper and The Infinite Zoo]
   操作很好想到，但是一直没想到怎么归纳结论,可以压扁一些1，关键是认识到如何操作以及归纳一个结论。

* [Power Products CodeForces - 1225D ]
    最开始想的是枚举x，用Meet in the middle 的思想来搞，但是这样做会崩,当x等于2的时候，复杂度不可接受。做题的时候也想到通过质因数分解来想，但是还是没有跳出枚举x的坑，想到枚举x不可行就放弃了,应该立马换思路的。正解是质因子分解。维护一个质因子分解列表。然后map套vector套pair哈希。

## 11.17
### 下午：
昨晚看懂了思路，但还没写,下午把[Power Products CodeForces - 1225D ] 写了。挺好的题。

* [ Apollo versus Pan CodeForces - 1466E ]

    补题，感觉挺好的一道题,直接帮我巩固了一下求和公式的性质,需要推公式,涉及到求和符号的变换,根据公式预处理。虽然感觉这种题正式比赛的时候应该没有多少人会做--。

### 晚上：
    操作系统作业。
    挂了场题单，写了一个搜索，发现判断NO的情况没想到怎么优化。
