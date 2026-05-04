



## C. Production of Snowmen
时间限制：每测试点 2 秒  
内存限制：每测试点 512 MB

## 问题描述
为了营造真正的节日气氛，圣诞老人的助手们开设了一家生产雪人的工厂！

每个雪人都由三个雪球组成——头部、躯干和腿部。要使雪人稳定，组成腿的雪球必须严格大于组成躯干的雪球；反过来，组成躯干的雪球也必须严格大于组成头的雪球。形式上，如果我们将头部、躯干和腿部的大小分别表示为 $a, b, c$，那么只有当且仅当 $a < b < c$ 时，雪人才是稳定的。

在雪人生产工厂，有三条传送带，每条传送带上都有 $n$ 个雪球。第一条传送带上的雪球大小为 $a_1, a_2, \dots, a_n$；第二条传送带上的雪球大小为 $b_1, b_2, \dots, b_n$；第三条传送带上的雪球大小为 $c_1, c_2, \dots, c_n$。这些传送带是循环的；换句话说，在编号为 $n$ 的元素之后，编号为 $1$ 的元素又接踵而至，我们可以认为编号为 $i$ 的球和编号为 $i+n$ 的球（以及编号为 $i+2n$、$i+3n$ 的球等等）是同一个球。

要生产雪人，需要指定三个参数 $i, j, k$（$1 \le i, j, k \le n$），表示从每个传送带上的哪个球开始生产。之后，雪人将按如下方式从球上组装起来：
*   第一个雪人的头部大小为 $a_i$，躯干大小为 $b_j$，腿部大小为 $c_k$；
*   第二个雪人的头部大小为 $a_{i+1}$，躯干大小为 $b_{j+1}$，腿部大小为 $c_{k+1}$；
*   以此类推；
*   第 $n$ 个（最后一个）雪人的头部大小为 $a_{i+n-1}$，躯干大小为 $b_{j+n-1}$，腿部大小为 $c_{k+n-1}$。

参数 $i, j, k$ 的选择必须保证所有 $n$ 个雪人都是稳定的。你的任务是计算合适的参数组合的数量。

## 输入格式
第一行包含一个整数 $t$（$1 \le t \le 5000$）——测试用例的数量。

每个测试用例包含四行：
*   第一行包含一个整数 $n$（$1 \le n \le 5000$）；
*   第二行包含 $n$ 个整数 $a_1, a_2, \dots, a_n$（$1 \le a_i \le 3n$）；
*   第三行包含 $n$ 个整数 $b_1, b_2, \dots, b_n$（$1 \le b_i \le 3n$）；
*   第四行包含 $n$ 个整数 $c_1, c_2, \dots, c_n$（$1 \le c_i \le 3n$）。

**输入数据额外保证：** 所有测试用例的 $n$ 的总和不超过 $5000$。

## 输出格式
对于每个测试用例，输出一个整数——使得所有 $n$ 个雪人都稳定的参数 $i, j, k$ 的组合数量。

## 样例输入
```
4
2
1 2
3 4
5 4
3
1 1 1
2 2 2
3 3 3
4
1 2 1 2
3 3 2 2
5 5 5 5
5
1 4 2 3 5
6 4 5 7 6
7 5 8 10 10
```

## 样例输出
```
4
27
0
10
```

## 样例说明
在第一个样例中，合适的参数 $i, j, k$ 组合如下：
*   $i=1, j=1, k=2$：那么雪人将是 $(1,3,4)$ 和 $(2,4,5)$；
*   $i=1, j=2, k=1$：那么雪人将是 $(1,4,5)$ 和 $(2,3,4)$；
*   $i=2, j=1, k=2$：那么雪人将是 $(2,3,4)$ 和 $(1,4,5)$；
*   $i=2, j=2, k=1$：那么雪人将是 $(2,4,5)$ 和 $(1,3,4)$。

在第二个样例中，所有参数组合都是合适的。

在第三个样例中，对于任何参数组合，都会产生一个头部大小为 $2$、躯干大小为 $2$ 的雪人，这是不稳定的。




```cpp
// Problem: CF 2182 C
// Contest: Codeforces - Educational Codeforces Round 186 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2182/problem/C
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long


int fun(vector<int>& a,vector<int>& b){
	int ans=0,n=(int)a.size();
	for(int m=0;m<n;m++){
		int flag=1;
		for(int i=0;i<n;i++){
			if(a[i]>=b[(i+m)%n]){
				flag=0;
				break;
			}
		}
		if(flag) ans++;
	}
	return ans;
}

void solve(){
	int n;
	cin>>n;
	vector<int> a(n),b(n),c(n);
	for(int i=0;i<n;i++) cin>>a[i];
	for(int i=0;i<n;i++) cin>>b[i];
	for(int i=0;i<n;i++) cin>>c[i];
	
	//cout<<fun(a,b)<<"  "<<fun(b,c)<<endl;
	cout<<(int)n*fun(a,b)*fun(b,c)<<endl;
	
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