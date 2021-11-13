[TOC]

# 后缀数组

## 一些约定

字符串相关的定义请参考 [字符串基础](./basic.md)。

字符串下标从 $1$ 开始。

" 后缀 $i$" 代指以第 $i$ 个字符开头的后缀。

## 后缀数组是什么？

后缀数组（Suffix Array）主要是两个数组：$sa$ 和 $rk$。

其中，$sa[i]$ 表示将所有后缀排序后第 $i$ 小的后缀的编号。$rk[i]$ 表示后缀 $i$ 的排名。

这两个数组满足性质：$sa[rk[i]]=rk[sa[i]]=i$。

后缀数组示例：

[![](C:/Users/sdlwq/Desktop/Github/OI-wiki/docs/string/images/sa1.png)][2]

## 后缀数组怎么求？

### O(n^2logn) 做法

我相信这个做法大家还是能自己想到的，用 `string`+`sort` 就可以了。由于比较两个字符串是 $O(n)$ 的，所以排序是 $O(n^2\log n)$ 的。

### O(nlog^2n) 做法

这个做法要用到倍增的思想。

先对每个长度为 $1$ 的子串（即每个字符）进行排序。

假设我们已经知道了长度为 $w$ 的子串的排名 $rk_w[1..n]$（即，$rk_w[i]$ 表示 $s[i..\min(i+w-1,n)]$ 在 $\{s[x..\min(x+w-1,n)]\ |\ x\in[1,n]\}$ 中的排名），那么，以 $rk_w[i]$ 为第一关键字，$rk_w[i+w]$ 为第二关键字（若 $i+w>n$ 则令 $rk_w[i+w]$ 为无穷小）进行排序，就可以求出 $rk_{2w}[1..n]$。

倍增排序示意图：

[![](C:/Users/sdlwq/Desktop/Github/OI-wiki/docs/string/images/sa2.png)][2]

如果用 `sort` 进行排序，复杂度就是 $O(n\log^2n)$ 的。

    using namespace std;
    
    const int N = 1000010;
    
    char s[N];
    int n, w, sa[N], rk[N << 1], oldrk[N << 1];
    // 为了防止访问 rk[i+w] 导致数组越界，开两倍数组。
    // 当然也可以在访问前判断是否越界，但直接开两倍数组方便一些。
    
    int main() {
      int i, p;
    
      scanf("%s", s + 1);
      n = strlen(s + 1);
      for (i = 1; i <= n; ++i) sa[i] = i, rk[i] = s[i];
    
      for (w = 1; w < n; w <<= 1) {
        sort(sa + 1, sa + n + 1, [](int x, int y) {
          return rk[x] == rk[y] ? rk[x + w] < rk[y + w] : rk[x] < rk[y];
        });  // 这里用到了 lambda
        memcpy(oldrk, rk, sizeof(rk));
        // 由于计算 rk 的时候原来的 rk 会被覆盖，要先复制一份
        for (p = 0, i = 1; i <= n; ++i) {
          if (oldrk[sa[i]] == oldrk[sa[i - 1]] &&
              oldrk[sa[i] + w] == oldrk[sa[i - 1] + w]) {
            rk[sa[i]] = p;
          } else {
            rk[sa[i]] = ++p;
          }  // 若两个子串相同，它们对应的 rk 也需要相同，所以要去重
        }
      }
    
      for (i = 1; i <= n; ++i) printf("%d ", sa[i]);
    
      return 0;
    }
    ```

### O(nlogn) 做法

在刚刚的 $O(n\log^2n)$ 做法中，单次排序是 $O(n\log n)$ 的，如果能 $O(n)$ 排序，就能 $O(n\log n)$ 计算后缀数组了。

前置知识：[计数排序](../basic/counting-sort.md)，[基数排序](../basic/radix-sort.md)。

由于计算后缀数组的过程中排序的关键字是排名，值域为 $O(n)$，并且是一个双关键字的排序，可以使用基数排序优化至 $O(n)$。

    using namespace std;
    
    const int N = 1000010;
    
    char s[N];
    int n, sa[N], rk[N << 1], oldrk[N << 1], id[N], cnt[N];
    
    int main() {
      int i, m, p, w;
    
      scanf("%s", s + 1);
      n = strlen(s + 1);
      m = max(n, 300);
      for (i = 1; i <= n; ++i) ++cnt[rk[i] = s[i]];
      for (i = 1; i <= m; ++i) cnt[i] += cnt[i - 1];
      for (i = n; i >= 1; --i) sa[cnt[rk[i]]--] = i;
    
      for (w = 1; w < n; w <<= 1) {
        memset(cnt, 0, sizeof(cnt));
        for (i = 1; i <= n; ++i) id[i] = sa[i];
        for (i = 1; i <= n; ++i) ++cnt[rk[id[i] + w]];
        for (i = 1; i <= m; ++i) cnt[i] += cnt[i - 1];
        for (i = n; i >= 1; --i) sa[cnt[rk[id[i] + w]]--] = id[i];
        memset(cnt, 0, sizeof(cnt));
        for (i = 1; i <= n; ++i) id[i] = sa[i];
        for (i = 1; i <= n; ++i) ++cnt[rk[id[i]]];
        for (i = 1; i <= m; ++i) cnt[i] += cnt[i - 1];
        for (i = n; i >= 1; --i) sa[cnt[rk[id[i]]]--] = id[i];
        memcpy(oldrk, rk, sizeof(rk));
        for (p = 0, i = 1; i <= n; ++i) {
          if (oldrk[sa[i]] == oldrk[sa[i - 1]] &&
              oldrk[sa[i] + w] == oldrk[sa[i - 1] + w]) {
            rk[sa[i]] = p;
          } else {
            rk[sa[i]] = ++p;
          }
        }
      }
    
      for (i = 1; i <= n; ++i) printf("%d ", sa[i]);
    
      return 0;
    }
    ```

### 一些常数优化

如果你把上面那份代码交到 [LOJ #111: 后缀排序](https://loj.ac/problem/111) 上：

![](C:/Users/sdlwq/Desktop/Github/OI-wiki/docs/string/images/sa3.png)

这是因为，上面那份代码的常数的确很大。

#### 第二关键字无需计数排序

实际上，像这样就可以了：

```cpp
for (p = 0, i = n; i > n - w; --i) id[++p] = i;
for (i = 1; i <= n; ++i) {
  if (sa[i] > w) id[++p] = sa[i] - w;
}
```

意会一下，先把 $s[i+w..i+2w-1]$ 为空串（即第二关键字为无穷小）的位置放前面，再把剩下的按排好的顺序放进去。

#### 优化计数排序的值域

每次对 $rk$ 进行去重之后，我们都计算了一个 $p$，这个 $p$ 即是 $rk$ 的值域，将值域改成它即可。

#### 将 rk\[id[i]] 存下来，减少不连续内存访问

这个优化在数据范围较大时效果非常明显。

#### 用函数 cmp 来计算是否重复

同样是减少不连续内存访问，在数据范围较大时效果比较明显。

把 `oldrk[sa[i]] == oldrk[sa[i - 1]] && oldrk[sa[i] + w] == oldrk[sa[i - 1] + w]` 替换成 `cmp(sa[i], sa[i - 1], w)`，`bool cmp(int x, int y, int w) { return oldrk[x] == oldrk[y] && oldrk[x + w] == oldrk[y + w]; }`。

#### 若排名都不相同可直接生成后缀数组

考虑新的 $rk$ 数组，若其值域为 $[1,n]$ 那么每个排名都不同，此时无需再排序。

    using namespace std;
    
    const int N = 1000010;
    
    char s[N];
    int n, sa[N], rk[N], oldrk[N << 1], id[N], px[N], cnt[N];
    // px[i] = rk[id[i]]（用于排序的数组所以叫 px）
    
    bool cmp(int x, int y, int w) {
      return oldrk[x] == oldrk[y] && oldrk[x + w] == oldrk[y + w];
    }
    
    int main() {
      int i, m = 300, p, w;
    
      scanf("%s", s + 1);
      n = strlen(s + 1);
      for (i = 1; i <= n; ++i) ++cnt[rk[i] = s[i]];
      for (i = 1; i <= m; ++i) cnt[i] += cnt[i - 1];
      for (i = n; i >= 1; --i) sa[cnt[rk[i]]--] = i;
    
      for (w = 1;; w <<= 1, m = p) {  // m=p 就是优化计数排序值域
        for (p = 0, i = n; i > n - w; --i) id[++p] = i;
        for (i = 1; i <= n; ++i)
          if (sa[i] > w) id[++p] = sa[i] - w;
        memset(cnt, 0, sizeof(cnt));
        for (i = 1; i <= n; ++i) ++cnt[px[i] = rk[id[i]]];
        for (i = 1; i <= m; ++i) cnt[i] += cnt[i - 1];
        for (i = n; i >= 1; --i) sa[cnt[px[i]]--] = id[i];
        memcpy(oldrk, rk, sizeof(rk));
        for (p = 0, i = 1; i <= n; ++i)
          rk[sa[i]] = cmp(sa[i], sa[i - 1], w) ? p : ++p;
        if (p == n) {
          for (int i = 1; i <= n; ++i) sa[rk[i]] = i;
          break;
        }
      }
    
      for (i = 1; i <= n; ++i) printf("%d ", sa[i]);
    
      return 0;
    }
    ```

### O(n) 做法

在一般的题目中，常数较小的倍增求后缀数组是完全够用的，求后缀数组以外的部分也经常有 $O(n\log n)$ 的复杂度，倍增求解后缀数组不会成为瓶颈。

但如果遇到特殊题目、时限较紧的题目，或者是你想追求更短的用时，就需要学习 $O(n)$ 求后缀数组的方法。

#### SA-IS

可以参考 [诱导排序与 SA-IS 算法](https://riteme.site/blog/2016-6-19/sais.html)。

#### DC3

可以参考[\[2009\]后缀数组——处理字符串的有力工具 by. 罗穗骞][2]。

## 后缀数组的应用

### 寻找最小的循环移动位置

将字符串 $S$ 复制一份变成 $SS$ 就转化成了后缀排序问题。

例题：[「JSOI2007」字符加密](https://www.luogu.com.cn/problem/P4051)。

### 在字符串中找子串

任务是在线地在主串 $T$ 中寻找模式串 $S$。在线的意思是，我们已经预先知道知道主串 $T$，但是当且仅当询问时才知道模式串 $S$。我们可以先构造出 $T$ 的后缀数组，然后查找子串 $S$。若子串 $S$ 在 $T$ 中出现，它必定是 $T$ 的一些后缀的前缀。因为我们已经将所有后缀排序了，我们可以通过在 $p$ 数组中二分 $S$ 来实现。比较子串 $S$ 和当前后缀的时间复杂度为 $O(|S|)$，因此找子串的时间复杂度为 $O(|S|\log |T|)$。注意，如果该子串在 $T$ 中出现了多次，每次出现都是在 $p$ 数组中相邻的。因此出现次数可以通过再次二分找到，输出每次出现的位置也很轻松。

### 从字符串首尾取字符最小化字典序

例题：[「USACO07DEC」Best Cow Line](https://www.luogu.com.cn/problem/P2870)。

题意：给你一个字符串，每次从首或尾取一个字符组成字符串，问所有能够组成的字符串中字典序最小的一个。

??? note "题解"
    暴力做法就是每次最坏 $O(n)$ 地判断当前应该取首还是尾（即比较取首得到的字符串与取尾得到的反串的大小），只需优化这一判断过程即可。
    

    由于需要在原串后缀与反串后缀构成的集合内比较大小，可以将反串拼接在原串后，并在中间加上一个没出现过的字符（如 `#`，代码中可以直接使用空字符），求后缀数组，即可 $O(1)$ 完成这一判断。

??? note "参考代码"
    ```cpp
    --8<-- "docs/string/code/sa/sa_1.cpp"
    ```

## height 数组

### LCP（最长公共前缀）

两个字符串 $S$ 和 $T$ 的 LCP 就是最大的 $x$($x\le \min(|S|, |T|)$) 使得 $S_i=T_i\ (\forall\ 1\le i\le x)$。

下文中以 $lcp(i,j)$ 表示后缀 $i$ 和后缀 $j$ 的最长公共前缀（的长度）。

### height 数组的定义

$height[i]=lcp(sa[i],sa[i-1])$，即第 $i$ 名的后缀与它前一名的后缀的最长公共前缀。

$height[1]$ 可以视作 $0$。

### O(n) 求 height 数组需要的一个引理

$height[rk[i]]\ge height[rk[i-1]]-1$

证明：

当 $height[rk[i-1]]\le1$ 时，上式显然成立（右边小于等于 $0$）。

当 $height[rk[i-1]]>1$ 时：

设后缀 $i-1$ 为 $aAD$（$A$ 是一个长度为 $height[rk[i-1]]-1$ 的字符串），那么后缀 $i$ 就是 $AD$。设后缀 $sa[rk[i-1]-1]$ 为 $aAB$，那么 $lcp(i-1,sa[rk[i-1]-1])=aA$。由于后缀 $sa[rk[i-1]-1]+1$ 是 $AB$，一定排在后缀 $i$ 的前面，所以后缀 $sa[rk[i]-1]$ 一定含有前缀 $A$，所以 $lcp(i,sa[rk[i]-1])$ 至少是 $height[rk[i-1]]-1$。

简单来说：

$i-1$：$aAD$

$i$：$AD$

$sa[rk[i-1]-1]$：$aAB$

$sa[rk[i-1]-1]+1$：$AB$

$sa[rk[i]-1]$：$A[B/C]$

$lcp(i,sa[rk[i]-1])$：$AX$（$X$ 可能为空）

### O(n) 求 height 数组的代码实现

利用上面这个引理暴力求即可：

```cpp
for (i = 1, k = 0; i <= n; ++i) {
  if (k) --k;
  while (s[i + k] == s[sa[rk[i] - 1] + k]) ++k;
  ht[rk[i]] = k;  // height太长了缩写为ht
}
```

$k$ 不会超过 $n$，最多减 $n$ 次，所以最多加 $2n$ 次，总复杂度就是 $O(n)$。

## height 数组的应用

### 两子串最长公共前缀

$lcp(sa[i],sa[j])=\min\{height[i+1..j]\}$

感性理解：如果 $height$ 一直大于某个数，前这么多位就一直没变过；反之，由于后缀已经排好序了，不可能变了之后变回来。

严格证明可以参考[\[2004\]后缀数组 by. 徐智磊][1]。

有了这个定理，求两子串最长公共前缀就转化为了 [RMQ 问题](../topic/rmq.md)。

### 比较一个字符串的两个子串的大小关系

假设需要比较的是 $A=S[a..b]$ 和 $B=S[c..d]$ 的大小关系。

若 $lcp(a, c)\ge\min(|A|, |B|)$，$A<B\iff |A|<|B|$。

否则，$A<B\iff rk[a]< rk[c]$。

### 不同子串的数目

子串就是后缀的前缀，所以可以枚举每个后缀，计算前缀总数，再减掉重复。

“前缀总数”其实就是子串个数，为 $n(n+1)/2$。

如果按后缀排序的顺序枚举后缀，每次新增的子串就是除了与上一个后缀的 LCP 剩下的前缀。这些前缀一定是新增的，否则会破坏 $lcp(sa[i],sa[j])=\min\{height[i+1..j]\}$ 的性质。只有这些前缀是新增的，因为 LCP 部分在枚举上一个前缀时计算过了。

所以答案为：

$\frac{n(n+1)}{2}-\sum\limits_{i=2}^nheight[i]$

### 出现至少 k 次的子串的最大长度

例题：[「USACO06DEC」Milk Patterns](https://www.luogu.com.cn/problem/P2852)。

??? note "题解"
    出现至少 $k$ 次意味着后缀排序后有至少连续 $k$ 个后缀的 LCP 是这个子串。
    

    所以，求出每相邻 $k-1$ 个 $height$ 的最小值，再求这些最小值的最大值就是答案。
    
    可以使用单调队列 $O(n)$ 解决，但使用其它方式也足以 AC。

??? note "参考代码"
    ```cpp
    --8<-- "docs/string/code/sa/sa_2.cpp"
    ```

### 是否有某字符串在文本串中至少不重叠地出现了两次

可以二分目标串的长度 $|s|$，将 $h$ 数组划分成若干个连续 LCP 大于等于 $|s|$ 的段，利用 RMQ 对每个段求其中出现的数中最大和最小的下标，若这两个下标的距离满足条件，则一定有长度为 $|s|$ 的字符串不重叠地出现了两次。

### 连续的若干个相同子串

我们可以枚举连续串的长度 $|s|$，按照 $|s|$ 对整个串进行分块，对相邻两块的块首进行 LCP 与 LCS 查询，具体可见[\[2009\]后缀数组——处理字符串的有力工具][2]。

### 结合并查集

某些题目求解时要求你将后缀数组划分成若干个连续 LCP 长度大于等于某一值的段，亦即将 $h$ 数组划分成若干个连续最小值大于等于某一值的段并统计每一段的答案。如果有多次询问，我们可以将询问离线。观察到当给定值单调递减的时候，满足条件的区间个数总是越来越少，而新区间都是两个或多个原区间相连所得，且新区间中不包含在原区间内的部分的 $h$ 值都为减少到的这个值。我们只需要维护一个并查集，每次合并相邻的两个区间，并维护统计信息即可。

经典题目：[「NOI2015」品酒大会](https://uoj.ac/problem/131)

### 结合线段树

某些题目让你求满足条件的前若干个数，而这些数又在后缀排序中的一个区间内。这时我们可以用归并排序的性质来合并两个结点的信息，利用线段树维护和查询区间答案。

### 结合单调栈

例题：[「AHOI2013」差异](https://loj.ac/problem/2377)

??? note "题解"
    被加数的前两项很好处理，为 $n(n-1)(n+1)/2$（每个后缀都出现了 $n-1$ 次，后缀总长是 $n(n+1)/2$），关键是最后一项，即后缀的两两 LCP。
    

    我们知道 $lcp(i,j)=k$ 等价于 $\min\{height[i+1..j]\}=k$。所以，可以把 $lcp(i,j)$ 记作 $\min\{x|i+1\le x\le j, height[x]=lcp(i,j)\}$ 对答案的贡献。
    
    考虑每个位置对答案的贡献是哪些后缀的 LCP，其实就是从它开始向左若干个连续的 $height$ 大于它的后缀中选一个，再从向右若干个连续的 $height$ 不小于它的后缀中选一个。这个东西可以用 [单调栈](../ds/monotonous-stack.md) 计算。
    
    单调栈部分类似于 [Luogu P2659 美丽的序列](https://www.luogu.com.cn/problem/P2659) 以及 [悬线法](../misc/hoverline.md)。

??? note "参考代码"
    ```cpp
    --8<-- "docs/string/code/sa/sa_3.cpp"
    ```

类似的题目：[「HAOI2016」找相同字符](https://loj.ac/problem/2064)。

# Manacher

## 描述

给定一个长度为 $n$ 的字符串 $s$，请找到所有对 $(i, j)$ 使得子串 $s[i \dots j]$ 为一个回文串。当 $t = t_{\text{rev}}$ 时，字符串 $t$ 是一个回文串（$t_{\text{rev}}$ 是 $t$ 的反转字符串）。

## 更进一步的描述

显然在最坏情况下可能有 $O(n^2)$ 个回文串，因此似乎一眼看过去该问题并没有线性算法。

但是关于回文串的信息可用 **一种更紧凑的方式** 表达：对于每个位置 $i = 0 \dots n - 1$，我们找出值 $d_1[i]$ 和 $d_2[i]$。二者分别表示以位置 $i$ 为中心的长度为奇数和长度为偶数的回文串个数。换个角度，二者也表示了以位置 $i$ 为中心的最长回文串的半径长度（半径长度 $d_1[i]$，$d_2[i]$ 均为从位置 $i$ 到回文串最右端位置包含的字符个数）。

举例来说，字符串 $s = \mathtt{abababc}$ 以 $s[3] = b$ 为中心有三个奇数长度的回文串，最长回文串半径为 $3$，也即 $d_1[3] = 3$：

$$
a\ \overbrace{b\ a\ \underset{s_3}{b}\ a\ b}^{d_1[3]=3}\ c
$$

字符串 $s = \mathtt{cbaabd}$ 以 $s[3] = a$ 为中心有两个偶数长度的回文串，最长回文串半径为 $2$，也即 $d_2[3] = 2$：

$$
c\ \overbrace{b\ a\ \underset{s_3}{a}\ b}^{d_2[3]=2}\ d
$$

因此关键思路是，如果以某个位置 $i$ 为中心，我们有一个长度为 $l$ 的回文串，那么我们有以 $i$ 为中心的长度为 $l - 2$，$l - 4$，等等的回文串。所以 $d_1[i]$ 和 $d_2[i]$ 两个数组已经足够表示字符串中所有子回文串的信息。

一个令人惊讶的事实是，存在一个复杂度为线性并且足够简单的算法计算上述两个“回文性质数组”$d_1[]$ 和 $d_2[]$。在这篇文章中我们将详细的描述该算法。

## 解法

总的来说，该问题具有多种解法：应用字符串哈希，该问题可在 $O(n \log n)$ 时间内解决，而使用后缀数组和快速 LCA 该问题可在 $O(n)$ 时间内解决。

但是这里描述的算法 **压倒性** 的简单，并且在时间和空间复杂度上具有更小的常数。该算法由 **Glenn K. Manacher** 在 1975 年提出。

## 朴素算法

为了避免在之后的叙述中出现歧义，这里我们指出什么是“朴素算法”。

该算法通过下述方式工作：对每个中心位置 $i$，在比较一对对应字符后，只要可能，该算法便尝试将答案加 $1$。

该算法是比较慢的：它只能在 $O(n^2)$ 的时间内计算答案。

该朴素算法的实现如下：

```cpp
// C++ Version
vector<int> d1(n), d2(n);
for (int i = 0; i < n; i++) {
  d1[i] = 1;
  while (0 <= i - d1[i] && i + d1[i] < n && s[i - d1[i]] == s[i + d1[i]]) {
    d1[i]++;
  }

  d2[i] = 0;
  while (0 <= i - d2[i] - 1 && i + d2[i] < n &&
         s[i - d2[i] - 1] == s[i + d2[i]]) {
    d2[i]++;
  }
}
```

```python
# Python Version
d1 = [0] * n
d2 = [0] * n
for i in range(0, n):
    d1[i] = 1
    while 0 <= i - d1[i] and i + d1[i] < n and s[i - d1[i]] == s[i + d1[i]]:
        d1[i] += 1
    
    d2[i] = 0
    while 0 <= i - d2[i] - 1 and i + d2[i] < n and s[i - d2[i] - 1] == s[i + d2[i]]:
        d2[i] += 1
```

## Manacher 算法

这里我们将只描述算法中寻找所有奇数长度子回文串的情况，即只计算 $d_1[]$；寻找所有偶数长度子回文串的算法（即计算数组 $d_2[]$）将只需对奇数情况下的算法进行一些小修改。

为了快速计算，我们维护已找到的最靠右的子回文串的 **边界 $(l, r)$**（即具有最大 $r$ 值的回文串，其中 $l$ 和 $r$ 分别为该回文串左右边界的位置）。初始时，我们置 $l = 0$ 和 $r = -1$（*-1*需区别于倒序索引位置，这里可为任意负数，仅为了循环初始时方便）。

现在假设我们要对下一个 $i$ 计算 $d_1[i]$，而之前所有 $d_1[]$ 中的值已计算完毕。我们将通过下列方式计算：

- 如果 $i$ 位于当前子回文串之外，即 $i > r$，那么我们调用朴素算法。

  因此我们将连续地增加 $d_1[i]$，同时在每一步中检查当前的子串 $[i - d_1[i] \dots i + d_1[i]]$（$d_1[i]$ 表示半径长度，下同）是否为一个回文串。如果我们找到了第一处对应字符不同，又或者碰到了 $s$ 的边界，则算法停止。在两种情况下我们均已计算完 $d_1[i]$。此后，仍需记得更新 $(l, r)$。

- 现在考虑 $i \le r$ 的情况。我们将尝试从已计算过的 $d_1[]$ 的值中获取一些信息。首先在子回文串 $(l, r)$ 中反转位置 $i$，即我们得到 $j = l + (r - i)$。现在来考察值 $d_1[j]$。因为位置 $j$ 同位置 $i$ 对称，我们 **几乎总是** 可以置 $d_1[i] = d_1[j]$。该想法的图示如下（可认为以 $j$ 为中心的回文串被“拷贝”至以 $i$ 为中心的位置上）：

  $$
  \ldots\
  \overbrace{
      s_l\ \ldots\
      \underbrace{
          s_{j-d_1[j]+1}\ \ldots\ s_j\ \ldots\ s_{j+d_1[j]-1}
      }_\text{palindrome}\
      \ldots\
      \underbrace{
          s_{i-d_1[j]+1}\ \ldots\ s_i\ \ldots\ s_{i+d_1[j]-1}
      }_\text{palindrome}\
      \ldots\ s_r
  }^\text{palindrome}\
  \ldots
  $$

  然而有一个 **棘手的情况** 需要被正确处理：当“内部”的回文串到达“外部”回文串的边界时，即 $j - d_1[j] + 1 \le l$（或者等价的说，$i + d_1[j] - 1 \ge r$）。因为在“外部”回文串范围以外的对称性没有保证，因此直接置 $d_1[i] = d_1[j]$ 将是不正确的：我们没有足够的信息来断言在位置 $i$ 的回文串具有同样的长度。

  实际上，为了正确处理这种情况，我们应该“截断”回文串的长度，即置 $d_1[i] = r - i$。之后我们将运行朴素算法以尝试尽可能增加 $d_1[i]$ 的值。

  该种情况的图示如下（以 $j$ 为中心的回文串已经被截断以落在“外部”回文串内）：

  $$
  \ldots\
  \overbrace{
      \underbrace{
          s_l\ \ldots\ s_j\ \ldots\ s_{j+(j-l)}
      }_\text{palindrome}\
      \ldots\
      \underbrace{
          s_{i-(r-i)}\ \ldots\ s_i\ \ldots\ s_r
      }_\text{palindrome}
  }^\text{palindrome}\
  \underbrace{
      \ldots \ldots \ldots \ldots \ldots
  }_\text{try moving here}
  $$

  该图示显示出，尽管以 $j$ 为中心的回文串可能更长，以致于超出“外部”回文串，但在位置 $i$，我们只能利用其完全落在“外部”回文串内的部分。然而位置 $i$ 的答案可能比这个值更大，因此接下来我们将运行朴素算法来尝试将其扩展至“外部”回文串之外，也即标识为 "try moving here" 的区域。

最后，仍有必要提醒的是，我们应当记得在计算完每个 $d_1[i]$ 后更新值 $(l, r)$。

同时，再让我们重复一遍：计算偶数长度回文串数组 $d_2[]$ 的算法同上述计算奇数长度回文串数组 $d_1[]$ 的算法十分类似。

## Manacher 算法的复杂度

因为在计算一个特定位置的答案时我们总会运行朴素算法，所以一眼看去该算法的时间复杂度为线性的事实并不显然。

然而更仔细的分析显示出该算法具有线性复杂度。此处我们需要指出，[计算 Z 函数的算法](./z-func.md) 和该算法较为类似，并同样具有线性时间复杂度。

实际上，注意到朴素算法的每次迭代均会使 $r$ 增加 $1$，以及 $r$ 在算法运行过程中从不减小。这两个观察告诉我们朴素算法总共会进行 $O(n)$ 次迭代。

Manacher 算法的另一部分显然也是线性的，因此总复杂度为 $O(n)$。

## Manacher 算法的实现

### 分类讨论

为了计算 $d_1[]$，我们有以下代码：

```cpp
// C++ Version
vector<int> d1(n);
for (int i = 0, l = 0, r = -1; i < n; i++) {
  int k = (i > r) ? 1 : min(d1[l + r - i], r - i + 1);
  while (0 <= i - k && i + k < n && s[i - k] == s[i + k]) {
    k++;
  }
  d1[i] = k--;
  if (i + k > r) {
    l = i - k;
    r = i + k;
  }
}
```

```python
# Python Version
d1 = [0] * n
l, r = 0, -1
for i in range(0, n):
    k = 1 if i > r else min(d1[l + r - i], r - i + 1)
    while 0 <= i - k and i + k < n and s[i - k] == s[i + k]:
        k += 1
    d1[i] = k
    k -= 1
    if i + k > r:
        l = i - k
        r = i + k
```

计算 $d_2[]$ 的代码十分类似，但是在算术表达式上有些许不同：

```cpp
// C++ Version
vector<int> d2(n);
for (int i = 0, l = 0, r = -1; i < n; i++) {
  int k = (i > r) ? 0 : min(d2[l + r - i + 1], r - i + 1);
  while (0 <= i - k - 1 && i + k < n && s[i - k - 1] == s[i + k]) {
    k++;
  }
  d2[i] = k--;
  if (i + k > r) {
    l = i - k - 1;
    r = i + k;
  }
}
```

```python
# Python Version
d2 = [0] * n
l, r = 0, -1
for i in range(0, n):
    k = 0 if i > r else min(d2[l + r - i + 1], r - i + 1)
    while 0 <= i - k - 1 and i + k < n and s[i - k - 1] == s[i + k]:
        k += 1
    d2[i] = k
    k -= 1
    if i + k > r:
        l = i - k - 1
        r = i + k
```

### 统一处理

虽然在讲解过程及上述实现中我们将 $d_1[]$ 和 $d_2[]$ 的计算分开考虑，但实际上可以通过一个技巧将二者的计算统一为 $d_1[]$ 的计算。

给定一个长度为 $n$ 的字符串 $s$，我们在其 $n + 1$ 个空中插入分隔符 $\#$，从而构造一个长度为 $2n + 1$ 的字符串 $s'$。举例来说，对于字符串 $s = \mathtt{abababc}$，其对应的 $s' = \mathtt{\#a\#b\#a\#b\#a\#b\#c\#}$。

对于字母间的 $\#$，其实际意义为 $s$ 中对应的“空”。而两端的 $\#$ 则是为了实现的方便。

注意到，在对 $s'$ 计算 $d_1[]$ 后，对于一个位置 $i$，$d_1[i]$ 所描述的最长的子回文串必定以 $\#$ 结尾（若以字母结尾，由于字母两侧必定各有一个 $\#$，因此可向外扩展一个得到一个更长的）。因此，对于 $s$ 中一个以字母为中心的极大子回文串，设其长度为 $m + 1$，则其在 $s'$ 中对应一个以相应字母为中心，长度为 $2m + 3$ 的极大子回文串；而对于 $s$ 中一个以空为中心的极大子回文串，设其长度为 $m$，则其在 $s'$ 中对应一个以相应表示空的 $\#$ 为中心，长度为 $2m + 1$ 的极大子回文串（上述两种情况下的 $m$ 均为偶数，但该性质成立与否并不影响结论）。综合以上观察及少许计算后易得，在 $s'$ 中，$d_1[i]$ 表示在 $s$ 中以对应位置为中心的极大子回文串的 **总长度加一**。

上述结论建立了 $s'$ 的 $d_1[]$ 同 $s$ 的 $d_1[]$ 和 $d_2[]$ 间的关系。

由于该统一处理本质上即求 $s'$ 的 $d_1[]$，因此在得到 $s'$ 后，代码同上节计算 $d_1[]$ 的一样。

# z-func

author: LeoJacob, Marcythm, minghu6

约定：字符串下标以 $0$ 为起点。

对于个长度为 $n$ 的字符串 $s$。定义函数 $z[i]$ 表示 $s$ 和 $s[i,n-1]$（即以 $s[i]$ 开头的后缀）的最长公共前缀（LCP）的长度。$z$ 被称为 $s$ 的 **Z 函数**。特别地，$z[0] = 0$。

国外一般将计算该数组的算法称为 **Z Algorithm**，而国内则称其为 **扩展 KMP**。

这篇文章介绍在 $O(n)$ 时间复杂度内计算 Z 函数的算法以及其各种应用。

## 样例

下面若干样例展示了对于不同字符串的 Z 函数：

- $z(\mathtt{aaaaa}) = [0, 4, 3, 2, 1]$
- $z(\mathtt{aaabaab}) = [0, 2, 1, 0, 2, 1, 0]$
- $z(\mathtt{abacaba}) = [0, 0, 1, 0, 3, 0, 1]$

## 朴素算法

Z 函数的朴素算法复杂度为 $O(n^2)$：

```cpp
// C++ Version
vector<int> z_function_trivial(string s) {
  int n = (int)s.length();
  vector<int> z(n);
  for (int i = 1; i < n; ++i)
    while (i + z[i] < n && s[z[i]] == s[i + z[i]]) ++z[i];
  return z;
}
```

```python
# Python Version
def z_function_trivial(s):
    n = len(s)
    z = [0] * n
    for i in range(1, n):
        while i + z[i] < n and s[z[i]] == s[i + z[i]]:
            z[i] += 1
    return z
```

## 线性算法

如同大多数字符串主题所介绍的算法，其关键在于，运用自动机的思想寻找限制条件下的状态转移函数，使得可以借助之前的状态来加速计算新的状态。

在该算法中，我们从 $1$ 到 $n-1$ 顺次计算 $z[i]$ 的值（$z[0]=0$）。在计算 $z[i]$ 的过程中，我们会利用已经计算好的 $z[0],\ldots,z[i-1]$。

对于 $i$，我们称区间 $[i,i+z[i]-1]$ 是 $i$ 的 **匹配段**，也可以叫 Z-box。

算法的过程中我们维护右端点最靠右的匹配段。为了方便，记作 $[l,r]$。根据定义，$s[l,r]$ 是 $s$ 的前缀。在计算 $z[i]$ 时我们保证 $l\le i$。初始时 $l=r=0$。

在计算 $z[i]$ 的过程中：

-   如果 $i\le r$，那么根据 $[l,r]$ 的定义有 $s[i,r] = s[i-l,r-l]$，因此 $z[i]\ge \min(z[i-l],r-i+1)$。这时：
    - 若 $z[i-l] < r-i+1$，则 $z[i] = z[i-l]$。
    - 否则 $z[i-l]\ge r-i+1$，这时我们令 $z[i] = r-i+1$，然后暴力枚举下一个字符扩展 $z[i]$ 直到不能扩展为止。
-   如果 $i>r$，那么我们直接按照朴素算法，从 $s[i]$ 开始比较，暴力求出 $z[i]$。
-   在求出 $z[i]$ 后，如果 $i+z[i]-1>r$，我们就需要更新 $[l,r]$，即令 $l=i, r=i+z[i]-1$。

可以访问 [这个网站](https://personal.utdallas.edu/~besp/demo/John2010/z-algorithm.htm) 来看 Z 函数的模拟过程。

## 实现

```cpp
// C++ Version
vector<int> z_function(string s) {
  int n = (int)s.length();
  vector<int> z(n);
  for (int i = 1, l = 0, r = 0; i < n; ++i) {
    if (i <= r && z[i - l] < r - i + 1) {
      z[i] = z[i - l];
    } else {
      z[i] = max(0, r - i + 1);
      while (i + z[i] < n && s[z[i]] == s[i + z[i]]) ++z[i];
    }
    if (i + z[i] - 1 > r) l = i, r = i + z[i] - 1;
  }
  return z;
}
```

```python
# Python Version
def z_function(s):
    n = len(s)
    z = [0] * n
    l, r = 0, 0
    for i in range(1, n):
        if i <= r and z[i - l] < r - i + 1:
            z[i] = z[i - l]
        else:
            z[i] = max(0, r - i + 1)
            while i + z[i] < n and s[z[i]] == s[i + z[i]]:
                z[i] += 1
        if i + z[i] - 1 > r:
            l = i
            r = i + z[i] - 1
    return z
```

## 复杂度分析

对于内层 `while` 循环，每次执行都会使得 $r$ 向后移至少 $1$ 位，而 $r< n-1$，所以总共只会执行 $n$ 次。

对于外层循环，只有一遍线性遍历。

总复杂度为 $O(n)$。

## 应用

我们现在来考虑在若干具体情况下 Z 函数的应用。

这些应用在很大程度上同 [前缀函数](./kmp.md) 的应用类似。

### 匹配所有子串

为了避免混淆，我们将 $t$ 称作 **文本**，将 $p$ 称作 **模式**。所给出的问题是：寻找在文本 $t$ 中模式 $p$ 的所有出现（occurrence）。

为了解决该问题，我们构造一个新的字符串 $s = p + \diamond + t$，也即我们将 $p$ 和 $t$ 连接在一起，但是在中间放置了一个分割字符 $\diamond$（我们将如此选取 $\diamond$ 使得其必定不出现在 $p$ 和 $t$ 中）。

首先计算 $s$ 的 Z 函数。接下来，对于在区间 $[0,|t| - 1]$ 中的任意 $i$，我们考虑以 $t[i]$ 为开头的后缀在 $s$ 中的 Z 函数值 $k = z[i + |p| + 1]$。如果 $k = |p|$，那么我们知道有一个 $p$ 的出现位于 $t$ 的第 $i$ 个位置，否则没有 $p$ 的出现位于 $t$ 的第 $i$ 个位置。

其时间复杂度（同时也是其空间复杂度）为 $O(|t| + |p|)$。

### 本质不同子串数

给定一个长度为 $n$ 的字符串 $s$，计算 $s$ 的本质不同子串的数目。

考虑计算增量，即在知道当前 $s$ 的本质不同子串数的情况下，计算出在 $s$ 末尾添加一个字符后的本质不同子串数。

令 $k$ 为当前 $s$ 的本质不同子串数。我们添加一个新的字符 $c$ 至 $s$ 的末尾。显然，会出现一些以 $c$ 结尾的新的子串（以 $c$ 结尾且之前未出现过的子串）。

设串 $t$ 是 $s + c$ 的反串（反串指将原字符串的字符倒序排列形成的字符串）。我们的任务是计算有多少 $t$ 的前缀未在 $t$ 的其他地方出现。考虑计算 $t$ 的 Z 函数并找到其最大值 $z_{\max}$。则 $t$ 的长度小于等于 $z_{\max}$ 的前缀的反串在 $s$ 中是已经出现过的以 $c$ 结尾的子串。

所以，将字符 $c$ 添加至 $s$ 后新出现的子串数目为 $|t| - z_{\max}$。

算法时间复杂度为 $O(n^2)$。

值得注意的是，我们可以用同样的方法在 $O(n)$ 时间内，重新计算在端点处添加一个字符或者删除一个字符（从尾或者头）后的本质不同子串数目。

### 字符串整周期

给定一个长度为 $n$ 的字符串 $s$，找到其最短的整周期，即寻找一个最短的字符串 $t$，使得 $s$ 可以被若干个 $t$ 拼接而成的字符串表示。

考虑计算 $s$ 的 Z 函数，则其整周期的长度为最小的 $n$ 的因数 $i$，满足 $i+z[i]=n$。

该事实的证明同应用 [前缀函数](./kmp.md) 的证明一样。

# AC自动机

我知道，很多人在第一次看到这个东西的时侯是非常兴奋的。（别问我为什么知道）不过这个自动机啊它叫作 `Automaton`，不是 `Automation`，让萌新失望啦。切入正题。似乎在初学自动机相关的内容时，许多人难以建立对自动机的初步印象，尤其是在自学的时侯。而这篇文章就是为你们打造的。笔者在自学 AC 自动机后花费两天时间制作若干的 gif，呈现出一个相对直观的自动机形态。尽管这个图似乎不太可读，但这绝对是在作者自学的时侯，画得最认真的 gif 了。另外有些小伙伴问这个 gif 拿什么画的。笔者用 Windows 画图软件制作。

## 概述

AC 自动机是 **以 Trie 的结构为基础**，结合 **KMP 的思想** 建立的。

简单来说，建立一个 AC 自动机有两个步骤：

1. 基础的 Trie 结构：将所有的模式串构成一棵 Trie。
2. KMP 的思想：对 Trie 树上所有的结点构造失配指针。

然后就可以利用它进行多模式匹配了。

## 字典树构建

AC 自动机在初始时会将若干个模式串丢到一个 Trie 里，然后在 Trie 上建立 AC 自动机。这个 Trie 就是普通的 Trie，该怎么建怎么建。

这里需要仔细解释一下 Trie 的结点的含义，尽管这很小儿科，但在之后的理解中极其重要。Trie 中的结点表示的是某个模式串的前缀。我们在后文也将其称作状态。一个结点表示一个状态，Trie 的边就是状态的转移。

形式化地说，对于若干个模式串 $s_1,s_2\dots s_n$，将它们构建一棵字典树后的所有状态的集合记作 $Q$。

## 失配指针

AC 自动机利用一个 fail 指针来辅助多模式串的匹配。

状态 $u$ 的 fail 指针指向另一个状态 $v$，其中 $v\in Q$，且 $v$ 是 $u$ 的最长后缀（即在若干个后缀状态中取最长的一个作为 fail 指针）。对于学过 KMP 的朋友，我在这里简单对比一下这里的 fail 指针与 KMP 中的 next 指针：

1. 共同点：两者同样是在失配的时候用于跳转的指针。
2. 不同点：next 指针求的是最长 Border（即最长的相同前后缀），而 fail 指针指向所有模式串的前缀中匹配当前状态的最长后缀。

因为 KMP 只对一个模式串做匹配，而 AC 自动机要对多个模式串做匹配。有可能 fail 指针指向的结点对应着另一个模式串，两者前缀不同。

没看懂上面的对比不要急（也许我的脑回路和泥萌不一样是吧），你只需要知道，AC 自动机的失配指针指向当前状态的最长后缀状态即可。

AC 自动机在做匹配时，同一位上可匹配多个模式串。

### 构建指针

下面介绍构建 fail 指针的 **基础思想**：（强调！基础思想！基础！）

构建 fail 指针，可以参考 KMP 中构造 Next 指针的思想。

考虑字典树中当前的结点 $u$，$u$ 的父结点是 $p$，$p$ 通过字符 `c` 的边指向 $u$，即 $trie[p,c]=u$。假设深度小于 $u$ 的所有结点的 fail 指针都已求得。

1. 如果 $\text{trie}[\text{fail}[p],c]$ 存在：则让 u 的 fail 指针指向 $\text{trie}[\text{fail}[p],c]$。相当于在 $p$ 和 $\text{fail}[p]$ 后面加一个字符 `c`，分别对应 $u$ 和 $fail[u]$。
2. 如果 $\text{trie}[\text{fail}[p],c]$ 不存在：那么我们继续找到 $\text{trie}[\text{fail}[\text{fail}[p]],c]$。重复 1 的判断过程，一直跳 fail 指针直到根结点。
3. 如果真的没有，就让 fail 指针指向根结点。

如此即完成了 $\text{fail}[u]$ 的构建。

### 例子

下面放一张 GIF 帮助大家理解。对字符串 `i`  `he`  `his`  `she`  `hers` 组成的字典树构建 fail 指针：

1. 黄色结点：当前的结点 $u$。
2. 绿色结点：表示已经 BFS 遍历完毕的结点，
3. 橙色的边：fail 指针。
4. 红色的边：当前求出的 fail 指针。

![AC_automation_gif_b_3.gif](C:/Users/sdlwq/Desktop/Github/OI-wiki/docs/string/images/ac-automaton1.gif)

我们重点分析结点 6 的 fail 指针构建：

![AC_automation_6_9.png](C:/Users/sdlwq/Desktop/Github/OI-wiki/docs/string/images/ac-automaton1.png)

找到 6 的父结点 5，$\text{fail}[5]=10$。然而 10 结点没有字母 `s` 连出的边；继续跳到 10 的 fail 指针，$\text{fail}[10]=0$。发现 0 结点有字母 `s` 连出的边，指向 7 结点；所以 $\text{fail}[6]=7$。最后放一张建出来的图

![finish](C:/Users/sdlwq/Desktop/Github/OI-wiki/docs/string/images/ac-automaton4.png)

## 字典树与字典图

我们直接上代码吧。字典树插入的代码就不分析了（后面完整代码里有），先来看构建函数 `build()`，该函数的目标有两个，一个是构建 fail 指针，一个是构建自动机。参数如下：

1. `tr[u,c]`：有两种理解方式。我们可以简单理解为字典树上的一条边，即 $\text{trie}[u,c]$；也可以理解为从状态（结点）$u$ 后加一个字符 `c` 到达的状态（结点），即一个状态转移函数 $\text{trans}(u,c)$。下文中我们将用第二种理解方式继续讲解。
2. 队列 `q`：用于 BFS 遍历字典树。
3. `fail[u]`：结点 $u$ 的 fail 指针。

```cpp
// C++ Version
void build() {
  for (int i = 0; i < 26; i++)
    if (tr[0][i]) q.push(tr[0][i]);
  while (q.size()) {
    int u = q.front();
    q.pop();
    for (int i = 0; i < 26; i++) {
      if (tr[u][i])
        fail[tr[u][i]] = tr[fail[u]][i], q.push(tr[u][i]);
      else
        tr[u][i] = tr[fail[u]][i];
    }
  }
}
```

```python
# Python Version
def build():
    for i in range(0, 26):
        if tr[0][i] == 1:
            q.append(tr[0][i])
    while len(q) > 0:
        u = q[0]
        q.pop()
        for i in range(0, 26):
            if tr[u][i] == 1:
                fail[tr[u][i]] = tr[fail[u]][i]
                q.append(tr[u][i])
            else:
                tr[u][i] = tr[fail[u]][i]
```

解释一下上面的代码：build 函数将结点按 BFS 顺序入队，依次求 fail 指针。这里的字典树根结点为 0，我们将根结点的子结点一一入队。若将根结点入队，则在第一次 BFS 的时候，会将根结点儿子的 fail 指针标记为本身。因此我们将根结点的儿子一一入队，而不是将根结点入队。

然后开始 BFS：每次取出队首的结点 u（$\text{fail}[u]$ 在之前的 BFS 过程中已求得），然后遍历字符集（这里是 0-25，对应 a-z，即 $u$ 的各个子节点）：

1. 如果 $\text{trans}[u][i]$ 存在，我们就将 $\text{trans}[u][i]$ 的 fail 指针赋值为 $\text{trans}[\text{fail}[u]][i]$。这里似乎有一个问题。根据之前的讲解，我们应该用 while 循环，不停的跳 fail 指针，判断是否存在字符 `i` 对应的结点，然后赋值，但是这里通过特殊处理简化了这些代码。
2. 否则，令 $\text{trans}[u][i]$ 指向 $\text{trans}[\text{fail}[u]][i]$ 的状态。

这里的处理是，通过 `else` 语句的代码修改字典树的结构。没错，它将不存在的字典树的状态链接到了失配指针的对应状态。在原字典树中，每一个结点代表一个字符串 $S$，是某个模式串的前缀。而在修改字典树结构后，尽管增加了许多转移关系，但结点（状态）所代表的字符串是不变的。

而 $\text{trans}[S][c]$ 相当于是在 $S$ 后添加一个字符 `c` 变成另一个状态 $S'$。如果 $S'$ 存在，说明存在一个模式串的前缀是 $S'$，否则我们让 $\text{trans}[S][c]$ 指向 $\text{trans}[\text{fail}[S]][c]$。由于 $\text{fail}[S]$ 对应的字符串是 $S$ 的后缀，因此 $\text{trans}[\text{fail}[S]][c]$ 对应的字符串也是 $S'$ 的后缀。

换言之在 Trie 上跳转的时侯，我们只会从 $S$ 跳转到 $S'$，相当于匹配了一个 $S'$；但在 AC 自动机上跳转的时侯，我们会从 $S$ 跳转到 $S'$ 的后缀，也就是说我们匹配一个字符 `c`，然后舍弃 $S$ 的部分前缀。舍弃前缀显然是能匹配的。那么 fail 指针呢？它也是在舍弃前缀啊！试想一下，如果文本串能匹配 $S$，显然它也能匹配 $S$ 的后缀。所谓的 fail 指针其实就是 $S$ 的一个后缀集合。

`tr` 数组还有另一种比较简单的理解方式：如果在位置 $u$ 失配，我们会跳转到 $\text{fail}[u]$ 的位置。所以我们可能沿着 fail 数组跳转多次才能来到下一个能匹配的位置。所以我们可以用 `tr` 数组直接记录记录下一个能匹配的位置，这样就能节省下很多时间。

这样修改字典树的结构，使得匹配转移更加完善。同时它将 fail 指针跳转的路径做了压缩（就像并查集的路径压缩），使得本来需要跳很多次 fail 指针变成跳一次。

好的，我知道大家都受不了长篇叙述。上图！我们将之前的 GIF 图改一下：

![AC_automation_gif_b_pro3.gif](C:/Users/sdlwq/Desktop/Github/OI-wiki/docs/string/images/ac-automaton2.gif)

1. 蓝色结点：BFS 遍历到的结点 u
2. 蓝色的边：当前结点下，AC 自动机修改字典树结构连出的边。
3. 黑色的边：AC 自动机修改字典树结构连出的边。
4. 红色的边：当前结点求出的 fail 指针
5. 黄色的边：fail 指针
6. 灰色的边：字典树的边

可以发现，众多交错的黑色边将字典树变成了 **字典图**。图中省略了连向根结点的黑边（否则会更乱）。我们重点分析一下结点 5 遍历时的情况。我们求 $\text{trans}[5][s]=6$ 的 fail 指针：

![AC_automation_b_7.png](C:/Users/sdlwq/Desktop/Github/OI-wiki/docs/string/images/ac-automaton2.png)

本来的策略是找 fail 指针，于是我们跳到 $\text{fail}[5]=10$ 发现没有 `s` 连出的字典树的边，于是跳到 $\text{fail}[10]=0$，发现有 $\text{trie}[0][s]=7$，于是 $\text{fail}[6]=7$；但是有了黑边、蓝边，我们跳到 $\text{fail}[5]=10$ 之后直接走 $\text{trans}[10][s]=7$ 就走到 $7$ 号结点了。

这就是 build 完成的两件事：构建 fail 指针和建立字典图。这个字典图也会在查询的时候起到关键作用。

## 多模式匹配

接下来分析匹配函数 `query()`：

```cpp
// C++ Version
int query(char *t) {
  int u = 0, res = 0;
  for (int i = 1; t[i]; i++) {
    u = tr[u][t[i] - 'a'];  // 转移
    for (int j = u; j && e[j] != -1; j = fail[j]) {
      res += e[j], e[j] = -1;
    }
  }
  return res;
}
```

```python
# Python Version
def query(t):
    u, res = 0, 0
    i = 1
    while t[i] == False:
        u = tr[u][t[i] - ord('a')]
        j = u
        while j == True and e[j] != -1:
            res += e[j]
            e[j] = -1
            j = fail[j]
        i += 1
    return res
```

这里 $u$ 作为字典树上当前匹配到的结点，`res` 即返回的答案。循环遍历匹配串，$u$ 在字典树上跟踪当前字符。利用 fail 指针找出所有匹配的模式串，累加到答案中。然后清零。在上文中我们分析过，字典树的结构其实就是一个 trans 函数，而构建好这个函数后，在匹配字符串的过程中，我们会舍弃部分前缀达到最低限度的匹配。fail 指针则指向了更多的匹配状态。最后上一份图。对于刚才的自动机：

![AC_automation_b_13.png](C:/Users/sdlwq/Desktop/Github/OI-wiki/docs/string/images/ac-automaton3.png)

我们从根结点开始尝试匹配 `ushersheishis`，那么 $p$ 的变化将是：

![AC_automation_gif_c.gif](C:/Users/sdlwq/Desktop/Github/OI-wiki/docs/string/images/ac-automaton3.gif)

1. 红色结点：$p$ 结点
2. 粉色箭头：$p$ 在自动机上的跳转，
3. 蓝色的边：成功匹配的模式串
4. 蓝色结点：示跳 fail 指针时的结点（状态）。

## 总结

时间复杂度：定义 $|s_i|$ 是模板串的长度，$|S|$ 是文本串的长度，$|\Sigma|$ 是字符集的大小（常数，一般为 26）。如果连了 trie 图，时间复杂度就是 $O(\sum|s_i|+n|\Sigma|+|S|)$，其中 $n$ 是 AC 自动机中结点的数目，并且最大可以达到 $O(\sum|s_i|)$。如果不连 trie 图，并且在构建 fail 指针的时候避免遍历到空儿子，时间复杂度就是 $O(\sum|s_i|+|S|)$。

### 确定有限状态自动机

如果大家理解了上面的讲解，那么作为拓展延伸，文末我们简单介绍一下自动机与 KMP 自动机。（现在你再去看百科上自动机的定义就会好懂很多啦）

有限状态自动机（deterministic finite automaton，DFA）是由

1. 状态集合 $Q$；
2. 字符集 $\Sigma$；
3. 状态转移函数 $\delta:Q\times \Sigma \to Q$，即 $\delta(q,\sigma)=q',\ q,q'\in Q,\sigma\in \Sigma$；
4. 一个开始状态 $s\in Q$；
5. 一个接收的状态集合 $F\subseteq Q$。

组成的五元组 $(Q,\Sigma,\delta,s,F)$。

那这东西你用 AC 自动机理解，状态集合就是字典树（图）的结点；字符集就是 `a` 到 `z`（或者更多）；状态转移函数就是 $\text{trans}(u,c)$ 的函数（即 $\text{trans}[u][c]$）；开始状态就是字典树的根结点；接收状态就是你在字典树中标记的字符串结尾结点组成的集合。

### KMP 自动机

KMP 自动机就是一个不断读入待匹配串，每次匹配时走到接受状态的 DFA。如果共有 $m$ 个状态，第 $i$ 个状态表示已经匹配了前 $i$ 个字符。那么我们定义 $\text{trans}_{i,c}$ 表示状态 $i$ 读入字符 $c$ 后到达的状态，$\text{next}_{i}$ 表示 [prefix function](./kmp.md)，则有：

$$
trans_{i,c} =
\begin{cases}
i + 1,  & \text{if }b_{i} = c \\[2ex]
\text{trans}_{next_{i},c}, & \text{else}
\end{cases}
$$

（约定 $\text{next}_{0}=0$）

我们发现 $\text{trans}_{i}$ 只依赖于之前的值，所以可以跟 [KMP](./kmp.md) 一起求出来。（一些细节：走到接受状态之后立即转移到该状态的 next）

时间和空间复杂度：$O(m|\Sigma|)$。

对比之下，AC 自动机其实就是 Trie 上的自动机。（虽然一开始丢给你这句话可能不知所措）