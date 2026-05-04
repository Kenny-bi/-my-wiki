





## D. Fibonacci Paths
时间限制：每测试点2秒
内存限制：每测试点256 MB

## 问题描述

给定一个有向图，包含 $n$ 个顶点和 $m$ 条边。每个顶点 $v$ 对应一个正整数 $a_v$。计算不同的简单路径$^{*}$的数量，这些路径至少包含两个顶点，并且路径上顶点的数字构成的序列形成一个广义斐波那契数列。

在这个问题中，我们认为数字序列 $x_0, x_1, \dots, x_k$ 构成广义斐波那契数列，如果满足：

- $x_0, x_1$ 是任意自然数。
- $x_i = x_{i-2} + x_{i-1}$ 对所有 $2 \le i \le k$ 成立。

注意，一个广义斐波那契数列至少包含两个数字。

由于答案可能很大，请输出其对 $998244353$ 取模的结果。

$^{*}$ 在有向图中，一条简单路径是顶点序列 $v_1, v_2, \dots, v_k$，满足图中的每个顶点在路径中最多出现一次，并且对于所有 $i < k$，存在一条从 $v_i$ 到 $v_{i+1}$ 的有向边。

## 输入格式

每个测试包含多个测试用例。第一行包含测试用例的数量 $t$（$1 \le t \le 10^4$）。接下来是每个测试用例的描述。

每个测试用例的第一行包含两个数字 $n$, $m$（$2 \le n \le 2 \cdot 10^5$, $1 \le m \le 2 \cdot 10^5$）—— 分别是图中顶点的数量和边的数量。

每个测试用例的第二行包含 $n$ 个自然数 $a_1, a_2, \dots, a_n$（$1 \le a_i \le 10^{18}$）—— 写在顶点上的数字。

接下来的 $m$ 行包含图的边；每条边由两个自然数 $v, u$ 定义（$1 \le v, u \le n$, $u \ne v$），表示一条从 $v$ 指向 $u$ 的有向边。保证图中没有重边。

保证所有测试用例的 $n$ 之和以及 $m$ 之和均不超过 $2 \cdot 10^5$。

## 输出格式

对于每个测试用例，输出形成广义斐波那契数列的路径数量，结果对 $998244353$ 取模。

## 样例输入
```
4
4 4
3 4 3 6
1 2
1 3
2 4
3 4
4 6
1 1 1 2
1 2
2 3
3 1
1 4
2 4
3 4
8 11
2 4 2 6 8 10 18 26
1 2
2 3
3 1
4 3
2 4
3 5
5 6
4 6
6 7
7 5
5 8
2 2
10 10
1 2
2 1
```

## 样例输出
```
5
9
24
2
```

## 样例说明

第一个测试用例示例的解释（括号外是顶点编号，括号内是写在顶点的数字）：

```
1(3) --> 2(4)
  |         |
  v         v
3(3) --> 4(6)
```
![](https://espresso.codeforces.com/871040142263979c6a132efe66618c0297258b2c.png)


在这种情况下，有 $5$ 条广义斐波那契路径：$(1,2)$, $(1,3)$, $(2,4)$, $(3,4)$, $(1,3,4)$。例如，对于路径 $(1,3,4)$，该路径上顶点数字构成的序列是：$[3,3,6]$。可以很容易地看出，序列中的第三个数是前两个数的和。

第二个测试用例示例的解释：
![](https://espresso.codeforces.com/8aa6e1cae1f8174334c375789c1bcae8f74f4506.png)
```
1(1) --> 2(1)
  ^ \     | \
  |  \    v  v
  |   --> 4(2)
  |       ^
3(1) <---/
```

在这种情况下，有 $9$ 条广义斐波那契路径：$(1,2)$, $(1,4)$, $(2,3)$, $(2,4)$, $(3,1)$, $(3,4)$, $(1,2,4)$, $(2,3,4)$, $(3,1,4)$。注意，对于路径 $(1,2,3)$，该路径上顶点数字构成的序列是：$[1,1,1]$，它不是一个广义斐波那契数列。








# AI   TLE  代码
```cpp
// Problem: CF 2176 D
// Contest: Codeforces - Codeforces Round 1070 (Div. 2)
// URL: https://codeforces.com/contest/2176/problem/D
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include <iostream>
#include <vector>
#include <map>
using namespace std;
const int MOD = 998244353;
const int MAXN = 200005; // 根据题目 N, M <= 2e5

struct Edge {
    int u;  // 起点
    int v;  // 终点
    int id; // 边的唯一编号 (0 到 m-1)
};

long long a[MAXN];       // 存储每个点的数值 (注意：题目数值高达 10^18，必须用 long long)
vector<Edge> all_edges;  // 存储所有的边，方便通过 ID 访问
// 格式：lookup[当前点u][来源点的数值] = {边ID列表}
map<long long, vector<int>> lookup[MAXN]; 
long long memo[MAXN]; 

long long dfs(int edge_idx) {
    // 步骤 1: 记忆化检查
    // TODO: 如果 memo[edge_idx] 已经被计算过（不等于 -1），直接返回它
    if(memo[edge_idx]!=-1) return memo[edge_idx];
    // 步骤 2: 获取当前边的信息
    // 通过 all_edges[edge_idx] 拿到 u (起点) 和 v (终点)
    auto [u,v,id]=all_edges[edge_idx];
    // 步骤 3: 初始化当前答案
    // 这条边本身就是一条合法的长度为 2 的路径，所以初始值为 1
    long long ans = 1;

    // 步骤 4: 逆向推导
    // 当前是 u -> v。
    // 如果存在上一条边 p -> u，那么必须满足：a[p] + a[u] == a[v]
    // 所以我们需要找的“目标前任值” target = a[v] - a[u]
    long long target = a[v]-a[u];

    // 步骤 5: 查找并递归
    // 只有当 target >= 1 时才查找（因为题目保证点权是自然数）
    if (target >= 1) {
        // TODO: 
        // 1. 检查 lookup[u] 中是否有 key 为 target 的记录
        // 2. 如果有，遍历这个 list 中的每一个 pre_edge_id
        // 3. 递归调用：ans = (ans + dfs(pre_edge_id)) % MOD
        vector<int> pre=lookup[u][target];
        if(pre.size()){
        	for(auto e:pre){
        		ans=(ans+dfs(e))%MOD;
        	}
        }
    }

    // 步骤 6: 记录并返回
    return memo[edge_idx] = ans;
}

void solve() {
    int n, m;
    cin >> n >> m;
	for(int i=1;i<=n;i++){
		cin>>a[i];
		lookup[i].clear();
	}
    all_edges.clear(); // 清空上一轮的边
    for (int i = 0; i < m; i++) {
        int u, v;
        cin >> u >> v;
        all_edges.push_back({u, v, i});
		lookup[v][a[u]].push_back(i);
        // 初始化 memo
        memo[i] = -1;
    }

    long long total_ans = 0;
    // TODO: 遍历所有边 (0 到 m-1)，累加 dfs(i) 的结果，记得取模
	for(int i=0;i<m;i++){
		total_ans=(total_ans+dfs(i))%MOD;
	}    
    cout << total_ans << "\n";
}

int main() {
    // 加速输入输出
    ios::sync_with_stdio(false);
    cin.tie(0);

    int t;
    cin >> t;
    while (t--) {
        solve();
    }
    return 0;
}
```





# Starsilk
```cpp
#include <bits/stdc++.h>
using namespace std;
const long long mod=998244353;
long long a[200000];
map<long long,long long>dp[200000];
struct linex{
	int u;
	int v;
	long long w;
	friend bool operator<(linex a,linex b){
		return a.w<b.w;
	}
}lx[200000];
int main(){
	ios::sync_with_stdio(false),cin.tie(0);
	int T,n,m,i;
	long long ans,x;
	for(cin>>T;T>0;T--)
	{
		cin>>n>>m;
		for(i=0;i<n;i++)cin>>a[i];
		for(i=0;i<m;i++)
		{
			cin>>lx[i].u>>lx[i].v;
			lx[i].u--;
			lx[i].v--;
			lx[i].w=a[lx[i].u]+a[lx[i].v];
		}
		sort(lx,lx+m);
		ans=0;
		for(i=0;i<m;i++)
		{
			x=dp[lx[i].u][a[lx[i].v]];
			ans=(ans+x+1)%mod;
			dp[lx[i].v][a[lx[i].v]+a[lx[i].u]]=(dp[lx[i].v][a[lx[i].v]+a[lx[i].u]]+x+1)%mod;
		}
		for(i=0;i<n;i++)dp[i].clear();
		cout<<ans<<'\n';
	}
	return 0;
}
```







# 真的折磨了我半天，暂时不想看到你了，我写简单题找找自信去(抄AC 代码)
```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int mod=998244353;
const int N=200000;

struct node{
	int u,v,w;
	friend bool operator<(node a,node b){
		return a.w<b.w;
	}
};

map<int,int> f[N];
void solve(){
	int n,m;
	cin>>n>>m;
	vector<int> a(n);
	vector<node> lx(m);
	for(int i=0;i<n;i++) cin>>a[i];
	for(int i=0;i<m;i++){
		cin>>lx[i].u>>lx[i].v;
		lx[i].u--;
		lx[i].v--;
		lx[i].w=a[lx[i].u]+a[lx[i].v];
	}
	sort(lx.begin(),lx.end());
	
	int ans=0;
	for(int i=0;i<m;i++){
		int x=f[lx[i].u][a[lx[i].v]];
		ans=(ans+1+x)%mod;
		f[lx[i].v][a[lx[i].u]+a[lx[i].v]]=(f[lx[i].v][a[lx[i].u]+a[lx[i].v]]+x+1)%mod;
	}
	
	for(int i=0;i<n;i++) f[i].clear();
	cout<<ans<<endl;
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