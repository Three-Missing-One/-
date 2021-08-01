# ST 算法
> 详细请见蓝书P41页

在RMQ问题(区间最值问题)，ST表是基于倍增方法，实现了区间查询$O(1)$**优于线段树，树状数组等查询**，但是不能实现区间修改

基本是预处理一个二维数组，复杂度$O(nlogn)$

模板
```cpp
int f[maxn][30];//第二维是一个长度范围，一般开到30就够大了，例如f[i][j]代表从i开始长度为2^j区间的最值
int Log[maxn];
void inits(){
    Log[1]=0;
    for(int i=2;i<=n;i++)
        Log[i]=Log[i/2]+1;
}
void RMQ_ST(){
    for(int i = 1;i <= n; i++){
        f[i][0] = a[i];
    }
    int t =Log[n]+1;        //预处理Log数组要比自带的log2快一些，如果要进行大量的区间查询，最好是预处理一下
    // int t = log2(n)+1;
    for(int j = 1;j < t;j++){
        for(int i = 1;i<= n-(1<<j)+1;i++){
            f[i][j] = max(f[i][j-1],f[i+(1<<(j-1))][j-1]);
        }
    }
}
```
查询
```c++
    int kk = Log[x-i+1];//同样是使用预处理数组
    // int kk = log2(x-i+1);
    mx = max(f[i][kk],f[x-(1<<kk)+1][kk]);
```

- [2021牛客多校5-K题](https://ac.nowcoder.com/acm/contest/11256/K)

做法：ST表查询，加尺取，或者使用优先队列

题意：给定一个序列，在给定一个k，让我们找到有多少个子串（l-r）满足l到r之间的最大值和最小值之差大于k

基本可以得出，如果有某两个数满足a[l]-a[r]>k那么在l和r之外的所有对都是成立的，
我们尺取规定一个l ，r 然后判断这个区间是否满足大于k的条件，如果满足，r说明找到区间，把n-r+1累加到答案，并且l左移（右端点不变），（定左端点，移动右端点，找到第一个合适的区间，然后右端点往右的全都是），如果找不到，说明右边的数还不够，右端点继续往右，依次寻找。

**PS：本题要求预处理log打表，使用log2会超时**


```c++

#include <bits/stdc++.h>
#define INF 0x3f3f3f3f
using namespace std;
typedef long long ll;
const int maxn = 2e5+5;
ll a[maxn];
ll k;
int n,m;
int f[maxn][30];
int ff[maxn][30];
inline int read(){
   int s=0,w=1;
   char ch=getchar();
   while(ch<'0'||ch>'9'){if(ch=='-')w=-1;ch=getchar();}
   while(ch>='0'&&ch<='9') s=s*10+ch-'0',ch=getchar();
   return s*w;
}
int Log[maxn];
void inits(){
    Log[1]=0;
    for(int i=2;i<=n;i++)
        Log[i]=Log[i/2]+1;
}
void RMQ(){
    for(int i = 1;i <= n; i++){
        f[i][0] = a[i];
        ff[i][0] = a[i];
    }
    int t =Log[n]+1;

    // int t = log2(n)+1;
    for(int j = 1;j < t;j++){
        for(int i = 1;i<= n-(1<<j)+1;i++){
            f[i][j] = max(f[i][j-1],f[i+(1<<(j-1))][j-1]);
            ff[i][j] = min(ff[i][j-1],ff[i+(1<<(j-1))][j-1]);
        }
    }
}

int ok(int i,int x){
    ll mx;
    ll mn;
    int kk = Log[x-i+1];
    // int kk = log2(x-i+1);
    mx = max(f[i][kk],f[x-(1<<kk)+1][kk]);
    mn = min(ff[i][kk],ff[x-(1<<kk)+1][kk]);
    if(mx-mn > k)return 1;
    return 0;
}
int main(){
    scanf("%d%d",&n,&m);
    for(int i = 1;i <= n; i++){
        scanf("%lld",&a[i]);
        // a[i] = read();
    }
    inits();
    RMQ();
    for(int i = 0 ;i < m; i ++){
        ll ans = 0;
        scanf("%lld",&k);
        int sub = 2;
        for(int j = 1 ; j <= n; j++){
            while(sub<=n){
                if(ok(j,sub)){
                    break;
                }
                else sub++;
            }
            ans += n-sub+1;
        }
        printf("%lld\n",ans);
    }
}
```


