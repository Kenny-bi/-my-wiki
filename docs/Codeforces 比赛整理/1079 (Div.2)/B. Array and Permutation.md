

# 我就说这个题绝对见过

![[Pasted image 20260211231523.png]]






## B. Array and Permutation

时间限制：每个测试点1.5秒  
内存限制：每个测试点256兆字节

## 问题描述

给定一个长度为 $n$∗ 的排列 $p$ 和一个长度为 $n$ 的数组 $a$。

如果可以通过执行若干次（可能为零次）以下类型的操作从排列 $p$ 得到数组 $a$，则称排列 $p$ 是数组 $a$ 的**生成排列**：

选择一个下标 $i$（$1 \le i < n$），并执行以下两种替换之一：
- $p_i := p_{i+1}$；
- $p_{i+1} := p_i$。

换句话说，在一次操作中，你可以选择数组中两个相邻的元素，并将其中一个元素的值复制到另一个元素中。

你需要判断排列 $p$ 是否为数组 $a$ 的生成排列。

∗ 长度为 $n$ 的排列是由 $n$ 个从 $1$ 到 $n$ 的不同整数按任意顺序组成的数组。例如，$[2,3,1,5,4]$ 是一个排列，但 $[1,2,2]$ 不是排列（$2$ 在数组中出现两次），$[1,3,4]$ 也不是排列（$n=3$ 但数组中含有 $4$）。

## 输入格式

每个测试包含多个测试用例。第一行包含一个整数 $t$，表示测试用例的数量（$1 \le t \le 10^4$）。  
接下来是每个测试用例的描述。

每个测试用例的第一行包含一个整数 $n$（$2 \le n \le 2 \times 10^5$）——数组和排列的长度。

第二行包含 $n$ 个整数 $p_1, p_2, \ldots, p_n$（$1 \le p_i \le n$）——排列 $p$ 的元素。

第三行包含 $n$ 个整数 $a_1, a_2, \ldots, a_n$（$1 \le a_i \le n$）——数组 $a$ 的元素。

保证所有测试用例中 $n$ 的总和不超过 $2 \times 10^5$。

## 输出格式

对于每个测试用例，如果排列 $p$ 是数组 $a$ 的生成排列，则输出 `"YES"`，否则输出 `"NO"`。

每个字母的大小写不限（小写或大写均可）。例如，字符串 `"yEs"`、`"yes"`、`"Yes"` 和 `"YES"` 都将被接受为肯定答案。

## 样例输入

```
6
3
1 2 3
1 2 2
4
3 1 2 4
3 4 2 2
5
1 3 2 5 4
3 3 3 5 4
7
3 7 4 2 1 6 5
3 3 4 4 5 6 5
7
1 2 3 4 5 6 7
7 7 7 7 7 7 7
7
1 3 2 7 5 4 6
2 2 7 7 7 5 6
```

## 样例输出

```
YES
NO
YES
NO
YES
YES
```

## 样例说明

在第一个测试用例中，通过一次操作即可从排列生成数组：

$i = 2$，执行 $p_{i+1} := p_i$。

在第二个测试用例中，无法从 $p$ 得到 $a$。

在第三个测试用例中，需要执行两次操作：

$i = 1$，执行 $p_i := p_{i+1}$；  
$i = 2$，执行 $p_{i+1} := p_i$。





# AC 代码
```cpp
// Problem: CF 2197 B
// Contest: Codeforces - Codeforces Round 1079 (Div. 2)
// URL: https://codeforces.com/contest/2197/problem/B
// Memory Limit: 256 MB
// Time Limit: 1500 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;

void solve(){
	int n;
	cin>>n;
	vector<int> p(n),a(n),re(n+3);
	
	for(int i=0;i<n;i++){
		cin>>p[i];
		re[p[i]]=i;
	}
	
	vector<int> ans;
	for(int i=0;i<n;i++){
		cin>>a[i];
		ans.push_back(re[a[i]]);
		//cout<<re[a[i]]<<" ";
	}
	
	for(int i=1;i<n;i++){
		if(ans[i]<ans[i-1])
		{
			cout<<"NO"<<endl;
			return ;
		}
	}
	cout<<"YES"<<endl;
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