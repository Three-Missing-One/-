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
