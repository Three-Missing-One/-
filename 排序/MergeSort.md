[TOC]
# 带求逆序对的写法（个人感觉比树状数组好用）
```c++
#include <stdio.h>
long long a[100005],b[100005];
long long cnt=0;
void m(int l,int r){
	if(l>=r)return ;
	int mid=(l+r)/2;
	int p=l;
	int q=mid+1;
	int pos=l;
	m(l,mid);
	m(mid+1,r);
	while(p<=mid||q<=r){
		if(q>r||(p<=mid&&a[p]<=a[q])){
			b[pos++]=a[p++];
		}
		else {
			cnt+=mid-p+1;
			b[pos++]=a[q++];
		}
	}
	for(int i=l;i<=r;i++){
		a[i]=b[i];
	}
}
int main(){
	int n;
	scanf("%d",&n);
	for(int i=1;i<=n;i++){
		scanf("%lld",&a[i]);
	}
	m(1,n);
	printf("%lld",cnt);
}
```

## 树状数组求逆序对

```cpp
#include<iostream>
#include<algorithm>
#include<math.h>
#include<cstdio>
#include<string> 
#include<string.h>
#include<list>
#include<queue>
#include<sstream>
#include<vector>
#include<set>
#include<map>
#include<deque>
#include<stack>
using namespace std;
#define debug(x) cout<<"###"<<x<<"###"<<endl;
const int INF=0x3f3f3f3f,Maxn=5e5+10;
typedef long long ll;
#define lowbit(x) ((x)&-(x))
int tree[Maxn],n;
void add(int x,int d){
	while(x<=n){
		tree[x]+=d;
		x+=lowbit(x);
	}
}
ll sum(int x){
	int sum=0;
	while(x>0){
		sum+=tree[x];
		x-=lowbit(x);
	}
	return sum;
}
struct Node{
	ll v,id;
}node[Maxn];
bool cmp(Node a,Node b){
	return a.v<b.v;
}
int a[Maxn];
int main(){
	while(scanf("%d",&n),n){
		ll cnt=0;
		memset(tree,0,sizeof(tree));
		for(int i=1;i<=n;i++){
			scanf("%lld",&node[i].v);
			node[i].id=i;
		}
		sort(node+1,node+1+n,cmp);
		for(int i=1;i<=n;i++){
			a[node[i].id]=i;
		}
		for(int i=n;i>=1;--i){
			cnt+=sum(a[i]);
			add(a[i],1);
		}
		cout<<cnt<<endl;
	}
}
```
