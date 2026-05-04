


## A. Friendly Numbers

时间限制：每个测试点1秒  
内存限制：每个测试点256兆字节

## 问题描述

对于一个整数 $x$，如果存在另一个整数 $y$ 满足以下条件，则称 $y$ 是 $x$ 的友好数：

$y - d(y) = x$，其中 $d(y)$ 表示 $y$ 的各位数字之和。

对于给定的整数 $x$，请确定它有多少个友好数。

## 输入格式

每个测试包含多个测试用例。第一行包含一个整数 $t$，表示测试用例的数量 ($1 \leq t \leq 500$)。  
接下来是每个测试用例的描述。

每个测试用例为单独一行，包含一个整数 $x$ ($1 \leq x \leq 10^9$)。

## 输出格式

对于每个测试用例，输出一个整数——表示该 $x$ 的友好数的个数。

## 样例输入

```
3
1
18
998244360
```

## 样例输出

```
0
10
10
```

## 样例说明

数字 $1$ 没有任何友好数。

数字 $18$ 有 $10$ 个友好数：即从 $20$ 到 $29$ 的所有整数。  
例如，$20 - d(20) = 20 - 2 = 18$。

数字 $998244360$ 有 $10$ 个友好数：

998244400  
998244401  
998244402  
998244403  
998244404  
998244405  
998244406  
998244407  
998244408  
998244409



# 这个题告诉我们打表的时候看清楚(AC  代码)
```cpp
// Problem: CF 2197 A
// Contest: Codeforces - Codeforces Round 1079 (Div. 2)
// URL: https://codeforces.com/contest/2197/problem/A
// Memory Limit: 256 MB
// Time Limit: 1000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;

int d(int x){
	int sum=0;
	while(x){
		sum+=x%10;
		x/=10;
	}
	return sum;
}

void solve(){
	int n;
	cin>>n;
	if(n%9==0){
		int flag=0;
		for(int i=0;i<300;i++){
			int t=n+i;
			if(t-d(t)==n){
				flag=1;
				break;
			}
		}
		
		if(flag) cout<<10<<endl;
		else cout<<0<<endl;
	}
	else cout<<0<<endl;
	
	return ;
}


int main(){
	// vector<int> st(300);
// 	
	// for(int i=0;i<=1000;i++){
		// cout<<(i-d(i))/9<<"-- ";
		// //st[(i-d(i))/9]=1;
		// cout<<i<<" "<<i-d(i)<<endl;
	// }
// 	
	// for(int i=0;i<300;i++){
		// if(!st[i]) cout<<i<<endl;
	// }
// 	
	int t;
	cin>>t;
	while(t--){
		solve();
	}
	return 0;
}
```