



## C. Odd Process
时间限制：每测试点2秒
内存限制：每测试点256 MB

## 问题描述

你有 $n$ 枚硬币，面额分别为 $a_1, a_2, \dots, a_n$，以及一个自然数 $k$。你还有一个初始为空的袋子，你可以将硬币放入其中。你需要恰好执行 $k$ 次操作。在每次操作中，你从剩余的硬币中取出一枚放入你的袋子。之后，你不能再取那枚硬币。

同时，你有一只喜欢偶数的猫，因此每当袋子中硬币面额之和变为偶数时，你的猫就会清空袋子——意味着它会把所有硬币带到一个只有它知道的地方，袋子再次变空。请注意，袋子会在每次添加硬币过程中和变为偶数时就被清空，而不仅仅是在最后时刻。

定义你的分数为袋子中硬币面额的总和。你的任务是执行 $k$ 次操作，以最大化你的最终分数。请找出所有 $1 \le k \le n$ 对应的答案。

## 输入格式

每个测试包含多个测试用例。第一行包含测试用例的数量 $t$（$1 \le t \le 10^4$）。接下来是每个测试用例的描述。

每个测试用例的第一行包含一个整数 $n$（$1 \le n \le 2 \cdot 10^5$）—— 你拥有的硬币数量。

每个测试用例的第二行包含 $n$ 个自然数 $a_1, a_2, \dots, a_n$（$1 \le a_i \le 10^9$）—— 硬币的面额。

保证所有测试用例的 $n$ 之和不超过 $2 \cdot 10^5$。

## 输出格式

对于每个测试用例，输出 $n$ 个数字——对于所有 $k$ 从 $1$ 到 $n$，恰好执行 $k$ 次操作所能获得的最大可能分数。

## 样例输入
```
6
3
1 1 1
3
1 2 3
5
4 1 3 1 2
5
4 2 3 1 3
3
4 1 2
3
4 2 2
```

## 样例输出
```
1 0 1 
3 5 0 
3 7 9 7 9 
3 7 9 7 9 
1 5 7 
0 0 0 
```

## 样例说明

在第一个测试用例中，你拥有的硬币面额为 $[1,1,1]$。

- $k=1$：在这种情况下，无论选择哪枚硬币，袋子中面额之和都是 $1$。最终分数也是 $1$。
- $k=2$：在这种情况下，无论第一枚选择哪枚硬币，面额之和都是 $1$。当选择第二枚硬币时，无论选择哪枚，和都会变为 $2$，因此袋子会被清空。最终分数是 $0$。
- $k=3$：在这种情况下，选择前两枚硬币时，和会变为 $2$，袋子会被清空，之后和为 $0$。当选择第三枚硬币时，和会变为 $1$。

在第二个测试用例中，你拥有的硬币面额为 $[1,2,3]$。

- $k=1$：在这种情况下，如果你选择面额为 $2$ 的硬币，袋子会被清空，最终分数为 $0$。如果你选择面额为 $1$ 或 $3$ 的硬币，最终分数分别为 $1$ 和 $3$。最大可能的最终分数是 $3$。
- $k=2$：在这种情况下，以任意顺序选择硬币 $1$ 和 $3$ 时，它们的和为 $4$，之后袋子会被清空，最终分数为 $0$。但是，如果你先选择硬币 $3$，分数会变为 $3$。然后你可以选择例如硬币 $2$，最终分数会变为 $5$。
- $k=3$：在这种情况下，无论以何种顺序选择硬币，最终和都会是 $6$，意味着袋子会被清空。最终分数为 $0$。
```cpp
// Problem: CF 2176 C
// Contest: Codeforces - Codeforces Round 1070 (Div. 2)
// URL: https://codeforces.com/contest/2176/problem/C
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;

bool cmp(int a,int b){ return a>b; }

void solve(){
	int n;
	cin>>n;
	vector<int> a(n);
	vector<int> o,e;
	for(int i=0;i<n;i++){
		cin>>a[i];
		if(a[i]&1) o.push_back(a[i]);
		else e.push_back(a[i]);
	}

	int ecnt=e.size(),ocnt=o.size();
	sort(o.begin(),o.end(),cmp);
	sort(e.begin(),e.end(),cmp);
	vector<int> pre(ecnt+3,0);
	for(int i=1;i<=ecnt;i++){
		pre[i]=e[i-1]+pre[i-1];
	}

	if((int)o.size()==0){
		for(int i=0;i<n;i++){
			printf("0 ");
		}
		printf("\n");
		return ;
	}
	
	if((int)e.size()==0){
		for(int i=1;i<=n;i++){
			if(i&1) printf("%d ",o[0]);
			else printf("0 ");
		}
		printf("\n");
		return ;
	}
	
	int need=1+ecnt;
	
	for(int i=1;i<=n;i++){
		if(i<=need){
			printf("%d ",o[0]+pre[i-1]);
		}else{
			int ans1=o[0]+pre[ecnt];
			int ans2=o[0]+pre[ecnt-1];
			
			if((need&1)==(i&1)) printf("%d ",ans1);
			else printf("%d ",ans2);
		}
	}
	printf("\n");
	
	return;
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

```cpp
// Problem: CF 2176 C
// Contest: Codeforces - Codeforces Round 1070 (Div. 2)
// URL: https://codeforces.com/contest/2176/problem/C
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long

bool cmp(int a,int b){ return a>b; }

void solve(){
	int n;
	cin>>n;
	vector<int> a(n);
	vector<int> o,e;
	for(int i=0;i<n;i++){
		cin>>a[i];
		if(a[i]&1) o.push_back(a[i]);
		else e.push_back(a[i]);
	}

	int ecnt=e.size(),ocnt=o.size();
	sort(o.begin(),o.end(),cmp);
	sort(e.begin(),e.end(),cmp);
	vector<int> pre(ecnt+3,0);
	for(int i=1;i<=ecnt;i++){
		pre[i]=e[i-1]+pre[i-1];
	}

	if((int)o.size()==0){
		for(int i=0;i<n;i++){
			printf("0 ");
		}
		printf("\n");
		return ;
	}
	
	if((int)e.size()==0){
		for(int i=1;i<=n;i++){
			if(i&1) printf("%lld ",o[0]);
			else printf("0 ");
		}
		printf("\n");
		return ;
	}
	
	int need=1+ecnt;
	
	for(int i=1;i<=n;i++){
		if(i<=need){
			printf("%lld ",o[0]+pre[i-1]);
		}else{
			int ans1=o[0]+pre[ecnt];
			int ans2=o[0]+pre[ecnt-1];
			if(i&1&&ocnt%2==0&&ecnt%2==1) printf("0");
			else if((need&1)==(i&1)) printf("%lld ",ans1);
			else printf("%lld ",ans2);
		}
	}
	printf("\n");
	
	return;
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




# AC  代码
```cpp
// Problem: CF 2176 C
// Contest: Codeforces - Codeforces Round 1070 (Div. 2)
// URL: https://codeforces.com/contest/2176/problem/C
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long

bool cmp(int a,int b){ return a>b; }

void solve(){
	int n;
	cin>>n;
	vector<int> a(n);
	vector<int> o,e;
	for(int i=0;i<n;i++){
		cin>>a[i];
		if(a[i]&1) o.push_back(a[i]);
		else e.push_back(a[i]);
	}

	int ecnt=e.size(),ocnt=o.size();
	sort(o.begin(),o.end(),cmp);
	sort(e.begin(),e.end(),cmp);
	vector<int> pre(ecnt+3,0);
	for(int i=1;i<=ecnt;i++){
		pre[i]=e[i-1]+pre[i-1];
	}

	if((int)o.size()==0){
		for(int i=0;i<n;i++){
			printf("0 ");
		}
		printf("\n");
		return ;
	}
	
	if((int)e.size()==0){
		for(int i=1;i<=n;i++){
			if(i&1) printf("%lld ",o[0]);
			else printf("0 ");
		}
		printf("\n");
		return ;
	}
	
	int need=1+ecnt;
	
	for(int i=1;i<=n;i++){
		int idx=ecnt;
		idx=min(idx,i-1);
		if((i-idx)%2==0) idx--;
		if(i-idx>ocnt) idx++;
		
		if((i-idx)%2==0) printf("0 ");
		else printf("%lld ",o[0]+pre[idx]);
	}
	printf("\n");
	
	return;
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