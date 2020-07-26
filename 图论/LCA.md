# 关于LCA

### 介绍有关算法的叫法

* 离线算法其实就是将多个询问一次性解决。离线算法往往是与在线算法相对的。例如求LCA的算法中，树上倍增属于在线算法，在对树进行O(n)预处理后，每个询问用 O(log_2n) 复杂度回答。而离线的Tarjan算法则是用O(n+q)时间将询问一次性全部回答。


  
 * [P3379【模板】最近公共祖先(LCA)]https://www.luogu.com.cn/problem/P3379

> Tarjan(离线)算法

因为完全是板子题,题解基本就是直接的模板
```c++
#include <bits/stdc++.h>
using namespace std;
const int maxn = 500005;
int head[maxn],cnt=0;
int fa[maxn],vis[maxn],ans[maxn];
struct edge{
	int u,v,next;
}e[maxn<<1];
struct note { int node, id; }; //询问以结构体形式保存
vector<note> ques[maxn];
void add(int u,int v){
	e[cnt].u = u;
	e[cnt].v = v;
	e[cnt].next = head[u];
	head[u] = cnt++;
}
int get(int x){
	if(fa[x]==x)return x;
	else return fa[x]=get(fa[x]);//路径压缩需要
}
void dfs(int u,int from){
	for(int i=head[u];~i;i=e[i].next){
		int v = e[i].v;
		if(v==from)continue;
		dfs(v,u);
		fa[v]=u;
	}
	int len = ques[u].size();
	 for (int i = 0; i < len; i++) 
        if (vis[ques[u][i].node]) 
            ans[ques[u][i].id] = get(ques[u][i].node); 
    
    //访问完毕回溯
    vis[u] = 1; 
}
int main(){
	memset(head,-1,sizeof(head));
	int n,m,s;
	cin>>n>>m>>s;
	for(int i=0;i<=n;i++)fa[i]=i;
	for(int i=0;i<n-1;i++){
		int x,y;
		scanf("%d%d",&x,&y);
		add(x,y);
		add(y,x);
	}
	for(int i=0;i<m;i++){
		int x,y;
		scanf("%d%d",&x,&y);
		note no;                    //说说这里，id记录了第几个被询问
		no.node=y;                  //node表示第x点与那个点有关联
		no.id=i;                        
		ques[x].push_back(no);
		no.node=x;                  //要入队两次
		no.id = i;
		ques[y].push_back(no);
	}
	dfs(s,0);
	for(int i=0;i<m;i++){
		cout<<ans[i]<<endl; 
	}
} 
```
* [算法详情]https://blog.csdn.net/weixin_34342578/article/details/88835114?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.channel_param

## 另外还有在线算法（倍增算法）enmmmmm还不不会