[TOC]

# HDU第三场

## Segment Tree with Pruning

补题过的，现场我们队是找规律做的，这里我给出我的记忆化搜索AC的代码

就是搜过的长度就不搜了，另外ull可以到2e9，而且动态开点线段树节点个数为n的话，节点数最多为$2n-1$(蓝书上有说)

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef unsigned long long ll;
map<ll,ll>mp;
ll n,k;
ll dfs(ll x){

    if(mp[x]!=0){
        return mp[x]; 
    }
    if(x <= k){
        return mp[x] = 1;
    }
    ll mid = x/2;
    int mod = x%2;
    if(mod==1){
        mp[mid] = dfs(mid);
        mp[mid+1] = dfs(mid+1);
        return mp[mid]+mp[mid+1]+1;
    } 
    else {
        mp[mid] = dfs(mid);
        return mp[mid]*2+1;
    }
    
}
int main(){
    int t;
    scanf("%d",&t);
    while(t--){
        mp.clear();
        scanf("%llu%llu",&n,&k);
        ll ans = dfs(n);
        printf("%llu\n",ans);
    }
}
```

# HDU第四场题解

## Lawn of the Dead

题意：有一个僵尸在坐标(0,0)处（左上角），他只能往左走，或者往下走，会给定一些格子这个僵尸无法走，问这个僵尸能走到的格子数
这道题我们队开始分析的很好：因为是$1e5*1e5$的数据范围，所以没有办法搜索点数，另外，但是雷数也只有1e5，所以我们可以从可行的点数出发寻找。
另外，在最上面那一行的的第一个点以后的点都不可达，第一列最上方以后的顶点都不可达，包括由行逐渐向下递推等思想，到时我们想状态压缩用bitset做，但是无奈，mle了bitset也开不到$1e5*1e5$的。 看了题解发现是道线段树，并且每一行递推，开两个线段树，并且向下递推，一个维护上一行的可行点，另一个对下一行进行修改，最终实现整个递推，每一行遇到一个地雷，就查询这个地雷后前可以走到的个数，并进行修改


开了一个线段树类，更快速简单的建树
```cpp
#include <bits/stdc++.h>
#define INF 0x3f3f3f3f
using namespace std;
typedef long long ll;
int n,m,k;
const int maxn = 1e5+5;
vector<int>g[maxn];

class SegmentTree{
    public:

    struct Tree{
        int l,r;
        int lazy;
        int ok;
    }tree[maxn<<2];
    void build(int pos,int l, int r){
        if(l==r){
            tree[pos].l = tree[pos].r = r;
            tree[pos].lazy = 0;
            tree[pos].ok = 0;
            return ;
        }
        tree[pos].l = l;tree[pos].r = r;
        tree[pos].ok = tree[pos].lazy = 0;
        int mid = (l+r)>>1;
        build(pos<<1,l,mid);
        build(pos<<1|1,mid+1,r);
    }
    void spread(int pos){
        if(tree[pos].lazy==0)return ;
        tree[pos<<1].ok = (tree[pos<<1].r-tree[pos<<1].l+1) * (tree[pos].lazy-1);
        tree[pos<<1|1].ok = (tree[pos<<1|1].r-tree[pos<<1|1].l+1) * (tree[pos].lazy-1);
        tree[pos<<1].lazy = tree[pos].lazy;
        tree[pos<<1|1].lazy = tree[pos].lazy;
        tree[pos].lazy = 0;
    }
    void pushup(int pos){
        tree[pos].ok = tree[pos<<1].ok+tree[pos<<1|1].ok;
    }
    void update(int pos,int l,int r,int v){
        // cout<<pos<<endl;
        if(l<=tree[pos].l&&tree[pos].r<=r){
            tree[pos].ok = v*len(pos);
            tree[pos].lazy = v + 1;
            return ;
        }
        spread(pos);
        int mid = (tree[pos].l+tree[pos].r)>>1;
        if(l<=mid)update(pos<<1,l,r,v);
        if(r>mid)update(pos<<1|1,l,r,v);
        pushup(pos);
    }
    int len(int pos){
        return tree[pos].r-tree[pos].l+1;
    }
    int query(int u,int l,int r) {
    	if(tree[u].ok==0) return INF;
    	if(tree[u].l==tree[u].r) return tree[u].l;
    	spread(u);
    	if(tree[u].l>=l&&tree[u].r<=r) {
    		if(tree[u<<1].ok>0) return query(u<<1,l,r);
    		else return query(u<<1|1,l,r);
    	}
    	int ans=INF;
        int mid = (tree[u].l+tree[u].r)>>1;
    	if(l<=mid) ans=min(ans,query(u<<1,l,r));
    	if(r>mid) ans=min(ans,query(u<<1|1,l,r));
    	return ans;
    }

};
SegmentTree t[2]; 
int main(){
    int tt;
    scanf("%d",&tt);

    while(tt--){
        scanf("%d%d%d",&n,&m,&k);
        for(int i=1;i<=n;i++) g[i].clear();
		for(int i=1;i<=k;i++) {
			int x,y; scanf("%d%d",&x,&y);
			g[x].push_back(y);
		}
		t[0].build(1,1,m); t[1].build(1,1,m);
		int now=0; t[now].update(1,1,1,1); now^=1;
		ll ans=0;
        for(int i = 1;i <= n; i++){
            sort(g[i].begin(),g[i].end());
            int l = 0;
                for(auto x:g[i]) {
                    if(l+1<=x-1) {
                        int pos = t[now^1].query(1,l+1,x-1);
                        if(pos!=INF) t[now].update(1,pos,x-1,1);
                    }
                    l=x;
                }
                if(l+1<=m) {
                    int pos=t[now^1].query(1,l+1,m);
                    if(pos!=INF) t[now].update(1,pos,m,1);
                }
            
            ans+=t[now].tree[1].ok;
            // cout<<t[now].tree[1].ok<<"##"<<endl;
            t[now^1].update(1,1,m,0);
            now^=1;
        }
        cout<<ans<<endl;
    }
}

```



# HDU第五场题解

## Cute Tree
记忆化搜索
本来是想使用暴力维护区间长度，就是一层层向下算每层节点的个数的，但是发现T掉后改了记忆化搜索的版本，
大致思想就是如果一个如果长度为2，那么我们所有2的区间将会在向下分，然后分出的结果就是一样的，我们不如打一个表，这个dp数组中使用记忆化搜索，$dp[i]$代表这个i长度可以分多少个区间，这样就好了

```cpp
#include <bits/stdc++.h>
using namespace std;
const int maxn = 2e5+5;
int a[maxn];
int dp[maxn];
int ans = 0;
int dfs(int x){
    // cout<<x<<"#"<<endl;
    if(dp[x]!=0)return dp[x];
    if(x==0)return 1;
    int a,b,c;
    a = b = c = x / 3;
    int mod = x%3;
    if(mod==1){
        dp[a] = dfs(a);dp[b] = dfs(b);dp[c+1] = dfs(c+1);
        return dp[x] = dp[a]+dp[b]+dp[c+1]+1;
    }
    else if(mod==2){
         dp[a] = dfs(a);dp[b+1]=dfs(b+1);dp[c+1]=dfs(c+1);
         return dp[x] = dp[a]+dp[b+1]+dp[c+1]+1;
    }
    else {
         dp[a] = dfs(a);dp[b] = dfs(b);dp[c] = dfs(c);
         return dp[x] = dp[a]+dp[b]+dp[c]+1;
    }
}
int main(){
    int t;
    scanf("%d",&t);
    dp[1] = 1;
    dp[2] = 3;
    dp[3] = 4;
    while(t--){
        int n;
        scanf("%d",&n);
        for(int i = 0 ;i < n;i++){
            scanf("%d",&a[i]);
        }
        int ans = 1;
        if(n<=3){
            if(n!=1)printf("%d\n",n+1);
            else printf("1\n");
            continue;
        }
        // ans += 1;
        ans = dfs(n);
        printf("%d\n",ans);
    }
}
```

## Banzhuan

思维题看了很长的实现，开始大家糊出来的思路是错的，然后直到最后才发现，大约是最后10分钟后过的题目，发现大家思维题也有待练习，之前被数据结构卡过，被上一场牛客也是被思维题（煎汉堡）卡住的，也是在最后想出的思路，但是那题没过....，所以没事的时候记得打cf，找手感

方法是，最小值就是沿着大x轴和y轴向上垒，比对角线垒要好，最大值就是放在顶上，让他自己往下掉
注意数据范围，要使用逆元

```cpp

#include <bits/stdc++.h>

#define ill __int128
#define ll long long
#define PII pair <ll,ll>
#define ull unsigned long long
#define me(a,b) memset (a,b,sizeof(a))
#define rep(i,a,b) for (int i = a;i <= b;i ++)
#define req(i,a,b) for (int i = a;i >= b;i --)
#define ios std :: ios :: sync_with_stdio(false)

const double Exp = 1e-9;
const int INF = 0x3f3f3f3f;
const int inf = -0x3f3f3f3f;
const ll mode = 1000000007;
const double pi = 3.141592653589793;

using namespace std;

int T;
ll n;

ll qmul(ll a, ll b)
{
    a = a % mode;
    b = b % mode;
    ll ans = 0;
    while (b) {
        if (b & 1) ans = (ans + a) % mode;
        a = (a + a) % mode;
        b >>= 1;
    }
    return ans % mode;
}

ll qpow(ll a, ll b)
{
    ll ans = 1ll;
    a = a % mode;
    while (b) {
        if(b & 1) ans = qmul(ans, a) % mode;
        a = qmul(a, a) % mode;
        b >>= 1;
    }
    return ans % mode;
}

int main()
{
    scanf ("%d",&T);
    while (T--) {
        scanf ("%lld", &n);
        ll a1 = qmul(n, n + 1), a2 = qmul(qmul(2, n) + 1, n);
        ll div12 = qpow(12 * 1ll, mode - 2ll) % mode;
        ll S = qmul(qmul(qmul(a1, a2), (n + 1)), div12) % mode;
        ll a3 = qmul(n - 1, n + 2);
        ll div2 = qpow(2ll, mode - 2ll) % mode, div6 = qpow(6ll, mode - 2ll) % mode;
        ll T1 = qmul(qmul(n, n + 1), div2), T2 = qmul(qmul(a1, qmul(2, n) + 1), div6);
        ll T = (qmul(T1 + T2 - 2, qmul(a3, div2))) % mode;
        ll ans = (S + T) % mode;
        printf ("%lld\n", ans);
        ans = qmul(qmul(n, n), S) % mode;
        printf ("%lld\n", ans); 
    }
    return 0;
}
```

# HDU第六场题解

## A.Yes,Prime Minister

**题解：**

背锅的来写题解.....贡献$Wa4$的罚时......

首先来说一下得出的结论：

对于任意一个正数$n$，我们都有：$n + n + 1 = 2n + 1$，$n + n + 1 + n + 2 = 3n + 3 \mod 3 = 0$，$n + n + 1 + n + 2 + n + 3 = 4n + 6 \mod 2 = 0$$......$，所以我们认为：**只有连续的两个数相加才有机会成为质数**，这一点对于向左拓展也是成立的。

所以我们需要做的就是以2为步长，枚举左端点，如果出现合法情况则停止枚举，更新答案。

假设$l$是我们枚举的左端点，那么就有了下面我们的分析：

- 如果满足：$x > 0$

  - $x$为质数：则输出答案为1
  - $x$​为合数，则左右两端分别扩展一位，若和为质数，则输出答案为2；否则令$x$右移，若先满足$2 \times l + 1$为质数，输出答案为$2 \times l + 1$，若先满足$l$为质数，则输出答案$2 \times l$

- 如果满足：$x < 0$

  **对$x$取绝对值**，然后同样向右拓展，和之前类似，若先满足$2 \times l + 1$为质数，输出答案为$2 \times l + 1$，若先满足$l$为质数，则输出答案$2 \times l$。​

**AC代码：**

```cpp
#include <bits/stdc++.h>

#define ill __int128
#define ll long long
#define PII pair <ll,ll>
#define ull unsigned long long
#define me(a,b) memset (a,b,sizeof(a))
#define rep(i,a,b) for (int i = a;i <= b;i ++)
#define req(i,a,b) for (int i = a;i >= b;i --)
#define ios std :: ios :: sync_with_stdio(false)

const double Exp = 1e-9;
const ll INF = 0x3f3f3f3f;
const int inf = -0x3f3f3f3f;
const ll mode = 1000000007;
const double pi = 3.141592653589793;

using namespace std;

const int maxm = 1e8 + 10;
int cnt, T;
bool jud[maxm];

void prime()
{
    ll n = 1e8;
    jud[1] = 1;
    for (ll i = 2ll;i <= n;i ++) {
        if (jud[i]) continue;
        for (ll j = i;j <= n / i;j ++) jud[i * j] = 1;
    }
    return ;
}

ll solve(int l) 
{
    l ++;
    while (1) {
        if (!jud[l]) return 2ll * l;
        else if (!jud[2 * l + 1]) return (2ll * l + 1ll);
        l ++;
    }
}

int main()
{
    prime();
    scanf ("%d", &T);
    while (T --) {
        int x;
        scanf ("%d", &x);
        if (x == 0) {
            printf ("3\n");
            continue;
        }
        else if (x > 0 && !jud[x]) {
            printf ("1\n");
            continue;
        }
        else if (x > 0 && jud[x]) {
            int l = x;
            ll ans = 1e10;
            if (!jud[2 * l + 1] || !jud[2 * l - 1]) printf ("2\n");
            else {
                ans = min(solve(l) * 1ll, ans);
                printf ("%lld\n",ans);
            }
        }
        else if (x < 0) {
            x = abs(x);
            ll ans = 1e10;
            int l = x;
            l ++;
            while (1) {
                if (!jud[l]) {
                    ans = min(ans, 2ll * l);
                    break;
                }
                else if (!jud[2 * l + 1]) ans = min(ans, 2ll * l + 1ll);
                l ++;
            }
            printf ("%lld\n",ans);
        }
    }
    return 0;
}
```

## E.Median

**题解：**

这$n$个数最后会分成$m$个集合，我们可以通过给出的$m$个数，把这$n$个数的序列分成$m + 1$个区间，每一个区间内有的数，就是我们需要操作的数。

举个例子：如果$n = 6$，$m = 2$，$b = {3,5}$那么我们可以得到的区间有：$1,2 | 4 | 6$这样的形式。这些数中任意两个不同区间的数都可以相互抵消。我们的任务就是确定能否满足中位数不变的前提下，把这$n - m$个数抵消掉。

记录一下$m + 1$个区间中，含有元素最多为$max$，其他部分的和为$other$

- 如果$max = other$，很明显可以抵消掉。
- 如果$max < other$​，那么我们可以让最后剩余的元素**尽可能大**，然后加入到集合中，其实就是构造了偶数长度的序列。
- 如果$max > other$，那最后剩下的元素肯定是在$max$中，如果想全部放好肯定需要满足：比当前区间中最小的元素还小的中位数的数目$num$满足$num >= max - other$，否则无法构造。

**AC代码：**

```cpp
#include <bits/stdc++.h>

#define ill __int128
#define ll long long
#define PII pair <ll,ll>
#define ull unsigned long long
#define me(a,b) memset (a,b,sizeof(a))
#define rep(i,a,b) for (int i = a;i <= b;i ++)
#define req(i,a,b) for (int i = a;i >= b;i --)
#define ios std :: ios :: sync_with_stdio(false)

const double Exp = 1e-9;
const int INF = 0x3f3f3f3f;
const int inf = -0x3f3f3f3f;
const ll mode = 1000000007;
const double pi = 3.141592653589793;

using namespace std;

const int maxn = 1e5 + 9;

int T, n, m;
int b[maxn] = {}, flag[maxn] = {}, sum[maxn] = {};

void init()
{
    me (flag, 0);
    me (b, 0);
    me (sum, 0);
    return ;
}

bool cmd (int x, int y)
{
    return x < y;
}

int main()
{
    scanf ("%d",&T);
    while (T -- ) {
        init();
        scanf ("%d%d",&n,&m);
        for (int i = 1;i <= m;i ++) {
            scanf ("%d",&b[i]);
            flag[b[i]] = 1;
        }
        sort(b + 1, b + 1 + m, cmd);
        int cnt = 0;
        for (int i = 1;i <= n;i ++) {
            if (flag[i]) {
                cnt = i;
                continue;
            }  
            else sum[cnt] ++;
        }
        int maxx = 0, add = 0, id = 0;
        for (int i = 0;i <= n;i ++) {
            if (sum[i] >= maxx) {
                maxx = sum[i];
                id = i;
            }
            add += sum[i];
        }
        add -= maxx;
        if (maxx <= add) printf ("YES\n");
        else {
            maxx -= add;
            int num = 0;
            for (int i = 1;i <= m;i ++) {
                if (id >= b[i]) num ++;
                else continue;
            }
            if (num >= maxx) printf ("YES\n");
            else printf ("NO\n");
        }
    }
    return 0;
}
```

转化成间隔，不要盲目模拟，把问题抽象化。思维能力有待加强，除了转化为间隔，还有很多我们需要注意的，比如说贪心其实是想办法让靠后的具有1这个数，而且发现%2最多就是1，然后还有就是判断能不能减完前面的数也不需要暴力，只需要把最大值减去就可以了。
**AC代码**
```cpp
#include <bits/stdc++.h>
#define lowbit(x) (x&(-x))
using namespace std;
const int maxn = 1e5+5;
int a[maxn];
int c[maxn];
int main(){
    int t;
    scanf("%d",&t);
    while(t--){
        int n,k;
        scanf("%d%d",&n,&k);
        for(int i = 1;i <= k;i++){
            scanf("%d",&a[i]);
        }
        sort(a+1,a+k+1);
        int sum = 0,mx = -1,pos = -1;
        for(int i = 1;i <= k;i++){
            c[i] = a[i]-a[i-1]-1;
            sum += c[i];
            if(c[i]>mx){
                mx = c[i];
                pos = i;
            }
        }
        c[k+1] = n-a[k];
        sum += c[k+1];
        if(c[k+1]>mx){
            mx = c[k+1];
            pos = k+1;
        }
        if(mx <= sum/2){
            printf("YES\n");
            continue;
        }
        else {
            if(mx*2 - sum < pos){
                printf("YES\n");continue;
            }
            else {
                printf("NO\n");continue;
            }
        }       
    }
}

```


