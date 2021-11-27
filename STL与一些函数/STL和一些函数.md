---
documentclass:
    - ctexart
geometry: "left=1cm,right=1cm,top=2cm,bottom=1cm"
header-includes:
 - \usepackage{fvextra}
 - \DefineVerbatimEnvironment{Highlighting}{Verbatim}{breaklines,commandchars=\\\{\}}
---


[TOC]

# STL 和一些函数与一些模板题


## vector
| 用法                      | 含义                                                                                                |
| ------------------------- | --------------------------------------------------------------------------------------------------- |
| v[i]                      | 直接以下标方式访问容器中的元素                                                                      |
| v.front()                 | 返回首元素                                                                                          |
| v.back()                  | 返回尾元素                                                                                          |
| v.push_back(x)            | 向表尾插入元素x                                                                                     |
| v.pop_back()              | 删除表尾元素                                                                                        |
| v.begin()                 | 返回指向首元素的迭代器                                                                              |
| v.end()                   | 返回指向尾元素的下一个位置的迭代器                                                                  |
| v.size()                  | 返回表长                                                                                            |
| v.empty()                 | 当表空时，返回真，否则返回假                                                                        |
| v.clear()                 | 删除容器中的所有的元素                                                                              |
| v.insert(it, val)         | 向迭代器it指向的元素前插入新元素val                                                                 |
| v.insert(it, n, x)        | 向迭代器it指向的元素前插入n个x                                                                      |
| v.insert(it, first, last) | 将由迭代器first和last所指定的序列[first, last)插入到迭代器it指向的元素前面                          |
| v.erase(it)               | 删除由迭代器it所指向的元素                                                                          |
| v.erase(first, last)      | 删除由迭代器first和last所指定的序列[first, last)                                                    |
| v.resize(n)               | 改变序列的长度，超出的元素将会被删除，如果序列需要扩展（原空间小于n），元素默认值将填满扩展出的空间 |
| v.resize(n, val)          | 改变序列的长度，超出的元素将会被删除，如果序列需要扩展（原空间小于n），将用val填满扩展出的空间      |
| v.swap(v2)                | 将s与另一个vector对象v2进行交换                                                                     |
| v.assign(first, last)     | 将序列替换成由迭代器first和last所指定的序列[first, last), [first, last)不能是原序列中的一部分       |
### 注意
* vector不自带v.find()函数

## set & multiset & unordered_set
| 用法                | 含义                                                 |
| ------------------- | ---------------------------------------------------- |
| s.begin()           | 返回指向第一个元素的迭代器                           |
| s.end()             | 返回指向最后一个元素的迭代器                         |
| s.clear()           | 清除所有元素                                         |
| s.empty()           | 如果集合为空，返回true                               |
| s.count(val)        | 返回值为val的元素的个数                              |
| s.erase(val)        | 删除集合中**所有**值为val的元素                      |
| s.erase(it)         | 删除集合中迭代器it指向的元素                         |
| s.erase(first,last) | 删除由迭代器first和last所指定的子集[first, last)     |
| s.equal_range(val)  | 返回有序/升序集合中val元素第一次和最后一次出现的位置 |
| s.find()            | 返回一个指向被查找到元素的迭代器                     |
| s.insert(val)       | 在集合中插入值为val的元素,返回值为pair<set<int>::iterator, bool>,pair::first 被设置为指向新插入元素的迭代器或指向等值的已经存在的元素的迭代器。成员 pair::second 是一个 bool 值，如果新的元素被插入，返回 true，如果等值元素已经存在（即无新元素插入），则返回 false。 (set.insert(x).second)                        |
| s.max_size()        | 返回集合能容纳的元素的最大限值                       |
| s.rbegin()          | 返回指向集合中最后一个元素的反向迭代器               |
| s.rend()            | 返回指向集合中第一个元素的反向迭代器                 |
| s.size()            | 集合中元素的数目                                     |
| s.swap(s2)          | 交换两个集合变量                                     |
| s.upper_bound(val)  | 返回大于val值元素的迭代器                            |
| s.lower_bound(val)  | 返回指向大于（或等于）val值的第一个元素的迭代器      |
### 注意

* set无法用下标访问，迭代器也不能进行数的加减，无法直接访问第k个元素
* set/multiset自动有序，无法使用排序函数
* unordered_set只能使用前向迭代器
* set、multiset头文件均为set，unordered_set头文件为unordered_set
* multiset的erase,**当传入的是值,将删除所有相同的值**;若传入的是迭代器,则只删除对应元素

## stack
| 用法         | 含义              |
| ------------ | ----------------- |
| st.empty()   | 堆栈为空则返回真  |
| st.pop()     | 移除栈顶元素      |
| st.push(val) | 在栈顶增加元素val |
| st.size()    | 返回栈中元素数目  |
| st.top()     | 返回栈顶元素      |

#### 注意

* 栈满足先入后出原则

## queue
| 用法      | 含义                     |
| --------- | ------------------------ |
| q.push()  | 入队                     |
| q.pop()   | 出队                     |
| q.front() | 返回首元素               |
| q.back()  | 返回末元素               |
| q.size()  | 输出现有元素的个数       |
| q.empty() | 队列为空返回1，反之返回0 |
#### 注意
* 队列满足先入先出原则
* 队列可以用[]访问

## priority_queue（优先队列）
| 用法        | 含义                |
| ----------- | ------------------- |
| p.empty()   | 判断是否为空        |
| p.push(val) | 队列尾部增加元素val |
| p.pop()     | 队列头部数据出队    |
| p.top()     | 返回头部数据        |
| p.size()    | 返回栈中元素个数    |

## deque（双向队列）
| 用法                      | 含义                                                     |
| ------------------------- | -------------------------------------------------------- |
| d.assign(first, last)     | 将[first, last)区间中的元素赋值给d                       |
| d.assign(n,val)           | 将n个val赋值给d                                          |
| d.at(index)               | 传回索引index所指的元素，如果index越界，抛出out_of_range |
| d.begin()                 | 返回首元素地址                                           |
| d.end()                   | 返回尾元素地址                                           |
| d.front()                 | 返回首元素                                               |
| d.back()                  | 返回尾元素                                               |
| d.clear()                 | 移除容器中所有元素                                       |
| d.empty()                 | 判断容器是否为空                                         |
| d.erase(pos)              | 删除pos位置的元素，传回下一个元素的位置                  |
| d.erase(first, last)      | 删除[first, last)区间的元素，传回下一个元素的位置        |
| d.insert(pos,val)         | 在pos位置插入val，传回新元素位置                         |
| d.insert(pos,n,val)       | 在pos位置插入n个val元素，无返回值                        |
| d.insert(pos,first, last) | 在pos位置插入在[first, last)区间的元素，无返回值         |
| d.pop_back()              | 删除最后一个元素                                         |
| d.pop_front()             | 删除头部元素                                             |
| d.push_back(val)          | 在尾部加入一个元素                                       |
| d.push_front(val)         | 在头部插入一个元素                                       |
| d.rbegin()                | 传回一个逆向队列的第一个元素                             |
| d.rend()                  | 传回一个逆向队列的最后一个元素的下一个位置               |
| d.resize(num)             | 重新指定队列的长度                                       |
| d.size()                  | 返回容器中实际元素的个数                                 |

## map & multimap & unordered_map

| 用法                         | 含义                                                  |
| ---------------------------- | ----------------------------------------------------- |
| mp[0] = x                    | 利用数组方式插入数据，0是键，x是值                    |
| mp.at(0) = x                 | 利用at执行插入操作                                    |
| mp.insert(make_pair(key,x))  | 利用insert插入pair(键，值)数据                        |
| mp.emplace(make_pair(key,x)) | 在映射中不存在主键key时执行插入操作                   |
| mp.size()                    | 返回mp的大小                                          |
| mp.count(key)                | 统计键为key的元素存在的映射数，存在返回1，不存在返回0 |
| mp.erase(it)                 | 根据迭代器删除元素                                    |
| mp.clear()                   | 清空映射                                              |
| mp.empty()                   | 判断映射是否为空                                      |
| mp.find(key)                 | 根据键key查找元素，找到以后返回迭代器                 |
| mp.rbegin()                  | 返回反向迭代器                                        |
| mp.rend()                    | 返回反向迭代器                                        |
| mp.swap(mp2)                 | 将mp和mp2进行交换                                     |
| mp.lower_bound(key)          | 返回map中第一个大于或等于key的迭代器指针              |
| mp.upper_bound(key)          | 返回map中第一个大于key的迭代器指针                    |

#### 三种插入操作示例

```cpp
    map<int, string> mapStudent;  
    mapStudent[0] = "student_zero";
    mapStudent.insert(pair<int, string>(1, "student_one")); 
    mapStudent.insert(map<int, string>::value_type (2, "student_two"));  
```
#### 注意

* map的键值key不可重复，具有严格的一一对应关系，而multimap可以一个键对应多个值
* map支持[ ]运算符，multimap不支持[ ]运算符
* map/multimap中的元素是自动按key升序排序，不能使用sort函数

## list

| 用法                          | 含义                                                                  |
| ----------------------------- | --------------------------------------------------------------------- |
|l.begin()|返回链表首地址|
|l.end()|返回链表尾地址|
|l.front()|返回首元素|
|l.back()|返回尾元素|
|l.push_back(elem)|插入元素到链表尾部|
|l.push_front(elem)|插入元素到链表头部|
|l.empty()|判断链表是否为空|
|l.insert(it, val1, val2))|在指定位置插入一个或多个元素|
|l.resize(n)|调整链表大小为n，超出n删除，少于n补0|
|l.clear()|清除|
|l.assign(len, val)|替换所有元素|
|l.assign(l2.begin(), l2.end())|替换所有元素为链表l2|
|l.swap(l2)|交换链表|
|l.merge(l2)|合并两个**有序**链表中的元素，调用后l2为空，可用greater<int>()|
|l.erase(it)|删除（区域中的）元素|
|l.remove(val)|删除值为val 的元素|

## bitset
其他参见《算法竞赛进阶指南》

```cpp
// 赋初值
bitset<100>foo('1110');
bitset<100>foo(100);

foo.to_ulong() //返回它转换为unsigned long的结果，如果超出范围则报错
foo.to_ullong() //返回它转换为unsigned long long的结果，如果超出范围则报错
foo.to_string() //返回它转换为string的结果
```

## lower_bound & upper_bound
| 用法                          | 含义                                                                  |
| ----------------------------- | --------------------------------------------------------------------- |
| lower_bound(first, last, val) | 在[first,last)区间进行二分查找，返回**大于或等于**val的第一个元素位置 |
| upper_bound(first, last, val) | 在[first,last)区间进行二分查找，返回**大于**val的第一个元素位置       |
#### 注意
* 只能作用于有序序列
* 如果所有元素都小于val，函数返回last的位置，此时last的位置是**越界**的！
* set::lower_bound()由于省略了再建树的过程，速度快于lower_bound()

## pair
#### 使用sort对pair类型进行排序
* std::pair::operator< 按标准规定会在两个 std::pair 的第一个元素互不小于对方的情况下比较第二个元素

## unique
* 将数组的不重复元素移到前面来，返回重复元素的首地址
* **使用前必须排序！**
```cpp
    //原型： iterator unique(iterator it_1,iterator it_2);返回值是一个迭代器，
    //它指向的是去重后容器中不重复序列的最后一个元素的下一个元素。
    vector<int> a;
    a.push_back(1);a.push_back(3);a.push_back(3);
    a.push_back(4);a.push_back(4);a.push_back(5);
    //用erase去重
    a.erase(unique(a.begin(),a.end()),a.end());
```

## string 
### substr ：
```cpp
#include <bits/stdc++.h>
using namespace std;
int main()
{
    ios::sync_with_stdio(false);
    string s="abcdefg";

    //s.substr(pos1,n)返回字符串位置为pos1后面的n个字符组成的串
    string s2=s.substr(1,5);//bcdef

    //s.substr(pos)//得到一个pos到结尾的串
    string s3=s.substr(4);//efg

    return 0;
}
```

### replace 操作
```cpp
#include <iostream>
#include <string>

int main ()
{
    std::string base="this is a test string.";
    std::string str2="n example";
    std::string str3="sample phrase";
    std::string str4="useful.";

    // replace signatures used in the same order as described above:

    // Using positions:                 0123456789*123456789*12345
    std::string str=base;           // "this is a test string."
    //第9个字符以及后面的长度5个字符被str2代替
    str.replace(9,5,str2);          // "this is an example string." (1)

    return 0;
}
```
### find 
```cpp
//查找所有子串在母串中出现的位置
//查找s 中flag 出现的所有位置。没有返回string::npos
    flag="a";
    position=0;
    int i=1;
    while((position=s.find(flag,position))!=string::npos)
    {
        cout<<"position  "<<i<<" : "<<position<<endl;
        position++;
        i++;
    }
    /*
    position 1:1
    position 2:21
    position 3:36
    */
```

## 其他
* __builtin_popcount() 计算一个数字的二进制中有多少个1，返回值1的个数。

* __gcd() 自带gcd    

#### 注意

* 以上可能不让用

## 一些函数

### __int128
&ensp;&ensp;__int128在gcc、codeblocks、vs2017都是不被支持的，不过__int128在Linux上可以编译并且能用。

```cpp
##include<iostream>
using namespace std;
inline __int128 read(){
    __int128 x = 0, f = 1;
    char ch = getchar();
    while(ch < '0' || ch > '9'){
        if(ch == '-')
            f = -1;
        ch = getchar();
    }
    while(ch >= '0' && ch <= '9'){
        x = x * 10 + ch - '0';
        ch = getchar();
    }
    return x * f;
}
inline void print(__int128 x){
    if(x < 0){
        putchar('-');
        x = -x;
    }
    if(x > 9)
        print(x / 10);
    putchar(x % 10 + '0');
}
int main(void){
    __int128 a = read();
    __int128 b = read();
    print(a + b);
    cout << endl;
    return 0;
}
```
### strchr
C 库函数 char *strchr(const char *str, int c) 在参数 str 所指向的字符串中搜索第一次出现字符 c（一个无符号字符）的位置。

#### 函数原型：
```cpp
char *strchr(const char *str, int c)
```
str -- 要被检索的 C 字符串。c -- 在 str 中要搜索的字符。函数返回在字符串 str 中第一次出现字符 c 的位置(地址)，如果未找到该字符则返回 NULL。
#### 示例
```cpp
##include <stdio.h>
##include <string.h>
int main (){
   const char str[] = "http://www.runoob.com";
   const char ch = '.';
   char *ret;
   ret = strchr(str, ch);
   printf("|%c| 之后的字符串是 - |%s|\n", ch, ret);
   return(0);
}
```
结果：
```
|.| 之后的字符串是 - |.runoob.com|
```

### 关于`sscanf`

一个字符串中读进与指定格式相符的数据。字符串操作是平常用途之多，截取，追加等等。也经常从文件中读取一行，取出所需要的字符串。基本有些是固定格式的。都可以用`sscanf`来得到。

例如：

```cpp
sscanf(line, "%[A-Z]%d", t, &r);
sscanf(line,"R%dC%d", &r, &c);
```

上述知识点可参见`Codeforces 1B`

https://vjudge.net/contest/387797#problem/B

### stringstream类

构造：
```c
stringstream::str (const string& s); 
```
sets s as the contents of the stream, discarding any previous contents.

3.stringstream清空，
```c
stringstream s; s.str("");
```
示例：将字符串"1 2 3 4 5"依次输入
```c
while(ss>>x)a[n++]=x;
```

### memcpy 函数
```cpp
int src[6][3]={{1,2,3},{4,5,6},{7,8,9},{1,2,3},{4,5,6},{7,8,9}};  
int des[6][3];//要小心，行数固定
memcpy(des,src,sizeof(src));
```

### __builtin_popcount()用于计算一个 32 位无符号整数有多少个位为1

Counting out the bits
可以很容易的判断一个数是不是2的幂次：清除最低的1位（见上面）并且检查结果是不是0.尽管如此，有的时候需要直到有多少个1被设置了，这就相对有点难度了。

GCC有一个叫做__builtin_popcount的内建函数，它可以精确的计算1的个数。尽管如此，不同于__builtin_ctz，它并没有被 翻译成一个硬件指令（至少在x86上不是）。相反的，它使用一张类似上面提到的基于表的方法来进行位搜索。这无疑很**高效**并且非常方便。

其他语言的使用者没有这个选项（尽管他们可以重新实现这个算法）。如果一个数只有很少的1的位，另外一个方法是重复的获取最低的1位，并且清除它。

```cpp
for(int i = 1;i <= 100; i++){
		cout<<__builtin_popcount(i)<<endl;
	}
```
# 计时
```cpp
#include <chrono>   
using namespace std;
using namespace chrono;

auto start = system_clock::now();

// do something...

auto end   = system_clock::now();
auto duration = duration_cast<microseconds>(end - start);
cout <<  "花费了" 
	<< double(duration.count()) * microseconds::period::num / microseconds::period::den 
	<< "秒" << endl;
```

# Windows对拍

在代码中增加

```cpp
    freopen("input0.in","r",stdin);
    freopen("output01.out","w",stdout);//两份代码output01要改成不同的文件
    
    fclose(stdin);
    fclose(stdout);
```

## 对拍c++代码
```cpp
//CheckData.cpp
#include<stdio.h>
#include<stdlib.h>
#include<time.h>
#include<string.h>
#include<random> 
#include<algorithm>
#include <chrono>
#define ll long long 
#define mmset(a,b) memset(a,b,sizeof(a))
using namespace std;
using namespace chrono;
const int INF = 0x3f3f3f3f; 
const int MaxnN=1e5,Maxn=10;
mt19937 rng;  //声明一个随机生成器 
template <typename T>
T RandomData(T a, T b){
    uniform_int_distribution<T> dist6(a, b); //指定范围 
	return dist6(rng);
}
 
void makeData(){
	FILE* fp=fopen("input0.in","w");
	int t = RandomData(1,Maxn);
	int n = RandomData(1,Maxn);
	//printf("%d\n",t); 
	fprintf(fp,"%d\n",t);//fprintf 从文件中写数据进去
	fprintf(fp,"%d\n",n);//模拟手动输入写文件
	for(int i=1;i<=n;i++){
		int a=RandomData(1,Maxn);
		int b=RandomData(1,Maxn);
		fprintf(fp,"%d %d\n",a,b);
	}
	fclose(fp);
} 
int main(){
	rng.seed(time(0));     //将时间作为随机生成器随机种子 
	for(int i = 1; i < 50; i++){
		makeData();//生成数据，一共生成循环这么多次 
		system("01.exe");
		system("02.exe");
		printf("Number:%d\n",i);
		//比较数据 
		if(system("fc output01.out output02.out"))
        {
            printf("Wrong Asnwer\n");
            break;
        } 
	}
	return 0;
} 
```

# 模板题

##  中缀表达式求值
  
```cpp
/*
中缀表达式求值，有两种做法：
1. 转换为后缀表达式再求值：O(N)
注意这个题目还要处理负数的符号位的情况，例如 -3+1
- 到底是负号还是减号?
特例：
((((((-1)
(-1)))
(1+2)^((-1+1))
包括可能括号不匹配的情况;
^ 次方
  
*/
#include <cmath>
#include <iostream>
#include <vector>
using namespace std;
  
vector<int> nums;
vector<char> ops;
string s;
  
// 优先级
int grade(char op) {
    switch (op) {
        case '(':
            return 1;
        case '+':
        case '-':
            return 2;
        case '*':
        case '/':
            return 3;
        case '^':
            return 4;
    }
    return 0;
}
// 处理后缀表达式中的一个运算符
void calc(char op) {
    // 从栈顶取出两个数
    int y = nums.back();
    nums.pop_back();
    int x = 0;
    // 如果出现负号的情况：
    // 1. 可能没有多的操作数了，前一个操作数当做 0 看待
    // 2. 可能前面是左括号 (，需要先把这个负数计算出来（0 - y = -y）
    if (nums.size() && !(op == '-' && ops.size() && ops.back() == '(')) {
        x = nums.back();
        nums.pop_back();
    }
    int z;
    switch (op) {
        case '+':
            z = x + y;
            break;
        case '-':
            z = x - y;
            break;
        case '*':
            z = x * y;
            break;
        case '/':
            z = x / y;
            break;
        case '^':
            z = pow(x, y);
    }
    // cout << x << op << y << " = " << z << endl;
    // 把运算结果放回栈中
    nums.push_back(z);
}
int main() {
    cin >> s;
    int val = 0;
    for (int i = 0; i < s.length(); i++) {
        // 多位数， 并且表达式是使用字符串逐字符存储的，我们只需要稍加判断，把连续的一段数字看成一个数即可。
        if (s[i] >= '0' && s[i] <= '9') {
            val = val * 10 + s[i] - '0';
            if (s[i + 1] >= '0' && s[i + 1] <= '9') continue;
            // 后缀表达式的一个数，直接入栈
            nums.push_back(val);
            val = 0;
        }
        // 左括号：直接入栈
        else if (s[i] == '(')
            ops.push_back('(');
        // 右括号：一直出栈直到遇见左括号
        else if (s[i] == ')') {
            while (ops.size() && ops.back() != '(') {
                // 出栈一个符号就计算一下
                char op = ops.back();
                ops.pop_back();
                calc(op);
            }
            // 左括号出栈（如果有的话）
            if (ops.size()) ops.pop_back();
        }
        // 运算符：只要栈顶符号的优先级不低于新符号，就不断取出栈顶并输出，最后把新符号进栈，其实只在这里使用了优先级的比较
        else {
            while (ops.size() && grade(ops.back()) >= grade(s[i])) {
                char op = ops.back();
                ops.pop_back();
                calc(op);
            }
            ops.push_back(s[i]);
        }
    }
    // 剩下符号再计算一下
    while (ops.size() && nums.size() >= 2) {
        char op = ops.back();
        ops.pop_back();
        calc(op);
    }
    // 后缀表达式栈中最后剩下的数就是答案
    cout << nums.front() << endl;
    return 0;
}
  
```

## 二维Hash,求在大矩阵中小矩阵存在问题

给定一个 M 行 N 列的 01 矩阵（只包含数字 0 或 1 的矩阵），再执行 Q 次询问，每次询问给出一个 A 行 B 列的 01 矩阵，求该矩阵是否在原矩阵中出现过。

A<100，M,N,B<1000，Q<1000

```cpp
#include <ctime>
#include <cstdlib>
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
#include <cassert>   // assert
#include<set>
#include<map>
#include<deque>
#include<stack>
#include<unordered_set>
using namespace std;
#define debug(x) cout<<"###"<<x<<"###"<<endl;
const int INF=0x3f3f3f3f,mod=1e9,Maxn=1e6+5;
const double eps=1e-8;
typedef long long ll;
typedef unsigned long long ull;
ull p[Maxn],hs[Maxn];
const ull base = 131;
ull Hash[1003][1003];
int n,m,a,b;
void init(){
    p[0] = 1;
    for(int i=1;i<Maxn;i++){
            p[i] = p[i-1]*base;
    }
}
ull GetHash(ull *h,int l,int r ){
	return h[r]-h[l-1]*p[r-l+1];//一维上的Hash处理
} 

unordered_set<ull> st;
int main(){
	init();
    cin>>m>>n>>a>>b;//m*n大矩阵，a*b小矩阵
    ull ch;
    for(int i=1;i<=m;i++){
        for(int j=1;j<=n;j++){
           scanf("%1d",&ch);
           Hash[i][j]=Hash[i][j-1]*base+(ull)ch;
           //计算每一行Hash
        }
    }
    for(int i=b;i<=n;i++){
        int l=i-b+1,r=i;
        ull s=0;
        for(int j=1;j<=m;j++){
            s=s*p[b]+GetHash(Hash[j],l,r);
            if(j>a){
                s-=GetHash(Hash[j-a],l,r)*p[a*b];
            }
            if(j>=a){
                st.insert(s);
			}
        }
    }    
    int q;
    cin>>q;
    while(q--){
        ull s=0;
        for(int i=1;i<=a;i++){
            for(int j=1;j<=b;j++){
                scanf("%1d",&ch);
                //debug(ch);
                s=s*base+ch;
            }//依次计算小矩阵Hash
        }
        if(st.count(s)){
            cout<<1<<endl;
        }
        else{
            cout<<0<<endl;
        }
    }
    return 0;
}
```
