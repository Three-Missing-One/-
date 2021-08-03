[TOC]

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
