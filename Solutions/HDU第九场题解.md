# HDU第八场

## 1002.Just another board game

题意：
    给定一个数据矩阵，然后我们有两个人A,B，一共可以走k步，然后A希望最后停留在的位置的数尽量大，B希望停留位置的数尽量小，轮到每个人走的时候，该玩家选择一项，停止游戏，或者按照他的法则继续走，然后就是A只能走列，B只能是走行


要看k的奇偶，如果k是奇数，意味着k最后走的是A，那么他一定会停留在某一行的最大值处，然后B希望他停留在的那一行的最大值最小
k为偶数A的想法同理，
ps注意判断特例，比如我们A同学在走第一步之前发现自己的数已经很大了，就可以直接快结束游戏
```cpp
#include <bits/stdc++.h>
#define INF 0x3f3f3f3f
using namespace std;
typedef long long ll;
const int maxn = 1e5+5;
int n,m;
ll k;
struct node {
    int v;
    int pos;
}mx[maxn],mn[maxn];
int main(){
    int t;
    scanf("%d",&t);
    while(t--){
        // inits();
        int st = 0;
        scanf("%d%d%lld",&n,&m,&k);
        for(int i = 1 ;i <= m ;i++)mn[i].v = INF;
        for(int i = 1;i <= n;i++){
            mx[i].v = 0;
            for(int j = 1;j <= m;j++){
                int x;
                scanf("%d",&x);
                if(i==1&&j==1)st = x;
                if(mx[i].v < x){
                    mx[i].v = x;
                    mx[i].pos = j;
                }
                if(mn[j].v > x){
                    mn[j].v = x;
                    mn[j].pos = i;
                }
            }
        }
        int mnx = INF;
        if(k&1){
            if(k==1){
                printf("%d\n",mx[1].v);
            }
            else {
                for(int i = 1;i <= n;i++){
                    mnx = min(mx[i].v,mnx);
                }
                if(st > mnx)mnx = st;
                printf("%d\n",mnx);
            }
        }
        else {
            int mxy = st;
            for(int i = 1;i <= m;i++){
                mxy = max(mn[i].v,mxy);
                // cout<<mn[i].v<<"##"<<endl;
            }
            printf("%d\n",mxy); 
        }
    }

}
```