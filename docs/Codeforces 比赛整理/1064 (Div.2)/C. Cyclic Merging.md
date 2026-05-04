


## C. Cyclic Merging
时间限制：每测试点 2 秒  
内存限制：256 MB

## 问题描述
给定 $n$ 个非负整数 $a_1, a_2, \dots, a_n$ 按环形排列。对于每个 $1 \leq i < n$，$a_i$ 和 $a_{i+1}$ 相邻；$a_1$ 和 $a_n$ 相邻。

你需要恰好执行 $n-1$ 次以下操作：

- 选择环上任意一对相邻元素，设它们的值为 $x$ 和 $y$，将它们合并为一个值为 $\max(x, y)$ 的元素，代价为 $\max(x, y)$。

注意，每次操作会使环的大小减少 $1$，并相应更新相邻关系。

请计算将环合并为一个元素的最小总代价。

## 输入格式
每个测试包含多个测试用例。第一行包含测试用例数量 $t$（$1 \leq t \leq 10^4$）。接下来是每个测试用例的描述。

每个测试用例的第一行包含一个整数 $n$（$2 \leq n \leq 2 \cdot 10^5$）。

接下来一行包含 $n$ 个整数 $a_1, a_2, \dots, a_n$（$0 \leq a_i \leq 10^9$）。

保证所有测试用例的 $n$ 之和不超过 $2 \cdot 10^5$。

## 输出格式
对于每个测试用例，输出一个整数 —— 最小总代价。

## 样例输入
```
3
4
1 1 3 2
2
0 2
7
1 1 4 5 1 4 1
```

## 样例输出
```
6
2
19
```

## 样例说明
第一个测试用例中，对数组 $[1,1,3,2]$ 可以按以下方式得到代价 $6$：

1. 合并下标 1 和 2，代价为 $1$，环变为 $[1,3,2]$。
2. 合并下标 1 和 3，代价为 $2$，环变为 $[3,2]$。
3. 合并下标 1 和 2，代价为 $3$，环变为 $[3]$。

总代价为 $1+2+3=6$。可以证明无法获得更小的代价，因此答案是 $6$。

第二个测试用例中，唯一的选择是合并两个元素，代价为 $2$。









```cpp
// Problem: CF 2166 C
// Contest: Codeforces - Codeforces Round 1064 (Div. 2)
// URL: https://codeforces.com/contest/2166/problem/C
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
typedef long long LL;
typedef pair<int,int> PII;



void solve(){
	int n;
	cin>>n;
	vector<int> a(n+3);
	vector<int> l(n+3),r(n+3);
	priority_queue<PII ,vector<PII> ,greater<PII> > q;
	for(int i=1;i<=n;i++) cin>>a[i];
	for(int i=1;i<=n;i++){
		l[i]=i-1;
		r[i]=i+1;
	}
	l[1]=n,r[n]=1;
	
	for(int i=2;i<n;i++){
		if(a[i-1]>a[i]) q.push({a[i],i});
		else q.push({a[i-1],i-1});
	}
	
	LL ans=0;
	while(q.size())
	{
		auto [v,x]=q.top();
		q.pop();
		ans+=v;
		l[r[x]]=l[x];
	    r[l[x]]=r[x];
		q.push({max(v,a[r[x]]),r[x]});
		q.push({max(v,a[l[x]]),l[x]});
	}
	printf("%lld\n",ans);
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




# 关于我第一版改错过程
## 第一次
这是一个非常好的开始！你已经正确地抓住了解决这个问题的两个核心工具：

1. **`l[]` 和 `r[]` 数组：** 用数组来模拟环形双向链表，这在 CP (Competitive Programming) 中是完美的，比使用指针或 `std::list` 更快、更简单。
    
2. **`priority_queue`：** 用小根堆来快速找到当前代价最小的合并操作。
    

你的代码里有几个关键点需要我们一起再仔细看看。

---

### 1. 堆的初始化

我们先看这个循环：

C++

```
for(int i=2;i<n;i++){
    if(a[i-1]>a[i]) q.push({a[i],i});
    else q.push({a[i-1],i-1});
}
```

在我们的贪心策略里，我们一开始需要考虑**所有**相邻的合并选项。

- 当有 $n$ 个元素时，环形上有多少个相邻的对？
    
- 你这个循环从 `i=2` 走到 `n-1`，它把哪些合并选项（`cost, index`）放进堆里了？它是不是漏掉了一些？
    

---

### 2. 堆的"惰性删除"

这是最棘手，也是最关键的部分。

C++

```
while(q.size())
{
    auto [v,x]=q.top();
    q.pop();
    ans+=v; // 关键问题在这里
    l[r[x]]=l[x];
    r[l[x]]=r[x];
    // ...
}
```

当你从堆顶取出 `[v, x]` 时，这代表一个“合并 `x` 和 `r[x]`，代价为 `v`”的_提议_。

但是，**这个提议可能已经“过时”了**。

比如我们有 `[...10, 1, 1, 10...]`。

- 堆里有一个 `(cost=1, index_of_first_1)` 的操作。
    
- 但如果我们先合并了 `(10, 1)`，那个 `(1, 1)` 的操作就不再有效了，因为它其中一个 `1` 已经被合并掉了。
    

**问题：** 当你从堆里 `pop()` 出 `[v, x]` 时，你怎么知道元素 `x` 和它_当时_的右邻居 `r[x]` 还是“活跃的”，并且没有在之前的步骤中被合并掉？

我们是不是需要一个额外的方式来追踪哪些元素“还在环上”？




## 我改良的版本
```cpp
// Problem: CF 2166 C
// Contest: Codeforces - Codeforces Round 1064 (Div. 2)
// URL: https://codeforces.com/contest/2166/problem/C
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
typedef long long LL;
typedef pair<int,int> PII;



void solve(){
	int n;
	cin>>n;
	vector<int> a(n+3);
	vector<int> l(n+3),r(n+3);
	priority_queue<PII ,vector<PII> ,greater<PII> > q;
	for(int i=1;i<=n;i++) cin>>a[i];
	for(int i=1;i<=n;i++){
		l[i]=i-1;
		r[i]=i+1;
	}
	l[1]=n,r[n]=1;
	
	for(int i=1;i<=n;i++){
		q.push({a[i],i});
	}
	
	vector<bool> st(n+3); 
	LL ans=0;
	while(q.size()>1)
	{
		auto [v,x]=q.top();
		q.pop();
		l[r[x]]=l[x];
	    r[l[x]]=r[x];
		if(st[x]) continue;
		st[x]=true;
		if(a[l[x]]>a[r[x]]) ans+=max(v,a[r[x]]);
		else ans+=max(v,a[l[x]]);
	}
	printf("%lld\n",ans);
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










# 他人AC 代码
```cpp
#include <bits/stdc++.h>
using namespace std;

using ll = long long;
using ull = unsigned long long;

void solve() {
    int n;
    cin >> n;

    vector<int> a(n);
    for (int& x : a) cin >> x;

    vector<int> l(n), r(n);
    for (int i = 0; i < n; ++i) {
        l[i] = (i + n - 1) % n;
        r[i] = (i + 1) % n;
    }

    priority_queue<pair<int, int>> q;
    for (int i = 0; i < n; ++i) {
        q.push({-a[i], i});
    }

    ll ans = 0;
    while (q.size() > 1) {
        auto [_, u] = q.top();
        q.pop();

        ans += min(a[l[u]], a[r[u]]);
        r[l[u]] = r[u];
        l[r[u]] = l[u];
    }

    cout << ans << '\n';
}

signed main() {

    cin.tie(0)->sync_with_stdio(false);

    int tt; cin >> tt;
    while (tt--) solve();

    return 0;
}
```