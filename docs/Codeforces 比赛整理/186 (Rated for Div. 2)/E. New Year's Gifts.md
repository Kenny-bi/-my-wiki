




## CF 2182 E. New Year's Gifts

**时间限制：** 2 秒  
**内存限制：** 512 MB  

## 问题描述
Monocarp 有 $n$ 个朋友，并决定送给每个朋友一份新年礼物。他还准备了 $m$ 个盒子来放置礼物；第 $i$ 个盒子的美观度为 $a_i$。每个盒子最多只能装一份礼物。

Monocarp 想送给第 $i$ 个朋友至少价值 $y_i$ 个硬币的礼物。此外，他还知道，如果**至少满足以下条件之一**，那么第 $i$ 个朋友就会感到高兴：

1. 礼物被放置在一个美观度**至少**为 $x_i$ 的盒子中；
2. 礼物本身的价值**至少**为 $z_i$（这里 $z_i > y_i$）。

你的任务是帮助 Monocarp 计算，如果他拥有 $k$ 个硬币，他最多能让多少个朋友开心。注意，Monocarp **必须**为每个朋友购买一份礼物，并且礼物**不一定**要装在盒子里。

## 输入格式
第一行包含一个整数 $t$（$1 \le t \le 10^4$）—— 测试用例的数量。

每个测试用例的第一行包含三个整数 $n$、$m$ 和 $k$（$1 \le n, m \le 2 \cdot 10^5$；$1 \le k \le 10^{15}$）。

第二行包含 $m$ 个整数 $a_1, a_2, \ldots, a_m$（$1 \le a_i \le m$）。

接下来 $n$ 行，每行包含三个整数 $x_i$、$y_i$ 和 $z_i$（$1 \le x_i \le m$；$1 \le y_i < z_i \le 10^9$）。

输入数据的附加约束：
- $\sum_{i=1}^{n} y_i \le k$。
- 所有测试用例的 $n$ 之和不超过 $2 \cdot 10^5$。
- 所有测试用例的 $m$ 之和不超过 $2 \cdot 10^5$。

## 输出格式
对于每个测试用例，输出一个整数——在拥有 $k$ 个硬币的情况下，Monocarp 最多能让多少个朋友开心。

## 样例输入
```
3
2 1 6
1
1 2 3
1 2 7
2 2 3
1 1
2 1 3
2 1 5
3 4 11
1 2 2 1
3 2 5
4 4 6
3 1 3
```

## 样例输出
```
2
0
2
```

## 样例说明
在第一个样例中，Monocarp 可以让两个朋友都开心，操作如下：送给第一个朋友一份价值 $3$ 个硬币的礼物，送给第二个朋友一份价值 $2$ 个硬币、放在美观度为 $1$ 的盒子里的礼物。

在第二个样例中，Monocarp 不能让任何一个朋友开心，因为他没有足够的钱为任何一个朋友购买价值 $z_i$ 个硬币的礼物；同时，所有盒子的美观度都低于任何一个 $x_i$。

在第三个样例中，Monocarp 可以让两个朋友（第 2 个和第 3 个）开心，操作如下：送给第一个朋友一份价值 $2$ 个硬币的礼物，送给第二个朋友一份价值 $6$ 个硬币的礼物，送给第三个朋友一份价值 $3$ 个硬币的礼物。



# WA  2代码
```cpp
// Problem: CF 2182 E
// Contest: Codeforces - Educational Codeforces Round 186 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2182/problem/E
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long 
typedef tuple<int,int,int> TII;

bool cmp(TII a,TII b){
	auto [x1,y1,z1]=a;
	auto [x2,y2,z2]=b;
	if(x1!=x2) return x1<x2;
	else{
		if(z1!=z2) return z1>z2;
	}
}

void solve(){
	int n,m,k;
	cin>>n>>m>>k;
	vector<int> x(n+2),y(n+2),z(n+2),a(m+2);
	vector<TII> b;
	priority_queue<int,vector<int>,greater<int> > q,li;
	for(int i=1;i<=m;i++) cin>>a[i],q.push(a[i]);
	for(int i=1;i<=n;i++){
		cin>>x[i]>>y[i]>>z[i];
		b.push_back({x[i],y[i],z[i]});
	}
	sort(b.begin(),b.end());
	
	int ans=0,idx;
	for(int i=0;i<n;i++){
		auto [xx,yy,zz]=b[i];
		int flag=1;
		while(q.size()){
			if(q.top()<xx) q.pop();
			else{
				ans++;
				flag=0;
				break;
			}
		}
		
		if(flag){
			idx=i;
			break;
		}
	}
	
	for(int i=idx;i<n;i++){
		auto [xx,yy,zz]=b[i];
		li.push(zz);
	}
	while(li.size()){
		if(k-li.top()>0){
			k-=li.top();
			li.pop();
			ans++;
		}else break;
	}
	
	
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







# 终于过了，真不容易说是
```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long 
typedef pair<int,int> PII;

bool cmp(PII a,PII b){
	return a.first>b.first;
}

void solve(){
	int n,m,k;
	cin>>n>>m>>k;
	vector<int> x(n),y(n),z(n),a(m);
	for(int i=0;i<m;i++) cin>>a[i];	
	sort(a.begin(),a.end());
	reverse(a.begin(),a.end());
		
	int sum=0;
	vector<PII> f;
	for(int i=0;i<n;i++){
		cin>>x[i]>>y[i]>>z[i];
		sum+=y[i];
		f.push_back({x[i],z[i]-y[i]});
	}
	
	if(sum>k){
		cout<<0<<endl;
		return ;
	}
	
	priority_queue<int,vector<int>,greater<int> > q;
	sort(f.begin(),f.end(),cmp);
	
	int ok=0,idx=0;
	vector<int> money;
	for(auto [x,c]:f){
		while(idx<m&&a[idx]>=x){
			idx++;
			ok++;
		}
		q.push(c);
		if(q.size()>ok){
			money.push_back(q.top());
			q.pop();
		}
	}
	
	int ans=q.size(),b=k-sum;
	sort(money.begin(),money.end());
	for(auto c:money){
		if(b-c>=0){
			b-=c;
			ans++;
		}else break;
	}
	
	cout<<ans<<endl;
	return  ;
}
 

signed main(){
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







# 算法笔记：CF 2182 E. New Year's Gifts —— 反悔贪心与资源分配

## 1. 问题本质转化

首先，我们需要剥离题目的“故事外壳”，将其转化为纯粹的数学/资源模型。

- **基础消耗**：无论朋友是否开心，必须送礼物，花费 $y_i$。
    
    - **预处理**：令 $K' = K - \sum y_i$。如果 $K' < 0$，直接输出 0。
        
- **开心的方式（二选一）**：
    
    1. **用盒子（Option Box）**：需要盒子美观度 $a_j \ge x_i$。**额外花费：0**。
        
    2. **用钱（Option Money）**：将礼物价值提升到 $z_i$。**额外花费：$c_i = z_i - y_i$**。
        

**目标**：在预算 $K'$ 内，让尽可能多的人开心。

## 2. 核心难点：为什么普通贪心会错？

### ❌ 错误的贪心（你的 WA 思路）

- **策略**：按盒子需求 $x_i$ 从小到大排序，优先把盒子给需求小的人。
    
- **漏洞**：**忽略了“替代成本”**。
    
    - 例子：
        
        - A 需要 $x_A=1$ 的盒子，或者花 $1$ 元 ($c_A=1$)。
            
        - B 需要 $x_B=100$ 的盒子，或者花 $10000$ 元 ($c_B=10000$)。
            
        - 只有一个 $a=100$ 的盒子。
            
    - 按错误策略：盒子给 A（因为 $x_A$ 小）。结果 B 没盒子，必须花 10000 元，预算瞬间爆炸。
        
    - 最优策略：盒子给 B（省下 10000 元），花 1 元解决 A。
        

### ✅ 正确的贪心（反悔贪心）

- **策略**：优先用盒子满足所有人，当盒子不够分时，**踢掉那个“最便宜”的人**去用钱解决。
    
- **核心逻辑**：盒子是“免费”的资源，用盒子等同于省下了 $c_i$ 的钱。为了省最多的钱，盒子应该留给 $c_i$ 大的人。
    

---

## 3. 算法流程详解

### 步骤一：排序策略

为了线性地统计可用盒子，我们需要利用单调性：

- **朋友**：按盒子需求 $x_i$ **从大到小**排序。
    
- **盒子**：按美观度 $a_i$ **从大到小**排序。
    

为什么？

如果一个盒子能满足 $x=100$ 的朋友，那它一定能满足 $x=50$ 的朋友。从大到小遍历朋友，随着 $x_i$ 降低，可用的盒子数量（ok）是单调增加的。

### 步骤二：维护“反悔堆”

我们维护一个**小根堆（Min-Priority Queue）**，里面存放**当前分配到盒子的人的额外花费 $c_i$**。

遍历每个朋友（$x_i$ 降序）：

1. **解锁盒子**：将所有 $a_j \ge x_i$ 的盒子加入可用计数 `ok`。
    
2. **尝试入队**：假设当前朋友用盒子满足，将他的花费 $c_i$ 放入堆。
    
3. **反悔（关键）**：
    
    - 检查：如果 `堆的大小`（想用盒子的人数） > `ok`（实际可用盒子数）。
        
    - 操作：**必须踢走一个人**。踢谁？踢堆顶元素（$c_i$ 最小的人）。
        
    - 意义：这个人用盒子“最不划算”，让他去用钱解决吧。被踢出的 $c_i$ 存入 `money` 列表。
        

### 步骤三：最后的资源结算

遍历结束后：

1. **堆里的人**：成功分到了盒子（免费开心）。
    
    - `ans = q.size()`。
        
2. **被踢出的人（`money` 列表）**：只能靠钱。
    
    - 将 `money` 列表从小到大排序。
        
    - 用剩余预算 $K'$ 依次购买，能买几个算几个。
        

---

## 4. 关键代码片段解析（AC版）

C++

```
// 1. 朋友按 x_i 降序排序，方便统计盒子
sort(f.begin(), f.end(), cmp); 
// 盒子也要降序，配合朋友的降序
sort(a.begin(), a.end()); reverse(a.begin(), a.end());

int ok = 0, idx = 0;
// 小根堆：存“用盒子的人的省钱金额”
priority_queue<int, vector<int>, greater<int>> q;
vector<int> money; // 被盒子淘汰，需要用钱的人

for(auto [req, cost] : f) {
    // 2. 动态增加可用盒子：随着 req 变小，更多盒子达标
    while(idx < m && a[idx] >= req) {
        idx++;
        ok++;
    }
    
    // 3. 先假设给当前人盒子
    q.push(cost);
    
    // 4. 盒子不够分了？反悔！
    if(q.size() > ok) {
        // 拿出 cost 最小的人（最不值得用盒子的人）
        money.push_back(q.top()); 
        q.pop(); 
    }
}

// 5. 统计答案
// 堆里剩下的就是确定用盒子的人
int ans = q.size(); 
// 剩下的人看钱够不够买
sort(money.begin(), money.end());
for(auto c : money) {
    if(b - c >= 0) {
        b -= c;
        ans++;
    } else break;
}
```

## 5. 易错点复盘（警钟长鸣）

1. **变量定义的陷阱**：
    
    - **错**：`int ans = ok;` （`ok` 只是盒子总数，不代表用盒子的人数，可能盒子比人多）。
        
    - **对**：`int ans = q.size();` （堆的大小才是真正匹配成功的人数）。
        
2. **预算判断**：
    
    - 一定要先判断 `sum > k`，如果连 $y_i$ 都付不起，直接 `return 0`。
        
3. **数据范围**：
    
    - $k$ 高达 $10^{15}$，必须全程使用 `long long` (`#define int long long`)。
        

## 6. 复杂度分析

- **时间复杂度**：
    
    - 排序朋友和盒子：$O(N \log N + M \log M)$。
        
    - 遍历朋友并操作堆：每个朋友最多进堆一次、出堆一次，堆操作 $O(\log N)$。总计 $O(N \log N)$。
        
    - 最后贪心买单：$O(N \log N)$。
        
    - 总计：**$O(N \log N)$**。对于 $2 \cdot 10^5$ 的数据量，轻松通过（限制 2秒）。
        
- **空间复杂度**：
    
    - 存储数组和堆：$O(N + M)$。
        

---

**总结**：这道题是 **Priority Queue** 妙用的教科书级例题。当你在面对“有限资源分配，且不同选择有不同代价”的问题时，如果顺推很难，不妨试试“先全都要，不够再扔最便宜的”这种反悔贪心策略。