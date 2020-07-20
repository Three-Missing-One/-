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