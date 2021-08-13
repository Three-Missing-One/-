# HDU第八场

## 1003.Ink on paper
一直T，原因有下如果把每个点和其他边的连接，然后在大约会有1e7条边，然后就是sort时候$nlog(n)$会卡掉时间，然后如果是不加所有的边，我们加最优的边，这样我们需要对每个点连的边进行排序，但是这边排序也会爆炸，最后想出来的办法是用个个优先队列找到了前100条最近的边，然后卡过去了


```cpp
#include <bits/stdc++.h>
#define INF 0x3f3f3f3f 
using namespace std;
const int maxn = 5005;
int n;
const int maxm = 2e6+5;
struct Node {
    int x,y;
}node[maxn];
inline double dis(Node a, Node b){
    return sqrt(1.0*(a.x-b.x)*(a.x-b.x)+1.0*(a.y-b.y)*(a.y-b.y));
}
struct edge{
    int x,y;
    double len;
    bool operator <(const edge &y)const{
        return len < y.len; 
    }
}e[maxm];
bool cmp(edge a,edge b){
    return a.len < b.len;
}
int fa[maxn];
int get(int x){
    if(fa[x]==x)return x;
    return fa[x] = get(fa[x]);
}
bool merge(int x,int y){
    int p = get(x);
    int q = get(y);
    if(p!=q){
        fa[p] = q;
        return true;
    }
    return false;
}
// edge E[maxn];
priority_queue<edge>q;
int main(){
    int t;
    scanf("%d",&t);
    while(t--){
        scanf("%d",&n);
        int cnt = 0;
        int tiao = 500;
        for(int i = 1; i <= n ;i++){
            scanf("%d%d",&node[i].x,&node[i].y);
            for(int j = 1;j < i; j++){
                // e[cnt].len = dis(node[i],node[j]);
                // e[cnt].x = i;
                // e[cnt++].y = j;
                // E[i][j] = E[j][i] = dis(node[i],node[j]);
                // E[j].x = i;E[j].y = j;E[j].len = dis(node[i],node[j]);
                double d = dis(node[i],node[j]);
                if(q.size()<tiao)q.push(edge{i,j,d});
                else if(q.top().len > d){
                    q.pop();
                    q.push(edge{i,j,d});
                }
            }
            // sort(E+1,E+i,cmp);
            while(!q.empty()){
                // cout<<q.top().len<<"$$"<<endl;
                e[cnt].x = q.top().x;
                e[cnt].y = q.top().y;
                e[cnt++].len = q.top().len;
                q.pop();
            }
        }
        sort(e,e+cnt,cmp);
              
        for(int i = 1;i <= n;i++){
            fa[i] = i;
        }

        int ans = INF;
        int rt = n;
        for(int i = 0;i < cnt; i++){
            if(merge(e[i].x,e[i].y)){
                rt--;
            }
            if(rt==1){
                ans = i;
                break;
            }
        }
        printf("%.0lf\n",e[ans].len*e[ans].len);
    }
}

```

## 1004-Counting Stars

和题解说的一样，一眼线段树，但是我们的修改又不是线段树的基础操作，然后就是经典的我们不知道的一些问题，比如：像这种数值快速递降至稳定的函数，（区间开根号，区间求欧拉函数之类），我们就是暴力区间修改，因为是这样的，我们先看减lowbit的操作，一个数最多减他的log次，一共n个数，然后减nlogn次就会减到0，线段树每次减数需要logn的复杂度，左移暴力修改的复杂度为$O(nlogn^2)$然后再看加法，我们就是加这个数的最高位，我们单独记最高位，在单独的判断处最高位其他位的和就可，注意打0的标记，不然暴力修改复杂度非常高。



AC代码：
```cpp

#include <bits/stdc++.h>
#define lowbit(x) (x&(-x))
using namespace std;
typedef long long ll;
const int maxn = 100005;
const int mod = 998244353;
int a[maxn];
struct Tree{
    int l,r;
    ll sum,top;
    ll tag;
    ll lazy;
}t[maxn<<2];
void pushup(int pos){
    t[pos].sum = (t[pos<<1].sum + t[pos<<1|1].sum)%mod;
    t[pos].tag = (t[pos<<1].tag||t[pos<<1|1].tag);
    t[pos].top = (t[pos<<1].top + t[pos<<1|1].top)%mod;
}
void build(int pos,int l,int r){
    t[pos].l = l;t[pos].r = r;
	t[pos].tag = 1;
    t[pos].lazy = 1;
    if(l==r){
        for(int i = 31;i>=0;i--){
            if(a[l]&(1LL<<i)){
                t[pos].top = 1LL<<i;break;
            }
        }                                                                                                                               
        t[pos].sum = (a[l] - t[pos].top + mod) % mod;
        return ;
    }
    int mid = (l+r)>>1;
    build(pos<<1,l,mid);
    build(pos<<1|1,mid+1,r);
    pushup(pos);
    return ;
}
void spread(int pos){
    if(t[pos].tag == 0){
        t[pos<<1].tag = 0;t[pos<<1|1].tag = 0;
    }
        t[pos<<1].lazy *= t[pos].lazy;
        t[pos<<1].lazy %= mod;
        t[pos<<1|1].lazy *= t[pos].lazy;
        t[pos<<1|1].lazy %= mod;
        t[pos<<1].top = t[pos<<1].top * t[pos].lazy % mod;
        t[pos<<1|1].top = t[pos<<1|1].top * t[pos].lazy % mod;
        t[pos].lazy = 1;
}
ll query(int pos,int l,int r){
    if(t[pos].tag==0)return 0;
    if(l<=t[pos].l&&r>=t[pos].r){
        return (t[pos].sum + t[pos].top)%mod;
    }
    spread(pos);
    int mid = (t[pos].l + t[pos].r)>>1;
    ll ans = 0;
    if(l <= mid)ans += query(pos<<1,l,r);
    if(r > mid)ans += query(pos<<1|1,l,r);
    ans%=mod;
    return ans;
}
void update1(int pos, int l,int r){
    if(t[pos].tag==0)return ;
    if(t[pos].l==t[pos].r){
        if(t[pos].sum==0){
            t[pos].top = 0;
            t[pos].tag = 0;
        }
        else t[pos].sum -= lowbit(t[pos].sum);
        return ;
    }
    spread(pos);
    int mid = (t[pos].l + t[pos].r)>>1;
    if(l<=mid)update1(pos<<1,l,r);
    if(mid<r)update1(pos<<1|1,l,r);
    pushup(pos);
}
void update2(int pos, int l,int r){
    if(t[pos].tag==0){
        return ;
    }
    if(t[pos].l>=l&&r>=t[pos].r){
        t[pos].top <<= 1;
        t[pos].top %= mod;
        t[pos].lazy <<= 1;
        t[pos].lazy %= mod;
        return ;
    }
    spread(pos);
    int mid = (t[pos].l + t[pos].r)>>1;
    if(l<=mid)update2(pos<<1,l,r);
    if(mid<r)update2(pos<<1|1,l,r);
    pushup(pos);
}
int main(){
    int t;
    scanf("%d",&t);
    while(t--){
        int n;
        scanf("%d",&n);
        for(int i = 1;i <= n;i++){
            scanf("%d",&a[i]);
        }
        build(1,1,n);
        int q;
        scanf("%d",&q);
        int opt,l,r;
        for(int i = 0 ;i < q; i++){
            scanf("%d%d%d",&opt,&l,&r);
            if(opt==1){
                printf("%lld\n",query(1,l,r)%mod);
            }
            else if(opt==2){
                update1(1,l,r);
            }
            else {
                update2(1,l,r);
            }
        }
    }
}

```