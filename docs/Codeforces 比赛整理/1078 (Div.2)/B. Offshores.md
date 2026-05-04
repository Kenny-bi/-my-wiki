




## B. Offshores
时间限制：每个测试点 1.5 秒
内存限制：每个测试点 256 MB

## 问题描述
马克非常爱钱，他把大部分钱都存在银行里。他的钱存放在 $n$ 家不同的银行。在第 $i$ 家银行，他有 $a_i$ 卢布。

有一天，马克决定将所有钱集中到一家银行，因此他需要把钱从一个银行账户转到另一个账户。所有银行间转账的安排方式都是一样的：他一次只能转账 $x$ 卢布，并且考虑到所有费用，实际上只有 $y$ 卢布会被存入目标账户（因为银行想要盈利，所以有 $y \le x$）。

马克可能无法将所有钱都转移到一家银行，但他想要找到最终可以在任意一家银行里获得的最大卢布数量。

## 输入格式
每个测试包含多个测试用例。第一行包含测试用例的数量 $t$ $(1 \le t \le 10^4)$。接下来是测试用例的描述。

每个测试用例的第一行包含三个整数 $n$, $x$, 和 $y$ $(2 \le n \le 2 \cdot 10^5$, $1 \le y \le x \le 10^9)$ —— 分别表示银行的数量、转账金额和实际到账金额。

每个测试用例的第二行包含 $n$ 个整数 $a_1, a_2, \dots, a_n$ $(1 \le a_i \le 10^9)$ —— 表示每家银行初始的卢布数量。

保证所有测试用例的 $n$ 值之和不超过 $2 \cdot 10^5$。

## 输出格式
对于每个测试用例，输出一个数字 —— 表示在任意一家银行可以获得的最大卢布数量。

## 样例输入
```
6
4 5 4
10 9 8 7
5 13 11
47 52 64 13 91
2 1 1
1000 1000
3 15 14
34 43 52
6 7 6
15 17 14 15 12 16
2 15 10
45 44
```

## 样例输出
```
25
229
2000
113
72
74
```

## 样例说明
在第一个测试用例中，最优的转账顺序可能如下所示：
$1 \rightarrow 4, 1 \rightarrow 4, 4 \rightarrow 3, 4 \rightarrow 3, 4 \rightarrow 3, 3 \rightarrow 2, 3 \rightarrow 2, 3 \rightarrow 2, 3 \rightarrow 2$。

让我们展示每一步之后总和的变化：
$(10,9,8,7)$
$\xrightarrow{1 \rightarrow 4}(5,9,8,11)\xrightarrow{1 \rightarrow 4}(0,9,8,15)$
$\xrightarrow{4 \rightarrow 3}(0,9,12,10)\xrightarrow{4 \rightarrow 3}(0,9,16,5)\xrightarrow{4 \rightarrow 3}(0,9,20,0)$
$\xrightarrow{3 \rightarrow 2}(0,13,15,0)\xrightarrow{3 \rightarrow 2}(0,17,10,0)\xrightarrow{3 \rightarrow 2}(0,21,5,0)\xrightarrow{3 \rightarrow 2}(0,25,0,0)$。

最终，在一家银行（具体是第二家）中，可以获得 $25$ 卢布，这个值就是该测试用例的答案。

在第三个测试用例中，可以从第一家银行向第二家银行转账 $1000$ 次一卢布，并在第二家银行获得 $2000$ 卢布。



```cpp
// Problem: CF 2194 B
// Contest: Codeforces - Codeforces Round 1078 (Div. 2)
// URL: https://codeforces.com/contest/2194/problem/B
// Memory Limit: 256 MB
// Time Limit: 1500 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long

void solve(){
	int n,x,y;
	cin>>n>>x>>y;
	vector<int> a(n);
	int sum=0,re=0;
	for(int i=0;i<n;i++){
		cin>>a[i];
		sum+=a[i];
	}
	if(x==y){
		cout<<sum<<endl;
		return ;
	}
	
	sort(a.begin(),a.end());
	for(int i=0;i<n-1;i++){
		re+=a[i]%x;
		re+=(a[i]/x*(x-y));
	}
	
	cout<<sum-re<<endl;
	return ;
}

signed main(){
	int t;
	cin>>t;
	while(t--){
		solve();
	}
	return 0;
}
```