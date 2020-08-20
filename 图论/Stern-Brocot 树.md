# Stern-Brocot 树

![](img\20190802152013505.png)

给定输入N，输出0到1之间分母小于或等于N的真分数，并排序，按照上图来看就是每一行。

怎么求第n行的SB树

```cpp
#include<bits/stdc++.h>
using namespace std;
int n;
typedef pair<int, int> P;
vector<P> vec;
void dfs(int l1 , int l2 , int r1 , int r2) //l1/l2 , r1/r2
{
	if (l2 + r2 > n) return ;
	//Stern-Brocot树 左侧总是最小的，右侧最大的，那么总是优先输入左侧，再输入中间的，最后输入右侧的数
	dfs(l1 , l2 , l1 + r1 , l2 + r2);
	// cout << l1 + r1 << "/" << l2 + r2 << " ";
	vec.push_back(P(l1 + r1, l2 + r2));
	dfs(l1 + r1 , l2 + r2 , r1 , r2);
}

int main() {
	n = 7;
	// cout << "0/1 ";
	vec.push_back(P(0, 1));
	dfs(0 , 1 , 1 , 1); //会按从小到大的次序输出结果
	// cout << "1/1" << endl;
	vec.push_back(P(1, 1));

	for (auto c : vec) {
		cout << c.first << "/" << c.second << " ";
	}
	cout << endl;
	return 0;
}
```

