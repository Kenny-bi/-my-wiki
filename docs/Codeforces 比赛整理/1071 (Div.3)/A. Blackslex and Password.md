


## A. Blackslex and Password
时间限制：每测试点 1 秒  
内存限制：每测试点 256 兆字节  

## 问题描述
Blackslex 正在为 Gean Dev 设计一个登录系统，发现大多数用户使用弱密码。

为了解决这个问题，他根据两个变量 $k$ 和 $x$，为所有密码设置了以下条件。每个密码是一个长度为 $n$ 的字符串 $s$，必须满足以下属性：
1. $s$ 仅使用英文字母表中的前 $k$ 个小写字母。
2. 对于每一对索引 $1 \le i < j \le n$，如果 $(j-i)$ 能被 $x$ 整除，那么字母 $s_i$ 和 $s_j$ 必须不同。

请找出最小的整数 $n$，使得不存在满足条件的长度为 $n$ 的字符串。

## 输入格式
第一行包含一个整数 $t$ $(1 \le t \le 500)$ —— 测试用例的数量。

每个测试用例包含一行，有两个整数 $k$ 和 $x$ $(1 \le k \le 26, 1 \le x \le 15)$。

## 输出格式
对于每个测试用例，输出最小的 $n$。

## 样例输入
```
3
2 1
3 2
1 5
```
## 样例输出
```
3
7
6
```
## 样例说明
对于第一个测试用例，不存在长度为 $n=3$ 的有效字符串。对于 $n=2$，一个有效的例子是 `ab`。注意，当 $n=2$ 时，满足 $(j-i)$ 能被 $x=1$ 整除且 $1 \le i < j \le n$ 的唯一数对是 $(1,2)$。

对于第二个测试用例，不存在长度为 $n=7$ 的有效字符串。对于 $n=6$，一个有效的例子是 `aabccb`。注意，当 $n=6$ 时，所有满足 $(j-i)$ 能被 $x=2$ 整除且 $1 \le i < j \le n$ 的数对包括 $(1,3)$、$(1,5)$、$(2,4)$、$(2,6)$、$(3,5)$ 和 $(4,6)$。

对于第三个测试用例，不存在长度为 $n=6$ 的有效字符串。对于 $n=5$，一个有效的例子是 `aaaaa`。注意，当 $n=5$ 时，没有满足 $(j-i)$ 能被 $x=5$ 整除且 $1 \le i < j \le n$ 的数对。



# 赛时AC 代码
```cpp
// Problem: CF 2179 A
// Contest: Codeforces - Codeforces Round 1071 (Div. 3)
// URL: https://codeforces.com/contest/2179/problem/A
// Memory Limit: 256 MB
// Time Limit: 1000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define ios ios::sync_with_stdio(false);cin.tie(nullptr);

void solve(){
	int a,b;
	cin>>a>>b;
	cout<<a*b+1<<endl;
	
	
	return ;
}

int main(){
	ios;
	int t;
	cin>>t;
	while(t--){
		solve();
	}
	return 0;
}
```