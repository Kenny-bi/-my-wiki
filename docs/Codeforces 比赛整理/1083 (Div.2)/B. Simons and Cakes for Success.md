

好的，这是将您提供的英文算法题面转换为规范中文 Markdown 格式的结果：

## B. Simons and Cakes for Success

时间限制：每个测试点1秒
内存限制：每个测试点256兆字节

等我成功了，我们就一起分享蛋糕！

— 舜

西蒙斯有 $n$ 个朋友和大量蛋糕。为了公平地分配蛋糕，请你帮助他解决以下问题：

找到最小的正整数 $k$，使得 $n$ 能整除 $k^n$。

可以证明，在给定的约束条件下，答案总是存在的。

## 输入格式

每个测试包含多个测试用例。第一行包含测试用例的数量 $t$ ($1 \le t \le 100$)。测试用例的描述如下。

每个测试用例只有一行，包含一个整数 $n$ ($2 \le n \le 10^9$) — 西蒙斯的朋友数量。

## 输出格式

对于每个测试用例，输出一个整数 — 你找到的最小 $k$。

## 样例输入

```
4
8
12
369
55635800
```

## 样例输出

```
2
6
123
2090
```

## 样例说明

在第一个测试用例中：

*   $1^8 = 1$，而 $8$ 不能整除 $1$；
*   $2^8 = 256$，而 $8$ 能整除 $256$，因为 $256 = 8 \times 32$。

因此，最小的 $k$ 是 $2$。

在第二个测试用例中，$12$ 能整除 $6^{12} = 2176782336$，因为 $2176782336 = 12 \times 181398528$。


# 帮我分析以下三种方法的时间复杂度，以及最坏情况下是什么样子，以及为什么最后一种明明看起来最暴力，但是奇怪的是时间确实最短的，这是为什么



# T3 代码
```cpp
// Problem: CF 2205 B
// Contest: Codeforces - Codeforces Round 1083 (Div. 2)
// URL: https://codeforces.com/contest/2205/problem/B
// Memory Limit: 256 MB
// Time Limit: 1000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;


void solve(){
	int n;
	cin>>n;
	int ans=1;
	int cnt=2;
	while(n!=1){
		if(n%cnt==0){
			ans*=cnt;
			while(n%cnt==0) n/=cnt;
		}
		cnt++;
	}
	cout<<ans<<endl;
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




# T9代码
```cpp
// Problem: CF 2205 B
// Contest: Codeforces - Codeforces Round 1083 (Div. 2)
// URL: https://codeforces.com/contest/2205/problem/B
// Memory Limit: 256 MB
// Time Limit: 1000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;

int isprime(int x){
	if(x<=1) return 0;
	if(x==2) return 1;
	if(x%2==0) return 0;
	for(int i=3;i<=sqrt(x);i+=2){
		if(x%i==0) return 0;
	}
	return 1;
}

void solve(){
	int n;
	cin>>n;
	int ans=1;
	int cnt=2;
	
	if(isprime(n)){
		cout<<n<<endl;
		return ;
	}
	
	while(n!=1){
		if(n%cnt==0){
			ans*=cnt;
			while(n%cnt==0) n/=cnt;
		}
		
		if(isprime(n)){
			ans*=n;
			break;
		}
		
		cnt++;
	}
	cout<<ans<<endl;
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




# AC  代码
```cpp
// Problem: CF 2205 B
// Contest: Codeforces - Codeforces Round 1083 (Div. 2)
// URL: https://codeforces.com/contest/2205/problem/B
// Memory Limit: 256 MB
// Time Limit: 1000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;


void solve(){
	int n;
	cin>>n;
	int ans=1;
	int cnt=2;
	
	for(int i=2;i*i<=n;i++){
		if(n%i==0){
			ans*=i;
			while(n%i==0) n/=i;
		}
	}
	if(n>1) ans*=n;
	
	cout<<ans<<endl;
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