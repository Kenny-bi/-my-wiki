


## B. Mickey Mouse Constructive

时间限制：1.5 秒  
内存限制：256 MB

## 问题描述

给定一个数组 $a$，令 $f(a)$ 表示将 $a$ 划分成一个或多个子数组 $^*$ 的方式数，满足：

- 每个元素恰好属于一个子数组。
- 所有子数组的和相等。

例如，如果 $a=[1,1]$，那么 $f(a)=2$，因为存在两种划分 $[1,1]$ 的方式：

- $[1,1]$，其中唯一的子数组和为 $2$。
- $[1]+[1]$，两个子数组的和均为 $1$。

给你两个整数 $x$ 和 $y$。考虑所有长度为 $x+y$ 的数组 $a$，其中包含 $x$ 个 $1$ 和 $y$ 个 $-1$（顺序任意）。求 $f(a)$ 的最小值。由于答案可能很大，请输出答案对 $676767677$ 取模的结果。此外，你需要构造一个达到该最小值的数组。

$^*$ 数组 $b$ 是数组 $c$ 的子数组，如果 $b$ 可以通过从 $c$ 的开头删除若干（可能为零或全部）元素以及从结尾删除若干（可能为零或全部）元素得到。

## 输入格式

每个测试包含多个测试用例。第一行包含测试用例数 $t$（$1 \le t \le 10^4$）。接下来是每个测试用例的描述。

每个测试用例的第一行包含两个整数 $x$ 和 $y$（$0 \le x, y \le 2 \cdot 10^5$）。保证 $x + y \ge 1$。

保证所有测试用例的 $x$ 之和不超过 $2 \cdot 10^5$，所有测试用例的 $y$ 之和也不超过 $2 \cdot 10^5$。

## 输出格式

对于每个测试用例，输出两行：

- 第一行：在所有有效数组 $a$ 中 $f(a)$ 的最小值对 $676767677$ 取模的结果。
- 第二行：一个达到该最小值的示例数组。

注意：你需要先最小化 $f(a)$，然后将该最小值对 $676767677$ 取模输出，而不是寻找 $f(a) \bmod 676767677$ 的最小可能值。

## 样例输入

```
4
2 0
1 1
6 7
1 3
```

## 样例输出

```
2
1 1
1
1 -1
1
-1 1 -1 1 -1 1 -1 1 -1 1 -1 1 -1
2
-1 -1 -1 1
```

## 样例说明

在第一个测试用例中，$x=2, y=0$。唯一可能的数组是 $a=[1,1]$，如上所述 $f(a)=2$。

在第二个测试用例中，$x=1, y=1$。一个最小化 $f(a)$ 的可行数组是 $a=[1,-1]$，此时 $f(a)=1$（唯一的划分方式是 $[[1,-1]]$）。





# 我真服了，我感觉这就是对的
```cpp
// Problem: CF 2211 B
// Contest: Codeforces - Nebius Round 2 (Codeforces Round 1088, Div. 1 + Div. 2)
// URL: https://codeforces.com/contest/2211/problem/B
// Memory Limit: 256 MB
// Time Limit: 1500 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;

int fun(int x){
	int cnt=0;
	for(int i=1;i<=sqrt(x);i++){
		if(x%i==0){
			if(i*i!=x) cnt+=2;
			else cnt++;
		}
	}
	return cnt;
}


void solve(){
	int x,y;
	cin>>x>>y;
	if(x==0||y==0){
		if(x==0){
			cout<<fun(y)<<endl;
		}else cout<<fun(x)<<endl;
	}else{
		if(abs(x-y)>=2) cout<<2<<endl;
		else cout<<1<<endl;
	}
	
	
	
	while(x||y){
		if(x){
			cout<<1<<" ";
			x--;
		}
		if(y){
			cout<<-1<<" ";
			y--;
		}
	}
	cout<<endl;
	return ;
}


int main(){
	int t;
	cin>>t;
	while(t--){
		solve();
	}
	return 0;
}
```






# 终于AC 
- 主要问题在于构造方式，还有看透问题的本质
- 其实可以直接看总和，然后就相当于几个数字（排列组合）
```cpp
// Problem: CF 2211 B
// Contest: Codeforces - Nebius Round 2 (Codeforces Round 1088, Div. 1 + Div. 2)
// URL: https://codeforces.com/contest/2211/problem/B
// Memory Limit: 256 MB
// Time Limit: 1500 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;

int fun(int x){
	int cnt=0;
	for(int i=1;i<=sqrt(x);i++){
		if(x%i==0){
			if(i*i!=x) cnt+=2;
			else cnt++;
		}
	}
	if(cnt==0) return 1;
	return cnt;
}


void solve(){
	int x,y;
	cin>>x>>y;
	
	cout<<fun(abs(x-y))<<endl;	
	for(int i=0;i<x;i++) cout<<1<<" ";
	for(int i=0;i<y;i++) cout<<-1<<" ";
	cout<<endl;
	return ;
}


int main(){
	int t;
	cin>>t;
	while(t--){
		solve();
	}
	return 0;
}
```