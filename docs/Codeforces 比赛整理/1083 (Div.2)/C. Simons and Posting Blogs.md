



## C. Simons and Posting Blogs

时间限制：每个测试点2秒
内存限制：每个测试点256兆字节

人生如电视剧；随着情节的发展，我们被卷入其中。

— 舜，陷阱

有 $n$ 个博客。第 $i$ 个博客按数组 $a_i = [a_{i,1}, a_{i,2}, \dots, a_{i,l_i}]$ 的顺序提到了 $l_i$ 个用户。

您将发布所有 $n$ 个博客。让我们维护一个序列 $Q$ 来描述您最近提到的用户列表。您需要准确地执行以下操作 $n$ 次：

1.  选择一个未发布的博客 $i$ ($1 \le i \le n$)，然后发布它。
2.  这将导致按顺序对每个 $1 \le j \le l_i$ 进行以下操作：
    *   如果 $a_{i,j}$ 已存在于 $Q$ 中，则将 $a_{i,j}$ 移动到 $Q$ 的开头。
    *   否则，在 $Q$ 的开头插入 $a_{i,j}$。

在所有 $n$ 次操作之后，找出字典序最小的 $Q$。

*   数组 $x$ 按字典序小于数组 $y$ 当且仅当以下条件之一成立：
    *   $x$ 是 $y$ 的前缀，但是 $x \neq y$；或
    *   在 $x$ 和 $y$ 不同的第一个位置，数组 $x$ 的元素比 $y$ 中的相应元素小。

## 输入格式

每个测试包含多个测试用例。第一行包含测试用例的数量 $t$ ($1 \le t \le 1000$)。测试用例的描述如下。

每个测试用例的第一行包含一个整数 $n$ ($1 \le n \le 3000$) — 博客的数量。

然后是 $n$ 行，第 $i$ 行以整数 $l_i$ ($1 \le l_i \le 3000$) 开头，描述第 $i$ 个博客中提到的用户数量，后面跟着 $l_i$ 个整数 $a_{i,1}, a_{i,2}, \dots, a_{i,l_i}$ ($1 \le a_{i,j} \le 10^6$) — 第 $i$ 个博客中提到的用户列表。

保证所有测试用例的 $n$ 之和不超过 $3000$。

记 $\sum_{i=1}^{n} l_i$ 为 $L$。保证所有测试用例的 $L$ 之和不超过 $3000$。

## 输出格式

记 $m$ 为至少在一个博客中被提及的用户数量。对于每个测试用例，输出 $m$ 个整数 $Q_1, Q_2, \dots, Q_m$ — 字典序最小的 $Q$。

## 样例输入

```
5
3
5 1 2 3 4 6
3 2 5 1
4 1 9 2 3
2
2 1 6
1 6
1
3 6 1 1
5
4 2 3 3 4
5 1 2 4 3 1
2 4 1
3 3 3 1
5 4 3 2 2 2
5
4 2 3 1 4
5 2 5 5 6 5
5 3 4 7 5 5
8 3 6 4 3 1 1 5 4
2 1 1
```

## 样例输出

```
1 5 2 3 9 6 4
6 1
1 6
1 3 2 4
1 4 3 2 5 6 7
```

## 样例说明

在第一个测试用例中，可以按如下顺序发布博客：

1.  发布第一个博客，$Q$ 将变为 $[6,4,3,2,1]$。
2.  发布第三个博客，$Q$ 将变为 $[3,2,9,1,6,4]$。
3.  发布第二个博客，$Q$ 将变为 $[1,5,2,3,9,6,4]$。

还有另一种发布博客的方法：

1.  发布第三个博客，$Q$ 将变为 $[1,9,2,3]$。
2.  发布第一个博客，$Q$ 将变为 $[6,4,3,2,1,9]$。
3.  发布第二个博客，$Q$ 将变为 $[1,5,2,6,4,3,9]$。

我们可以看到 $[1,5,2,3,9,6,4]$ 字典序小于另一种方法得到的结果。如果我们不在最后发布第二个博客，数组的第一个元素不会是 $1$，因此 $[1,5,2,3,9,6,4]$ 是字典序最小的数组 $Q$。

在第二个测试用例中，可以按如下顺序发布博客：

1.  发布第二个博客，$Q$ 将变为 $[6,1]$。
2.  发布第一个博客，$Q$ 保持为 $[6,1]$。

在第三个测试用例中，必须发布唯一的博客，$Q$ 将变为 $[1,6]$。


# 样例中一个小样例没有过
- 本质是因为我把整个排序看成是静态的了，其实是动态的，这个题目数据范围支持暴力循环找
```cpp
// Problem: CF 2205 C
// Contest: Codeforces - Codeforces Round 1083 (Div. 2)
// URL: https://codeforces.com/contest/2205/problem/C
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;

void solve(){
	int n;
	cin>>n;
	vector< vector<int> > ans;
	
	for(int i=0;i<n;i++){
		int m;cin>>m;
		vector<int> a(m);
		for(int j=0;j<m;j++){
			cin>>a[j];
		}
		reverse(a.begin(),a.end());
		ans.push_back(a);
	}
	sort(ans.begin(),ans.end());
	
	vector<int> res;
	map<int,int> cnt;
	for(int i=0;i<ans.size();i++){
		for(auto x:ans[i]){
			if(cnt[x]) continue;
			cnt[x]=1;
			res.push_back(x);
		}
	}
	
	for(int i=0;i<res.size();i++){
		cout<<res[i]<<" ";
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




# AC  代码
```cpp
// Problem: CF 2205 C
// Contest: Codeforces - Codeforces Round 1083 (Div. 2)
// URL: https://codeforces.com/contest/2205/problem/C
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;

void solve(){
	int n;
	cin>>n;
	vector< vector<int> > ans;
	
	for(int i=0;i<n;i++){
		int m;cin>>m;
		vector<int> a(m);
		for(int j=0;j<m;j++){
			cin>>a[j];
		}
		reverse(a.begin(),a.end());
		vector<int> unic;
		set<int> s;
		for(int i=0;i<a.size();i++){
			if(s.count(a[i])) continue;
			unic.push_back(a[i]);
			s.insert(a[i]);
		}
		ans.push_back(unic);
	}
	
	vector<int> res;
	map<int,int> vis;
	vector<int> stblog(n+3,0);
	for(int c=0;c<n;c++){
		int bestid=-1;
		vector<int> bestsq;
		bool first=true;
		
		for(int i=0;i<n;i++){
			if(stblog[i]) continue;
			
			vector<int> cursq;
			for(int x:ans[i]){
				if(!vis[x]) cursq.push_back(x);
			}
			
			if(first||cursq<bestsq){
				first=false;
				bestid=i;
				bestsq=cursq;
			}
		}
		
		stblog[bestid]=1;
		for(int x:bestsq){
			res.push_back(x);
			vis[x]=1;
		}
	}
	
	
	for(int i=0;i<res.size();i++){
		cout<<res[i]<<" ";
	}
	cout<<endl;
	return ;
}


int main(){
	ios::sync_with_stdio(false);
    cin.tie(nullptr);
	int t;
	cin>>t;
	while(t--){
		solve();
	}
	return 0;
}
```