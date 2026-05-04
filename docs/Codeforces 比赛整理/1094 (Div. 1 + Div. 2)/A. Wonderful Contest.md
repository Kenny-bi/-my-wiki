




## A. A Wonderful Contest

时间限制：1秒  
内存限制：256MB  

## 问题描述  

作为一个热爱比赛的人，你现在需要参加一场精彩的 OI 竞赛。  

这场竞赛有 $n$ 道题，每道题满分为 $100$。第 $i$ 道题有 $a_i$ 个子任务，每个子任务的分数为 $\frac{100}{a_i}$。保证 $a_i$ 是 $100$ 的约数。  

现在，若干选手将参加这场竞赛。假设某位选手解决了第 $i$ 道题的 $x_i$（$0 \le x_i \le a_i$）个子任务，那么他在第 $i$ 道题上的得分为 $x_i \cdot \frac{100}{a_i}$。该选手的总分为所有题目得分之和，即  
\[
\sum_{i=1}^n x_i \cdot \frac{100}{a_i}.
\]  

为了证明这场竞赛确实是一场精彩的竞赛，你需要检查是否可能得到从 $0$ 到 $100 \cdot n$（包含两端）的每一个整数总分。形式化地说，你需要判断以下命题是否成立：  

对于每个整数 $k$ 满足 $0 \le k \le 100 \cdot n$，都存在一个长度为 $n$ 的数组 $x$（$0 \le x_i \le a_i$），使得  
\[
k = \sum_{i=1}^n x_i \cdot \frac{100}{a_i}.
\]  

## 输入格式  

每个测试包含多个测试用例。第一行包含整数 $t$（$1 \le t \le 100$），表示测试用例的数量。接下来是每个测试用例的描述。  

每个测试用例的第一行包含一个整数 $n$（$1 \le n \le 10$），表示竞赛中的题目数量。  

第二行包含 $n$ 个整数 $a_1, a_2, \dots, a_n$（$1 \le a_i \le 100$），表示每道题的子任务数量。保证每个 $a_i$ 都是 $100$ 的约数。  

## 输出格式  

对于每个测试用例，如果可以得到 $0$ 到 $100 \cdot n$ 之间的任意整数总分，则输出 `"Yes"`，否则输出 `"No"`。  

你可以以任意大小写输出答案。例如，`"yEs"`、`"yes"`、`"Yes"` 和 `"YES"` 都会被识别为肯定回答。  

## 样例输入  

```
5
2
100 20
2
10 10
3
50 100 25
4
1 2 5 20
10
100 1 2 4 5 10 20 25 50 100
```  

## 样例输出  

```
Yes
No
Yes
No
Yes
```  

## 样例说明  

**第一个测试用例**：对于每个整数 $k$（$0 \le k \le 200$），都可以得到恰好为 $k$ 的总分。例如，当 $k = 10$ 时，选手在第一题通过 $0$ 个子任务、第二题通过 $2$ 个子任务，得分为 $0 \cdot \frac{100}{100} + 2 \cdot \frac{100}{20} = 10$。  

**第二个测试用例**：当 $k = 95$ 时，可以证明无法得到恰好为 $95$ 的总分。  

**第三个测试用例**：对于每个整数 $k$（$0 \le k \le 300$），都可以得到恰好为 $k$ 的总分。例如，当 $k = 233$ 时，选手在三道题中分别通过 $25$、$83$ 和 $25$ 个子任务，得分为 $25 \cdot \frac{100}{50} + 83 \cdot \frac{100}{100} + 25 \cdot \frac{100}{25} = 233$。




# AC  代码
- 我的想法就是直接使用动态规划传递  我们能凑出来的值
- 最后检查是不是所有值都可以被凑出来
```cpp
// Problem: CF 2222 A
// Contest: Codeforces - Spectral::Cup 2026 Round 1 (Codeforces Round 1094, Div. 1 + Div. 2)
// URL: https://codeforces.com/contest/2222/problem/A
// Memory Limit: 256 MB
// Time Limit: 1000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;


void solve(){
	int n;
	cin>>n;
	vector<int> a(n);
	vector<int> ans;
	for(int i=0;i<n;i++){
		cin>>a[i];
		ans.push_back(100/a[i]);
	}
	sort(ans.begin(),ans.end());
	ans.erase(unique(ans.begin(),ans.end()),ans.end());
	
	int N=n*100;
	vector<int> f(n*100+1,0);
	f[0]=1;
	for(int i=0;i<ans.size();i++) f[ans[i]]=1;
	for(int i=1;i<=N;i++){
		for(int j=0;j<ans.size()&&ans[j]<=i;j++){
			f[i]=max(f[i], f[i-ans[j]] ) ;
		}
	}
	
	for(int i=1;i<=N;i++){
		if(!f[i]){
			cout<<"No"<<endl;
			return ;
		}
	}
	cout<<"Yes"<<endl;
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