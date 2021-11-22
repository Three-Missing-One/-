## 10.31

整合了前面几天做的思维题，进行了一次比较集中的[补题](https://vjudge.net/contest/463722)。下面陈述几个印象比较深的题目：

感觉Atcoder的题目思维都还可以，Beginner Contest的题目比较适合训练思维。

### [AtCoder - abc223_f ](https://vjudge.net/problem/AtCoder-abc223_f/origin)

括号序列的合法性问题，但是需要支持交换任意两个位置的括号，同时需要满足查找区间$[l,r]$内括号序列是否合法。

区间讨论还是优先想的线段树，可以把左括号作为1，右括号作为-1，这样区间内序列合法的条件就是：$str[l] = '(' \enspace \& \enspace str[r] = ')' \enspace \& \enspace sum[l,r] = 0 \enspace \& \enspace sum[l,r] = min$

解释一下就是：左右端点必须是$()$的匹配对，同时$[l,r]$区间和为0，还要满足$sum[l,r] \le sum[l,i] (l \le i < r)$

同时满足上述条件的为合法序列。

直接上线段树然后维护一个区间最小值就可以。



### [CodeForces - 1579F](https://vjudge.net/problem/CodeForces-1579F/origin)

这个题的话一开始猜了一个玄学的思路，认为有限次循环移位以后应该都可以成为全0序列。但是后面发现，序列中已经是0的位置，不管之后经过多少次变换，它和任何数做与运算都是0，所以我们关心的其实就是每一次变换新增多少个0，然后最后扫一遍序列看一下是不是全0即可。

我们可以把序列中原本是0的位置放在一个队列里，然后以此出队，经过$d$的循环移位之后它的位置来到了$now = (i + d) \% len$，如果此时$a[now]  =1$，我们把当前的位置的值置为0，然后把这个位置入队。



### [CodeForces - 1579E2 ](https://vjudge.net/problem/CodeForces-1579E2/origin)

各一个序列，然后按照`deque`的规则模拟以此入队，询问可以构建的序列中，逆序对最小值是多少。

其实需要比较的就是当前队内比他大的数和比他小的数的大小，如果比它大的数多就放在队头否则就放在队尾。于是想到可以使用树状数组，这里把需要维护的区间再拓展一倍，也就是$2n$的长度，这一变化是为了更好的维护区间和插入值。每一次我们比较$ask(a[i] - 1)$和$ask(2n) - ask(a[i])$的大小，然后分别执行$add(-- l, 1)$和$add(++r, 1)$，这里$r = l = n$。



## 11.1

补作业...

晚上还有答辩白天还得准备材料，晚上弄完优团的事情赶着写出入党申请书...

吐了.....

明天再写写CF



## 11.2

正常比赛.....

E和I题感觉在签到题里面算是比较难的了，基本上开出这两个在加上一道`Path`就能进前150了。

大概说一下E和I的思路：

### [E - String](https://vjudge.net/problem/HDU-6586)

基本的思路就是用队列维护每一个字母在原字符串中出现的次数，然后同时求一个后缀和数组，$sum[i][j]$表示第$i$个位置之后的字母$j$有多少个。这样的话我们对于$k$个位置，每一个位置从`a`开始放，能过放入答案序列的条件就是：

1. 放入该字母后，这个字母剩下的数量加上答案序列中已经有的数量要比最小值大。
2. 同时，对于其他字母，满足答案序列中剩余的空位可以满足最差的情况，也就是最终答案序列中的字母数量都是对应的最小值，但同时不能大于最大值。
3. 如果1，2不满足那就不存在这样的序列



### [I - Vacation](https://vjudge.net/problem/HDU-6581)

本场的真实签到题，但是实际情况却是比`Path`过的都少

问题的关键在于，我们需要确定最终的车队是以那一辆车的车速通过的。

所以我们要做的就是遍历一遍车队，用时最长的就是答案，因为这样说明最后的车队一定会接在这个车的后面，统一按照这个车的车速走。

还有一道dp + SAM...有机会看看。



## 11.6

练习了一次HDU的比赛，最后一道网络流理论模型建立得已经差不多了，结果卡在了建图上...

自己过的两题，一个是签到题，还有一个假的Fibonaci：

$\begin{drcases} dp[i] = dp[i - 1] & \text{if} & str[i] = str[i - 1] \\ dp[i] = dp[i - 1] + dp[i - 2] & \text{if} & str[i] \not= str[i - 1] \end{drcases}$



## 11.7

做了一场Edu Div2的比赛。

### [B - Groups](https://vjudge.net/problem/CodeForces-1598B)

数据比较小可以直接暴力枚举，可以发现，如果我们选了$a$和$b$这两天作为上课的时间。那么这一天有空的人数之和减去这两天都有空的人数就是$2n$。判断一下就可以了。

### [C - Delete Two Elements](https://vjudge.net/problem/CodeForces-1598C)

我感觉这个题其实放一般的Div2能到1400左右。Edu的题目确实也偏难。

如果这$n$个数的和是$sum$，平均值是$val$，如果满足存在两个数满足$\frac{sum -a -b}{n - 2} = val$，那么就有$\frac{sum}{n} = \frac{sum - a - b}{n - 2}$。于是可以得出：$\frac{a + b}{2} = \frac{sum}{n}$。所以我们要做的就是，统计一下每个数出现的次数，然后遍历一次是否存在$a+b=2\times val$的情况，注意这里统计一次之后需要做一次清零工作，不然会出现重复。同时$a_i$的范围过大，再做一次离散化。

```cpp
//This code is written by Kyrie Qi
//QQ : 601383880
//Email : cuc_qzl@cuc.edu.cn

#include <bits/stdc++.h>

#define ill __int128
#define ll long long
#define PII pair <ll,ll>
#define ull unsigned long long
#define me(a,b) memset (a,b,sizeof(a))
#define rep(i,a,b) for (int i = a;i <= b;i ++)
#define req(i,a,b) for (int i = a;i >= b;i --)
#define debug(x) cout << #x << ' ' << x << endl
#define ios std :: ios :: sync_with_stdio(false)

const double Exp = 1e-9;
const int INF = 0x3f3f3f3f;
const int inf = -0x3f3f3f3f;
const int mode = 1000000007;
const double pi = acos(-1.0);

using namespace std;

const int maxn = 2e5 + 13;
int t, n, a[maxn], b[maxn], nn;
ll sum, ans, mp[maxn << 1];
double k;
map<int, bool> flag;

inline bool cmp(int a, int b)
{
	return a < b;
}

inline void init()
{
	ans = sum = 0;
	me (mp, 0ll);
	flag.clear();
	return ;
}

int main()
{
	scanf ("%d", &t);
	while (t -- ) {
		init();
		scanf ("%d", &n);
		rep(i, 1, n) {
			scanf ("%d", &a[i]);
			flag[a[i]] = 1;
			b[i] = a[i];
			sum += a[i];
		}
		sort (a + 1, a + 1 + n, cmp);
		nn = unique(a + 1, a + 1 + n) - (a + 1);
		k = (1.0 * sum) / (1.0 * n);
		// debug(k);
		if ((k - 0.5) != (int)k && (int)k != k) {
			printf ("0\n");
			continue;
		}
		for (int i = 1;i <= n;i ++) {
			int tmp = lower_bound(a + 1, a + 1 + nn, b[i]) - a;
			mp[tmp] ++;
		}
		int tal = 2 * k;
		for (int i = 1;i <= n;i ++) {
			int k1 = lower_bound(a + 1, a + 1 + nn, b[i]) - a;
			int k2 = lower_bound(a + 1, a + 1 + nn, tal - b[i]) - a;
			// debug(k1);
			// debug(k2);
			if (!flag[tal - b[i]]) continue;
			if (k1 == k2) {
				ans += (mp[k1] * (mp[k1] - 1)) / 2; 
				mp[k1] = 0;
			} 
			else {
				ans += mp[k1] * mp[k2];
				mp[k1] = mp[k2] = 0;
			}
			// debug(ans);
		}
		printf ("%lld\n", ans);
	}
	return 0;
}

/*
1
7
8 7 4 6 2 1 7
*/
```

### [D - Training Session](https://vjudge.net/problem/CodeForces-1598D)

 退而求其次，先求不能满足题意的，最后拿总方案数减去就行。

但是这里有一个思路不好想：

对于一个二元组$<a,b>$，当它被选中的时候，存在的方案数是：$ (mp[a] - 1)\times(mp[b]-1)$

所以我们遍历一次每一个二元组，统计一次答案，最后用$\frac{n(n-1)(n-2)}{6}$减去就是正确答案。

注意这里的$mp$要开$long long$

```cpp
//This code is written by Kyrie Qi
//QQ : 601383880
//Email : cuc_qzl@cuc.edu.cn

#include <bits/stdc++.h>

#define ill __int128
#define ll long long
#define PII pair <ll,ll>
#define ull unsigned long long
#define me(a,b) memset (a,b,sizeof(a))
#define rep(i,a,b) for (int i = a;i <= b;i ++)
#define req(i,a,b) for (int i = a;i >= b;i --)
#define debug(x) cout << #x << ' ' << x << endl
#define ios std :: ios :: sync_with_stdio(false)

const double Exp = 1e-9;
const int INF = 0x3f3f3f3f;
const int inf = -0x3f3f3f3f;
const int mode = 1000000007;
const double pi = acos(-1.0);

using namespace std;

const int maxn = 2e5 + 9;
int t;
ll n;
unordered_map<int, ll> mpx, mpy;

struct Edge 
{
	int x, y;
}a[maxn];

inline void init()
{
	mpx.clear();
	mpy.clear();
	return ;
}

int main()
{
	scanf ("%d", &t);
	while (t --) {
		init();
		scanf ("%lld", &n);
		rep(i, 1, n) {
			scanf ("%d%d", &a[i].x, &a[i].y);
			mpx[a[i].x] ++;
			mpy[a[i].y] ++;
		}
		ll ans = (n * (n - 1) * (n - 2)) / 6;
		rep(i, 1, n) ans -= (mpx[a[i].x] - 1ll) * (mpy[a[i].y] - 1ll);
		printf ("%lld\n", ans);
	}
	return 0;
}
```



## 11.8 

做了一场Atcoder的比赛，感觉ATC的题目大部分都比较考验思维，做一下还是挺好的。

今天这场真的是STL专场，A了四个题然后三个是STL的，`map`套`vector`，`map`套`pair`...

也算是复习了一下STL的一些用法叭，毕竟网络赛的时候吃亏就吃在STL上。

晚上计划做一场CF，顺便把题目打印出来，准备最后一次模拟了。



## 11.9 

白天在上课，10周之后课表逐渐变满....

晚上重新补了一下CCPC网络赛的题目，尝试把STL的那个题做出来，结果交了6发全WA了....

明天再去看看



## 11.11

白天满课QAQ。

晚上补过了之前的网络赛的题目，然后写了两个题解。

感觉两个题都是好题，尤其是STL那道真的综合，二分的题目确实细节考察也到位了。

附上两个题解：

[Plants vs. Zombies 解题报告](https://blog.csdn.net/weixin_45671402/article/details/121276765?spm=1001.2014.3001.5502)

[Monopoly 解题报告](https://blog.csdn.net/weixin_45671402/article/details/121276326?spm=1001.2014.3001.5502)



## 11.16

上午实验课

下午数据库

晚上做了最近的一场Div2，明天下午机房不能来了，考虑一下去哪儿做题。



## 11.17

上午计网实验

下午学了一下博弈的知识点，然后做了一场Div2

晚上去上课



## 11.18

白天满课

晚上把之前吕队讲课的博弈论题目做了一些，还没咋做完。



## 11.19

上午电视原理课，下午数据库的老师给我们单独补了一节课.....又是一天没了。

晚上去打了会儿球，七点多到的机房，看了一下阶梯博弈的证明，然后做了之前的一道相关的习题，有点困早早就撤了。



## 11.20

由于没有抢上lib的位置，在咖啡厅呆了一天。

博弈论的题目结束了，然后打了div2 + div3，练手速。

晚上大创组开会。


## 11.21

从上午开始到下午五点做沈阳站的志愿者。

边看场子边看题。

然后看了一下题解，`J`的解法确实是枚举暴力，从`a`状态转到`b`状态的转法显然可以有两种：逆时针和顺时针，而且可以贪心认为我们只能一次转到位，也就是不会出现转上去再拧回来的情况，于是我们把每一个位置可能发生的变化枚举一下，就是$2^{4} = 16$种可能，统计每一种的转动次数，取最小即可。但是好像现场的两只队伍忘了0的问题：**0可以不转，也可以转一周。**
