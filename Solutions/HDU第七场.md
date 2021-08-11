[TOC]

# HDU第七场

## 1003-Fall with Trees

水题，大概就是判断二叉树覆盖的面积，这个题主要是需要$O(1)$计算面积，$O(n)$会爆掉，大致就是预处理一下，然后在直接计算
**AC代码**
```cpp
#include <bits/stdc++.h>
using namespace std;
double dis(double lsx,double lsy,double rsx,double rsy){
    return sqrt((lsx-rsx)*(lsx-rsx) + (lsy-rsy) * (lsy-rsy));
}
const int maxn = 2e4+5;
double sum[maxn],s[maxn];
int main(){
    int t;
    double up = 0,down = 1;
    double h = 1;
    scanf("%d",&t);
    for(int i = 1;i < 1e4;i++){
        s[i] = s[i-1]+(up+down)*h/2;
        double tmp = up;
        up = down;
        down = up+(down-tmp)/2;
    }
    while(t--){
        int rx,ry,lsx,lsy,rsx,rsy;
        int k;
        scanf("%d",&k);
        scanf("%d%d%d%d%d%d",&rx,&ry,&lsx,&lsy,&rsx,&rsy);
        double d = dis(lsx,lsy,rsx,rsy);
        double h = dis(rx,ry,1.0*(lsx+rsx)/2,1.0*(lsy+rsy)/2);
        // cout<<d<<"##"<<h<<endl;
        // double up = 0;
        // double down = d;
        double S = 0;
        // for(int i = 1;i < k;i++){
        //     // cout<<up<<"##"<<down<<endl;
        //     S += (up+down)*h/2;
        //     double tmp = up;
        //     up = down;
        //     down = up+(down-tmp)/2;
        // }
        S = d*h*s[k-1];
        printf("%.3lf\n",S);
    }
}
```

## 1004-Link with Balls

组合数学，需要一定的基础和思维才可以

题意就是给$2*n$个篮子，然后每个篮子可以拿的个数是确定的，第一个篮子可以拿$k$个（k任意），第二个篮子可以拿一个第三个篮子可以拿$2k$个,大致排列如下
$$k,1,2k,2,3k,3,4k,4 \ldots$$

然后我们发现两两组合可以取任意数，比如第二个和第三个，第四个和第五个$\ldots$
然后第一个单看也是可以取任何数，而且最后会剩下一个$n$单出来也可以代表任何数,所以就是有可以选任何数的$n+1$个盒子，我们枚举一个盒子选取的个数，然后在其他盒子中使用隔板法，就有$$\sum_{i=0}^{n}\tbinom{m-n-i-1}{n-1}$$
运用组合数斜项求和可以化简到$O(1)$
化简到$$\tbinom{m+n}{n} - \tbinom{m-1}{n}$$
直接套板子就能做

**AC代码**

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int mod = 1e9+7;
const int Maxn = 2e6+5;
//用O(n)的时间预处理逆元表inv[n]。
ll inv[Maxn+10];
void setInv(int n){
	inv[0]=inv[1]=1;
	for(int i=2;i<=n;i++){
		inv[i]=1LL*(mod-mod/i)*inv[mod%i]%mod;
	}
}
//预处理阶乘表 fac[n]=(fac[n-1]*n)%p=(n!)%p
//预处理阶乘的逆元表 facInv[n]=(facINv[n-1]*Inv[n])%p 。
ll facInv[Maxn+10],fac[Maxn+10];
void setFac(int n){
	fac[0]=facInv[0]=1;
	for(int i=1;i<=n;i++){
		fac[i]=1LL*fac[i-1]*i%mod;
		facInv[i]=1LL*facInv[i-1]*inv[i]%mod;
	}
}
//计算组合数 $C_{n}^{m}$
ll C(int n,int m){
	if(n<m) return 0;
	if(n<0||m<0) return 0;
	int ans=fac[n];
	ans=1LL*ans*facInv[m]%mod;
	ans=1LL*ans*facInv[n-m]%mod;
	return ans;
}

int main(){
    int t;
    scanf("%d",&t);
    setInv(Maxn -3);
    setFac(Maxn - 3);
    while(t--){
        ll n,m;
        scanf("%lld%lld",&n,&m);
        printf("%lld\n",(C(m+n,n)%mod-C(m-1,n)%mod+mod)%mod);
    }
}

```

## 1005-Link with EQ

递归，记忆化，递推，
直接贴图
![](img/2021-08-11%2016-56-24屏幕截图.png)
然后就是注意这个取模的问题，递推的时候提前取模

**AC代码**
```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int maxn = 1e6+5;
ll f[maxn],g[maxn],h[maxn];
const int mod = 1e9+7;
ll Pow(ll a ,ll b){
    ll ans = 1;
    while(b){
        if(b&1)ans = ans * a %mod;
        a = a*a%mod; 
        b>>=1;
    }
    return ans;
}
int main(){
    int t;
    scanf("%d",&t);
    f[1] = f[2] = 0;
    g[2] = 1;
    for(int i = 3;i < maxn ;i++){
        f[i] = (f[(i-1)/2] + f[i-1-(i-1)/2] + 1)%mod;
        g[i] = (f[i-1] + 1)%mod;
        h[i] = (h[i-1] + g[i-1])%mod;
        // cout<<i<<":"<<h[i]<<endl;
    }
    while(t--){
        ll n;
        scanf("%lld",&n);
        printf("%lld\n",((n+2*h[n]%mod)*Pow(n,mod-2)%mod)%mod);
    }
}

```

## 1006-Link with Grenade

题目有些难，然后没想到要转换重力场，然后就是特判0和1的情况，再者说还要注意计算的是可以存活的范围

![](img/2021-08-11%2019-01-05屏幕截图.png)
![](img/2021-08-11%2019-02-05屏幕截图.png)

```cpp
#include <bits/stdc++.h>
#define pi acos(-1)
using namespace std;
typedef long long ll;
const int mod = 1e9+7;
ll Pow(ll a,ll b){
    ll ans = 1;
    while(b){
        if(b&1)ans = a * ans % mod;
        a = a*a%mod;
        b>>=1;
    }
    return ans;
}
int main(){
    int T;
    scanf("%d",&T);
    while(T--){
        ll t,v,r;
        ll g = 10;
        scanf("%lld%lld%lld",&t,&v,&r);
        if (4ll*g*t*t*t*v+4ll*v*v*t*t+1ll*g*g*t*t*t*t-4ll*r*r<=0) {
		    printf("0\n");
            continue;
	    }   
	    if (4ll*g*t*t*t*v+4ll*v*v*t*t+1ll*g*g*t*t*t*t-4ll*r*r>=8ll*g*t*t*t*v) {
		    printf("1\n");
            continue;
	    }
        ll CosUp = (1LL*(5*t*t)*(5*t*t)%mod + (v*t) * (v*t) - r*r)%mod;
        ll CosDown = (1LL*2*v*t*5*t*t)%mod;
        // cout<<CosUp<<" "<<CosDown<<endl;
        ll hUp = v*t%mod*CosDown%mod - (v*t%mod*CosUp%mod);
        ll hDown = CosDown;
        ll ans = (hDown*2%mod*v%mod*t%mod - hUp + mod)%mod;
        ans = ans * Pow(hDown * 2*v%mod*t%mod ,mod-2);
        // ll S = ((hUp) * Pow(hDown,mod-2))%mod;
        // // cout<<hUp<<"##"<<hDown<<endl;
        // ll ans = (S) * Pow(2 *v*t ,mod-2)%mod;
        printf("%lld\n",ans%mod);
        // printf("%lld\n",5*(Pow(8,mod-2))%mod);
    }
}

```

## 1007-Link with Limit

就是求每个环上数值的均值，比如f(1) = 2我们可以在1到2上连边，这样我们的计算，通过并查集，记录是否有环，然后再沿环dfs搜索，得到均值即可


```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int maxn = 1e5+5;
ll f[maxn];
int fa[maxn];
struct edge{
    int v,next;
}e[maxn<<1];
int head[maxn];
int cnt = 0;
double avg = -1;
int n;
int get(int x){
    if(fa[x]==x)return x;
    return fa[x] = get(fa[x]);
}
bool merge(int x,int y){
    int p = get(x);
    int q = get(y);
    if(p!=q){
        fa[q] = p;
        return true;
    }
    return false;
}
inline void add(int u,int v){
    e[cnt].v = v;
    e[cnt].next = head[u];
    head[u] = cnt++;
}
int ed;
int flag = 0;
void dfs(int x,ll ff, int sz){
    if(x==ed){
        if(avg<0){
            avg = 1.0*ff/sz;
            // cout<<avg<<"#"<<x<<endl;
        }
        else {
            if(avg!=1.0*ff/sz){
                flag = 1;
                // cout<<avg<<"##"<<1.0*ff/sz<<endl;
            }
        }
        return ;
    }
    for(int i = head[x];~i;i = e[i].next){
        int v = e[i].v;
        dfs(v,ff+f[v],sz+1);
    }
}
int main(){
    int t;
    scanf("%d",&t);
    while(t--){
        cnt = 0;
        scanf("%d",&n);
        for(int i = 1;i <= n;i++){
            head[i] = -1;fa[i] = i;
        }
        avg = -1;
        flag = 0;
        for(int i = 1;i <= n;i++){
            scanf("%lld",&f[i]);
            if(flag == 1 )continue;
            if(i==f[i]){
                if(avg<0)avg = f[i];
                else {
                    if(abs(avg-f[i])>1e-4){
                        flag = 1;
                        continue;
                    }
                }
            }
            ed = i;
            if(!merge(i,f[i])){
                // cout<<i<<get(i)<<":"<<ed<<endl;
                dfs(f[i],f[f[i]],1);
            }
            add(i,f[i]);
        }

        if(!flag&&avg!=-1)printf("YES\n");
        else printf("NO\n");
    }
}

```


## 1008-Smzzl with Greedy Snake
模拟，不说了，无脑转向，在无脑前进
```cpp
#include <bits/stdc++.h>
using namespace std;
int main(){
    int t;
    scanf("%d",&t);
    while(t--){
        int x,y,d;
        scanf("%d%d%d",&x,&y,&d);
        int n;
        scanf("%d",&n);
        for(int i = 0 ;i < n;i ++){
            int tx,ty;
            scanf("%d%d",&tx,&ty);
            if(tx>x&&ty>y){
                if(d==2){
                    printf("u");
                    for(int j = 0;j < tx-x;j++)printf("f");
                    printf("u");
                    for(int j = 0;j < ty-y;j++)printf("f");
                    d = 0;x = tx;y = ty;
                }
                else if(d==3){
                    printf("c");
                    for(int j = 0 ;j < ty-y;j++)printf("f");
                    printf("c");
                    for(int j = 0 ;j < tx-x;j++)printf("f");
                    d = 1;x = tx;y = ty;
                }
                else if(d==1){
                    for(int j = 0;j < tx-x;j++)printf("f");
                    printf("u");
                    for(int j = 0;j < ty-y;j++)printf("f");
                    d = 0;x = tx;y = ty;
                }
                else {
                    for(int j = 0 ;j < ty-y;j++)printf("f");
                    printf("c");
                    for(int j = 0 ;j < tx-x;j++)printf("f");
                    d = 1;x = tx;y = ty;
                }
                
            }
            else if(tx>x&&ty<y){//右下
                  if(d==2){
                    for(int j = 0;j < y-ty;j++)printf("f");
                    printf("u");
                    for(int j = 0;j < tx-x;j++)printf("f");
                    d = 1;x = tx;y = ty;
                }
                else if(d==3){
                    printf("u");
                    for(int j = 0 ;j < y-ty;j++)printf("f");
                    printf("u");
                    for(int j = 0 ;j < tx-x;j++)printf("f");
                    d = 1;x = tx;y = ty;
                }
                else if(d==1){
                    for(int j = 0;j < tx-x;j++)printf("f");
                    printf("c");
                    for(int j = 0;j < y-ty;j++)printf("f");
                    d = 2;x = tx;y = ty;
                }
                else {
                    printf("c");
                    for(int j = 0 ;j < tx-x;j++)printf("f");
                    printf("c");
                    for(int j = 0 ;j < y-ty;j++)printf("f");
                    d = 2;x = tx;y = ty;
                }
            }
            else if(tx<x&&ty>y){//左上
                if(d==2){
                    printf("c");
                    for(int j = 0;j < x-tx;j++)printf("f");
                    printf("c");
                    for(int j = 0;j < ty-y;j++)printf("f");
                    d = 0;x = tx;y = ty;
                }
                else if(d==3){
                    for(int j = 0;j < x-tx;j++)printf("f");
                    printf("c");
                    for(int j = 0;j < ty-y;j++)printf("f");
                    d = 0;x = tx;y = ty;
                }
                else if(d==1){
                    printf("u");
                    for(int j = 0;j < ty-y;j++)printf("f");
                    printf("u");
                    for(int j = 0;j < x-tx;j++)printf("f");
                    d = 3;x = tx;y = ty;
                }
                else {
                    for(int j = 0;j < ty-y;j++)printf("f");
                    printf("u");
                    for(int j = 0 ;j < x-tx;j++)printf("f");
                    d = 3;x = tx;y = ty;
                }
            }
            else if(tx<x&&ty<y){//左下
                if(d==2){
                    for(int j = 0;j < y-ty;j++)printf("f");
                    printf("c");
                    for(int j = 0;j < x-tx;j++)printf("f");
                    d = 3;x = tx;y = ty;
                }
                else if(d==3){
                    for(int j = 0;j < x-tx;j++)printf("f");
                    printf("u");
                    for(int j = 0;j < y-ty;j++)printf("f");
                    d = 2;x = tx;y = ty;
                }
                else if(d==1){
                    printf("c");
                    for(int j = 0;j < y-ty;j++)printf("f");
                    printf("c");
                    for(int j = 0;j < x-tx;j++)printf("f");
                    d = 3;x = tx;y = ty;
                }
                else {
                    printf("u");
                    for(int j = 0;j < x-tx;j++)printf("f");
                    printf("u");
                    for(int j = 0 ;j < y-ty;j++)printf("f");
                    d = 2;x = tx;y = ty;
                }
            }
            
        }
        printf("\n");
    }
   
}

```



