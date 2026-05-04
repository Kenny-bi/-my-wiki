---
created: 2025-11-23 15:30
tags:
  - algorithm/构造
  - difficulty/待定
  - status/AC
  - site/Codeforces
---



**题目链接**: [](<% tp.file.cursor(1) %>) 
**核心考点**: [[构造]]


## F. Rae Taylor and Trees (hard version)
时间限制：每测试点3秒  
内存限制：每测试点256 MB  

## 问题描述
"想想看，一个平民竟然会坐在我旁边。知道自己的位置！"  
— Claire François  

**这是问题的困难版本。简单版本和困难版本之间的唯一区别是，困难版本要求您构造一个满足条件的树的示例。**

作为一名地球法师，Rae已经掌握了生长树木的法术！但是Manaria夸口说她能种植一种更令人印象深刻的树种。Rae记得最稀有的树种可以使用由特定排列表示的公式来生长——请帮助她构造它！

您将得到一个长度为 $n$ 的排列$^*$ $p$。

确定是否存在一个具有 $n$ 个顶点（标记为 $1,2,\ldots,n$）的无向树，满足以下条件：

对于任意两个由边连接的顶点 $u$ 和 $v$ ($1 \leq u < v \leq n$)，$u$ 在 $p$ 中出现在 $v$ 之前。

此外，如果存在这样的树，输出其中任意一棵。

$^*$长度为 $n$ 的排列是一个数组，它包含从 $1$ 到 $n$ 的每个整数恰好一次，顺序任意。

## 输入格式
第一行包含一个整数 $t$ ($1 \leq t \leq 10^4$) — 测试用例的数量。

每个测试用例的第一行包含一个整数 $n$ ($2 \leq n \leq 2 \cdot 10^5$)。

每个测试用例的第二行包含 $n$ 个整数 $p_1, p_2, \ldots, p_n$ ($1 \leq p_i \leq n$)。保证所有的 $p_i$ 都是不同的。

保证所有测试用例的 $n$ 之和不超过 $2 \cdot 10^5$。

## 输出格式
对于每个测试用例，如果存在满足给定条件的树，输出一行"Yes"；否则输出"No"。

然后，如果答案是"Yes"，输出 $n-1$ 行。这些行中的第 $i$ 行应包含两个整数 $u$ 和 $v$，表示连接顶点 $u$ 和 $v$ 的边。

答案可以以任何大小写形式输出。例如，字符串"yEs"、"yes"、"YES"和"yeS"都会被识别为"Yes"。

## 样例输入
```
9
6
1 3 4 5 2 6
4
3 4 1 2
5
4 3 5 1 2
4
1 2 3 4
7
4 3 5 7 6 2 1
6
2 4 6 1 3 5
3
2 1 3
4
2 4 1 3
6
4 2 6 5 1 3
```

## 样例输出
```
Yes
3 1
4 1
6 5
6 2
6 1
No
No
Yes
2 1
4 3
4 1
No
Yes
4 2
6 2
3 1
5 1
5 2
Yes
3 2
3 1
Yes
4 2
3 1
3 2
Yes
6 4
6 2
3 1
5 4
2 3
```

## 样例说明
在第一个样例中，我们可以构造样例输出中给出的树。我们有：

- $1 < 3$，且 $1$ 在 $p$ 中出现在 $3$ 之前
- $1 < 4$，且 $1$ 在 $p$ 中出现在 $4$ 之前  
- $5 < 6$，且 $5$ 在 $p$ 中出现在 $6$ 之前
- $2 < 6$，且 $2$ 在 $p$ 中出现在 $6$ 之前
- $1 < 6$，且 $1$ 在 $p$ 中出现在 $6$ 之前

在第二个样例中，可以证明不存在满足给定约束的树。





# 别人的思路
- 维护两个小根堆，一个小根堆存前缀最小值，一个用来存后续连边的那个，
- 后续连边的有两种方式，要么前面有比他小的，要么就是后面又比他大的
- **连接完之后记得加入第一个小根堆为后续做准备**（第一次就死在这个地方）
```cpp
// Problem: CF 2171 F
// Contest: Codeforces - Codeforces Round 1065 (Div. 3)
// URL: https://codeforces.com/contest/2171/problem/F
// Memory Limit: 256 MB
// Time Limit: 3000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
typedef pair<int,int> PII;


void solve(){
	int n;
	cin>>n;
	vector<int> a(n);
	for(int i=0;i<n;i++) cin>>a[i];
	vector<PII> ans;
	priority_queue<int,vector<int>,greater<int> > lil,lli;
	
	for(int i=0;i<n;i++){
		if(lil.empty()) lil.push(a[i]);
		else{
			if(a[i]>lil.top()){
				ans.push_back({lil.top(),a[i]});
				lil.push(a[i]);
				while(lli.size()&&lli.top()<a[i]){
					ans.push_back({lli.top(),a[i]});
					lil.push(lli.top());
					lli.pop();
				}
			}else lli.push(a[i]);	
		}
	}
	
	if(lli.size()) printf("No\n");
	else{
		printf("Yes\n");
		for(int i=0;i<ans.size();i++){
			printf("%d %d\n",ans[i].first,ans[i].second);
		}
	}
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











# CF  题解思路代码
```cpp
// Problem: CF 2171 D
// Contest: Codeforces - Codeforces Round 1065 (Div. 3)
// URL: https://codeforces.com/contest/2171/problem/D
// Memory Limit: 256 MB
// Time Limit: 3000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
typedef long long LL;
typedef pair<int,int> PII;
const int N=1e6+7;
const int mod=1e9+7;



void solve(){
	int n;
	cin>>n;
	vector<int> a(n+1);
	for(int i=1;i<=n;i++){
		cin>>a[i];
	}
	vector<int> pre(n+2,1e9);
	vector<PII> suf(n+2,{0,0});
	for(int i=1;i<=n;i++){
		pre[i]=min(pre[i-1],a[i]);
	}
	for(int i=1;i<=n;i++) suf[i]={a[i],i};
	
	for(int i=n;i>0;i--){
		if(a[i]>suf[i+1].first){
			suf[i]={a[i],i};
		}else suf[i]=suf[i+1];
	}
	
	for(int i=2;i<=n;i++){
		if(pre[i-1]>suf[i].first){
			printf("No\n");
			return ;
		}
	}
	printf("Yes\n");
	for(int l=1;l<=n;){
		int r=suf[l].second;
		for(int i=l;i<r;i++){
			printf("%d %d\n",a[i],a[r]);
		}
		if(l>1) printf("%d %d\n",pre[l-1],a[r]);
		l=r+1;
	}
	
	
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






## 💡 核心直觉 (一句话总结)
> <% tp.file.cursor(2) %>

## 🚫 我的误区 (Fail Log)
* **思路错误**: 
* **实现错误**: 
* **数据范围**: 

## ✅ 正解思路


## 🧠 举一反三 (Pattern)
* 下次看到 **"..."** -> 想到 **...**

---
## 💻 代码
```cpp

````



