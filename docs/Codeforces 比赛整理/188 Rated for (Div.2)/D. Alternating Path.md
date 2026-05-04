

## D. Alternating Path

时间限制：每个测试2秒
内存限制：每个测试512 MB

## 问题描述

给定一个具有 n 个顶点和 m 条边的无向图。顶点编号从 1 到 n。图中不包含自环或多重边。

你的任务是通过为每条边选择一个方向来生成一个有向图。在给边定向之后，如果一个顶点序列 v1,v2,…,vk（其中 k 可以任意大，且任何顶点可以重复任意多次）满足以下条件，则称之为**交替路径**：

- 边 (v1,v2) 的方向是从 v1 指向 v2；
- 边 (v2,v3) 的方向是从 v3 指向 v2；
- 边 (v3,v4) 的方向是从 v3 指向 v4；
- 边 (v4,v5) 的方向是从 v5 指向 v4；
- 以此类推。

如果原始图中从顶点 v 出发的**所有**路径（不一定简单）在生成的有向图中都是交替的，则称顶点 v 是**美丽的**。

在为所有边定向后，最多可以使多少个顶点成为美丽的？

## 输入格式

每个测试包含多个测试用例。第一行包含测试用例的数量 t (1≤t≤104)。接下来是测试用例的描述。

每个测试用例的第一行包含两个整数 n 和 m (1≤n≤2⋅105; 0≤m≤2⋅105) —— 分别表示图中的顶点数和边数。

接下来的 m 行，每行包含两个整数 v 和 u (1≤v,u≤n) —— 描述图中的一条边。

输入的附加约束：

- 给定的图不包含自环或多重边；
- 所有测试用例中 n 的总和不超过 2⋅105；
- 所有测试用例中 m 的总和不超过 2⋅105。

## 输出格式

对于每个测试用例，输出一个整数 —— 在为边定向后，最多可以成为美丽顶点的数量。

## 样例输入

```
4
8 9
1 3
1 4
2 3
2 4
5 6
6 7
7 8
8 5
6 8
4 0
6 2
1 5
2 3
1 0
```

## 样例输出

```
2
4
4
1
```





# 有一个奇怪的错误(新建一个测试点答案就是对的，但是一起测就不对，怀疑有什么没有更新，被影响了)
```cpp
// Problem: CF 2204 D
// Contest: Codeforces - Educational Codeforces Round 188 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2204/problem/D
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
const int N=2e5+7;

vector<int> adj[N],st(N),color(N);

bool dfs(int u,int c){
	color[u]=c;
	st[u]=1;
	for(auto v:adj[u]){
		if(!color[v]){
			if(!dfs(v,3-c)) return false;
		}else if(color[v]==c) return false;
	}
	return true;
}

int bfs(int u,int &num){
	map<int,int> vis;
	queue<int> q;
	q.push(u);
	int cnt=0;
	num=0;
	while(q.size()){
		int t=q.front();
		q.pop();
		if(vis[t]) continue;
		vis[t]=1;
		
		num++;
		if(color[t]==1) cnt++;
		for(auto v:adj[u]){
			q.push(v);
		}
	}
	
	return cnt;
}

void solve(){
	int n,m;
	cin>>n>>m;
	for(int i=0;i<=n;i++){
		adj[i].clear();
		st[i]=0;
	}
	for(int i=0;i<m;i++){
		int u,v;
		cin>>u>>v;
		adj[u].push_back(v);
		adj[v].push_back(u);
	}
	
	int ans=0;
	for(int i=1;i<=n;i++){
		if(!st[i]){
			if(dfs(i,1)){
				int num=0;
				int cnt=bfs(i,num);
				ans+=max(cnt,num-cnt);
			}
		}
	}
	
	cout<<ans<<endl;
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


# AI  分析
这是一个非常典型的**“多组测试数据综合症”**！

在算法竞赛中，如果你的代码**“单测全对，连测就错”**，99.9% 的原因都是：**全局变量/数组在两组数据之间没有被彻底清空！**

我帮你看了一下代码，果然抓到了两个大 Bug，其中第一个就是导致你连测失败的罪魁祸首，而第二个则是你 `bfs` 函数里的一个致命手误。

### 致命 Bug 1：忘记清空 `color` 数组

看你 `solve()` 里的初始化代码：

C++

```
for(int i=0;i<=n;i++){
    adj[i].clear();
    st[i]=0;
    // 这里漏掉了什么？
}
```

你清空了图 `adj` 和访问标记 `st`，但是**你完全忘记清空 `color` 数组了！**

这就导致到了第二组测试数据时，某些点上面还残留着上一组数据的颜色（非 0 值）。当 `dfs` 走到这些点时，`if(!color[v])` 判断就会失效，直接导致奇环判定错误或 DFS 遍历中断。

**修复方法**：在初始化循环里加上 `color[i] = 0;`。

---

### 致命 Bug 2：BFS 里的复制粘贴手误

来看你的 `bfs` 函数里扩展邻居的这行代码：

C++

```
while(q.size()){
    int t=q.front();
    q.pop();
    // ... 省略 ...
    
    // 💥 致命错误在这里！
    for(auto v:adj[u]){ 
        q.push(v);
    }
}
```

你当前出队的节点是 **`t`**，你应该去遍历 `t` 的邻居把它们加入队列。但是你写成了 `adj[u]`！`u` 是 BFS 一开始传进来的起点，这意味着你的 BFS 永远只在扩展起点的邻居，根本无法遍历整个连通块！

---

### 优化建议：双剑合璧（删掉多余的 BFS）

其实，你根本不需要写两个函数（一个 DFS 判二分图，一个 BFS 数颜色）。既然 `dfs` 已经把整个连通块遍历并且染好色了，直接在 `dfs` 的过程中顺手把颜色统计出来，不仅代码更短，运行速度还会快一倍！而且还能省掉 `map<int,int> vis` 这个非常耗时的操作。

这是帮你修好 Bug 并整合了逻辑的极致清爽版：

C++

```
#include<bits/stdc++.h>
using namespace std;
const int N=2e5+7;

vector<int> adj[N],st(N),color(N);
int cnt1 = 0, cnt2 = 0; // 用来统计当前连通块两种颜色的数量

bool dfs(int u, int c){
    color[u] = c;
    st[u] = 1;
    
    // 顺手统计颜色数量
    if(c == 1) cnt1++;
    else cnt2++;
    
    for(auto v : adj[u]){
        if(!color[v]){
            if(!dfs(v, 3 - c)) return false;
        }else if(color[v] == c) return false;
    }
    return true;
}

void solve(){
    int n, m;
    cin >> n >> m;
    
    // 彻底清空上一组测试数据的残留！
    for(int i = 0; i <= n; i++){
        adj[i].clear();
        st[i] = 0;
        color[i] = 0; // 必须清空 color！
    }
    
    for(int i = 0; i < m; i++){
        int u, v;
        cin >> u >> v;
        adj[u].push_back(v);
        adj[v].push_back(u);
    }
    
    int ans = 0;
    for(int i = 1; i <= n; i++){
        if(!st[i]){
            cnt1 = 0; // 每次遍历新连通块前，重置计数器
            cnt2 = 0;
            
            if(dfs(i, 1)){
                // 如果是二分图，直接取这两种颜色的最大值
                ans += max(cnt1, cnt2);
            }
        }
    }
    
    cout << ans << "\n"; // 多组数据千万别用 endl，用 \n 防超时
}

int main(){
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int t;
    if(cin >> t){
        while(t--){
            solve();
        }
    }
    return 0;
}
```

**修改复盘：**

1. 初始化彻底安全，不再被上一组数据“幽灵干涉”。
    
2. 去掉了错误且冗余的 `bfs` 函数，把统计颜色的任务 `cnt1++` 和 `cnt2++` 巧妙地融合进了 `dfs` 中。
    
3. 换用了 `\n` 和 `ios::sync_with_stdio`，防止在 $T=10^4$ 的情况下被卡 I/O 超时。
    

快拿去提交测一下吧！多组数据清空数组这个痛，几乎每个打算法竞赛的人都要痛上好几次才能刻骨铭心！




# 待测代码
```cpp
// Problem: CF 2204 D
// Contest: Codeforces - Educational Codeforces Round 188 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2204/problem/D
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
const int N=2e5+7;

vector<int> adj[N],st(N),color(N);

bool dfs(int u,int c){
	color[u]=c;
	st[u]=1;
	for(auto v:adj[u]){
		if(!color[v]){
			if(!dfs(v,3-c)) return false;
		}else if(color[v]==c) return false;
	}
	return true;
}

int bfs(int u,int &num){
	map<int,int> vis;
	queue<int> q;
	q.push(u);
	int cnt=0;
	num=0;
	while(q.size()){
		int t=q.front();
		q.pop();
		if(vis[t]) continue;
		vis[t]=1;
		
		num++;
		if(color[t]==1) cnt++;
		for(auto v:adj[t]){
			q.push(v);
		}
	}
	
	return cnt;
}

void solve(){
	int n,m;
	cin>>n>>m;
	for(int i=0;i<=n;i++){
		adj[i].clear();
		st[i]=0;
		color[i]=0;
	}
	for(int i=0;i<m;i++){
		int u,v;
		cin>>u>>v;
		adj[u].push_back(v);
		adj[v].push_back(u);
	}
	
	int ans=0;
	for(int i=1;i<=n;i++){
		if(!st[i]){
			if(dfs(i,1)){
				int num=0;
				int cnt=bfs(i,num);
				ans+=max(cnt,num-cnt);
			}
		}
	}
	
	cout<<ans<<endl;
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








# AC 代码
- 错误原因是因为耍小聪明，提前结束了dfs,导致没有遍历完
```cpp
// Problem: CF 2204 D
// Contest: Codeforces - Educational Codeforces Round 188 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2204/problem/D
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
const int N=2e5+7;
int flag;

vector<int> adj[N],st(N),color(N);

bool dfs(int u,int c){
	color[u]=c;
	st[u]=1;
	for(auto v:adj[u]){
		if(!color[v]){
			dfs(v,3-c);
		}else if(color[v]==c) flag=0;
	}
	return true;
}

int bfs(int u,int &num){
	map<int,int> vis;
	queue<int> q;
	q.push(u);
	int cnt=0;
	num=0;
	while(q.size()){
		int t=q.front();
		q.pop();
		if(vis[t]) continue;
		vis[t]=1;
		
		num++;
		if(color[t]==1) cnt++;
		for(auto v:adj[t]){
			q.push(v);
		}
	}
	
	return cnt;
}

void solve(){
	int n,m;
	cin>>n>>m;
	for(int i=0;i<=n;i++){
		adj[i].clear();
		st[i]=0;
		color[i]=0;
	}
	for(int i=0;i<m;i++){
		int u,v;
		cin>>u>>v;
		adj[u].push_back(v);
		adj[v].push_back(u);
	}
	
	int ans=0;
	for(int i=1;i<=n;i++){
		if(!st[i]){
			flag=1;
			dfs(i,1);
			if(flag){
				int num=0;
				int cnt=bfs(i,num);
				ans+=max(cnt,num-cnt);
			}
		}
	}
	
	cout<<ans<<endl;
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