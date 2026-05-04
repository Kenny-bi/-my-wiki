

## A. Same Difference
时间限制：每测试点 1 秒  
内存限制：256 MB

## 问题描述
给定一个长度为 $n$ 的字符串 $s$，由小写字母组成。

在一次操作中，你可以选择一个整数 $i$（$1 \leq i < n$），并将 $s_i$ 改为 $s_{i+1}$。

最少需要多少次操作才能使所有字符相同？可以证明这总是可以实现的。

## 输入格式
每个测试包含多个测试用例。第一行包含测试用例数量 $t$（$1 \leq t \leq 20$）。接下来是每个测试用例的描述。

每个测试用例的第一行包含一个整数 $n$（$2 \leq n \leq 100$）—— 字符串的长度。

接下来一行包含一个长度为 $n$ 的字符串 $s$，由小写字母组成。

保证所有测试用例的 $n$ 之和不超过 $100$。

## 输出格式
对于每个测试用例，输出一个整数 —— 使所有字符相同所需的最少操作次数。

## 样例输入
```
5
3
qwq
2
aa
4
test
5
abbac
6
abcabc
```

## 样例输出
```
1
0
2
4
4
```

## 样例说明
- 第一个测试用例中，可以通过 1 次操作将 $s_2$ 改为 $s_3$ 达成目标。
- 第三个测试用例中，可以先将 $s_3$ 改为 $s_4$，再将 $s_2$ 改为 $s_3$，总共使用 2 次操作。可以证明答案不小于 2。



# 赛时AC代码
```cpp
// Problem: CF 2166 A
// Contest: Codeforces - Codeforces Round 1064 (Div. 2)
// URL: https://codeforces.com/contest/2166/problem/A
// Memory Limit: 256 MB
// Time Limit: 1000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

//39

#include<bits/stdc++.h>
using namespace std;
typedef long long LL;
typedef pair<int,int> PII;

void solve(){
	int n;
	cin>>n;
	string s;
	cin>>s;
	vector<int> cnt(300,0); 
	for(int i=0;i<n;i++){
		cnt[s[i]-'a']++;
	}
	printf("%d\n",n-cnt[s[n-1]-'a']);
	
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