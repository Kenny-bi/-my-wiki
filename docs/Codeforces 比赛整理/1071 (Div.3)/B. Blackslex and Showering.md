


## B. Blackslex and Showering
时间限制：每测试点 2 秒  
内存限制：每测试点 256 兆字节  

## 问题描述
在他的 IMO 奖牌得主朋友“为了这周不用再洗澡”而洗了两个小时澡之后，Blackslex 要上课迟到了！

为了去上课，Blackslex 必须按照特定顺序乘坐拥挤的电梯前往许多楼层。因为他是一名黑客，他可以在其他人不知情的情况下**跳过访问至多一个楼层**。他所花费的时间是**连续楼层号之间绝对差值的总和**。在允许跳过至多一个楼层的情况下，求所需的最小时间。

更正式地说，给定一个包含 $n$ 个整数的数组 $a=[a_1, a_2, \dots, a_n]$，你可以选择至多一个索引 $k \in \{1, 2, \dots, n\}$ 来删除元素 $a_k$，使得和式
$$ \sum_{i=1}^{n-2} |b_i - b_{i+1}| $$
最小化，其中 $b=[a_1, \dots, a_{k-1}, a_{k+1}, \dots, a_n]$ 是删除元素 $a_k$ 后的数组。请输出这个最小和。

## 输入格式
第一行包含一个整数 $t$ $(1 \le t \le 10^4)$ —— 测试用例的数量。

每个测试用例的第一行包含一个整数 $n$ $(3 \le n \le 2 \cdot 10^5)$ —— 数组的大小。

第二行包含 $n$ 个整数 $a_1, a_2, \dots, a_n$ $(1 \le a_i \le 100)$。

保证所有测试用例的 $n$ 之和不超过 $2 \cdot 10^5$。

## 输出格式
对于每个测试用例，输出一个实数 —— 所需的最小时间。

## 样例输入
```
3
5
4 15 1 7 9
3
2 4 8
6
11 13 17 19 23 29
```
## 样例输出
```
11
2
12
```
## 样例说明
对于第一个测试用例，从数组 $[4, 15, 1, 7, 19]$ 中删除的最佳索引是 $k=2$。数组变为 $[4, 1, 7, 19]$，所需时间为 $|4-1| + |1-7| + |7-19| = 3 + 6 + 12 = 21$？等一下，这个计算似乎有问题。让我们重新检查一下：

删除 $a_2=15$ 后，数组变为 $[4, 1, 7, 9]$（注意原数组最后一个是 9，不是 19）。那么和为 $|4-1| + |1-7| + |7-9| = 3 + 6 + 2 = 11$，与输出 11 一致。

对于第二个测试用例，删除的最佳索引是 $k=3$。




# AC  代码
```cpp
// Problem: CF 2179 B
// Contest: Codeforces - Codeforces Round 1071 (Div. 3)
// URL: https://codeforces.com/contest/2179/problem/B
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define ios ios::sync_with_stdio(false);cin.tie(nullptr);


void solve(){
	int n;
	cin>>n;
	vector<int> a(n);
	for(int i=0;i<n;i++){
		cin>>a[i];
	}
	int tot=0;
	for(int i=1;i<a.size();i++){
		tot+=abs(a[i]-a[i-1]);
	}
	int ans=1e9;
	
	for(int i=0;i<n;i++){
		int cur=tot;
		if(i==0){
			cur-=abs(a[1]-a[0]);
		}else if(i==n-1){
			cur-=abs(a[n-1]-a[n-2]);
		}else{
			cur-=abs(a[i]-a[i-1]);
			cur-=abs(a[i]-a[i+1]);
			cur+=abs(a[i-1]-a[i+1]);
		}
		ans=min(ans,cur);
	}
	cout<<ans<<endl;
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