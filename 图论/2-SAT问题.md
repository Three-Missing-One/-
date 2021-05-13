# 2-SAT问题

**描述：有N个变量，每个变量只有两种可能的取值。再给定M个条件，每个条件都是对两个变量取值的限制。求是否存在对N个变量的合法赋值，使M个条件均可满足。**
### 2-SAT
- 2-satisfiability(2-可满足性)

一般M个条件可以转化为如下语句，若A取值为0，则B取值为1····（如果可能的取值只有0或者1的情况下）

* 非常类似于扩展域的并查集，适用于更普遍的情况，因为并查集维护的全是无向图，而2-SAT是从有向图出发的

- 无向图维护的是什么条件呢？是若p=0，则q=0 和 若q=0，则p=0等价，我们如果有条件若p则q，我们就可以吧p = 0和q = 0放在一起，p = 1和去= 1放在一起
- 有向图呢，就比如有 若p = 0，则q = 0，但是q = 0推不出p = 0
我们只能把 若p = 0，则q = 0，和若q = 1，则p = 1（逆否命题）放在一起（有向边）

* [Katu Puzzle](https://vjudge.net/problem/POJ-3678)

典型的2-SAT问题

- AND条件
    - c = 1
    若 a = 0 则 a = 1
    若 b = 0 则 b = 1
    - c = 0
    若 a = 1 则 b = 0
    若 b = 1 则 a = 0
    (很明显没有 若b = 0，则a = 1)
- OR 条件同理

- XOR条件
    - c = 1
    若 a = 0 则 b = 1
    若 b = 1 则 a = 0
    ······
    （满足无向图的条件）
> 详解请见蓝书P421页
```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cstring>
#include <cmath>
#include <map>
using namespace std;
const int maxn = 2e6+5;
const int N = 2e3+5;
int n,m;
struct edge{
    int v,next;
}e[maxn<<1];
int head[N],cnt;
void add(int u,int v){
    e[cnt].v = v;
    e[cnt].next = head[u];
    head[u] = cnt++;
}
void inits(){
    memset(head,-1,sizeof(head));
}
int Scc = 0;
int dfn[N],low[N],num;
int st[N],top = 0;
int ins[N];
int c[N];
void Tarjan(int x){
    dfn[x] = low[x] = num++;
    st[++top] = x;ins[x] = 1;
    for(int i = head[x];~i;i = e[i].next){
        int v = e[i].v;
        if(!dfn[v]){
            Tarjan(v);
            low[x] = min(low[x],low[v]);
        }
        else if(ins[v])low[x] = min(low[x],dfn[v]);
    }
    if(dfn[x]==low[x]){
        Scc++;
        int v;
        do{
            v = st[top--];
            c[v] = Scc;
            ins[v] = 0;
        }while(v!=x);
    }
}
int main(){
    cin>>n>>m;
    inits();
    for(int i = 0;i<m;i++){
        int a,b,c;
        char op[10];
        scanf("%d%d%d",&a,&b,&c);
        getchar();
        scanf("%s",op);
        if(op[0]=='A'){
            if(c==1){
                add(a,a+n);
                add(b,b+n);
            }
            else {
                add(a+n,b);
                add(b+n,a);
            }
        }
        else if(op[0]=='O'){
            if(c==1){
                add(a,b+n);
                add(b,a+n);
            }
            else {
                add(a+n,a);
                add(b+n,b);
            }
        }
        else {
            if(c==1){
                add(a,b+n);
                add(a+n,b);
                add(b,a+n);
                add(b+n,a);
            }
            else {
                add(a,b);
                add(a+n,b+n);
                add(b,a);
                add(b+n,a+n);
            }
        }
        //cout<<a<<" "<<b<<" "<<c<<" "<<op<<endl;
    }
    for(int i = 0;i<2*n;i++){
        if(!dfn[i]){
            Tarjan(i);
        }
    }
    for(int i = 0;i<n;i++){
        if(c[i]==c[i+n]){
            cout<<"NO"<<endl;
            return 0;
        }
    }
    cout<<"YES"<<endl;
    return 0;
}

```

### 扩展
现在状态可以改成三个

* [扩展域的并查集-食物链](https://vjudge.net/problem/POJ-1182)
容易验证确实对于原命题和原命题的逆命题是等价的