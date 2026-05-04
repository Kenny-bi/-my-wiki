

## C. Quotient and Remainder
时间限制：每测试点2秒  
内存限制：每测试点512MB  

## 问题描述
给定两个整数数组：$q_1, q_2, \ldots, q_n$ 和 $r_1, r_2, \ldots, r_n$，以及一个整数 $k$。

你可以执行以下操作任意次（包括零次）：

选择两个整数 $x$ 和 $y$，使得：
1. $1 \leq y < x \leq k$；
2. 存在索引 $i$ 满足 $q_i = \lfloor \frac{x}{y} \rfloor$（向下取整）；
3. 存在索引 $j$ 满足 $r_j = x \bmod y$。

从数组 $q$ 中删除 $q_i$，从数组 $r$ 中删除 $r_j$。如果数组 $q$ 中有多个 $q_i$，只删除一个；数组 $r$ 中的 $r_j$ 同理。

计算你可以对给定数组 $q$ 和 $r$ 执行的最大操作次数。

## 输入格式
第一行包含一个整数 $t$ ($1 \leq t \leq 10^4$) —— 测试用例数量。

每个测试用例的第一行包含两个整数 $n$ 和 $k$ ($1 \leq n \leq 2 \cdot 10^5$; $2 \leq k \leq 10^{18}$) —— 数组 $q$ 和 $r$ 的大小，以及 $x$ 和 $y$ 的上限。

每个测试用例的第二行包含 $n$ 个整数 $q_1, q_2, \ldots, q_n$ ($1 \leq q_i \leq 10^9$) —— 数组 $q$。

第三行包含 $n$ 个整数 $r_1, r_2, \ldots, r_n$ ($1 \leq r_i \leq 10^9$) —— 数组 $r$。

输入的其他约束：所有测试用例的 $n$ 之和不超过 $2 \cdot 10^5$。

## 输出格式
对于每个测试用例，输出一个整数 —— 可以对给定数组执行的最大操作次数。

## 样例输入
```
3
1 100
1
27
3 10
5 6 7
1 7 7
5 42
5 4 2 2 1
9 8 9 8 100
```

## 样例输出
```
1
0
3
```

## 样例说明
在第一个测试用例中，可以执行一次操作：选择 $x=69$ 和 $y=42$。则 $\lfloor \frac{69}{42} \rfloor = 1 = q_1$ 且 $69 \bmod 42 = 27 = r_1$。

在第二个测试用例中，无法执行任何操作，因为找不到合适的 $x$ 和 $y$。

在第三个测试用例中，可以执行三次操作：
- $x=42$, $y=17$: $\lfloor \frac{42}{17} \rfloor = 2 = q_3$ 且 $42 \bmod 17 = 8 = r_2$；
- $x=41$, $y=16$: $\lfloor \frac{41}{16} \rfloor = 2 = q_4$ 且 $41 \bmod 16 = 9 = r_1$；
- $x=20$, $y=12$: $\lfloor \frac{20}{12} \rfloor = 1 = q_5$ 且 $20 \bmod 12 = 8 = r_4$；

这些操作后，数组变为 $q=[5,4]$ 和 $r=[9,100]$，无法再执行更多操作。


# 赛时AC代码
```cpp
// Problem: CF 2170 C
// Contest: Codeforces - Educational Codeforces Round 185 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2170/problem/C
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long


void solve(){
	int n,k;
	cin>>n>>k;
	priority_queue<int> q;
	vector<int> r(n);
	for(int i=0;i<n;i++){
		int x;cin>>x;
		q.push(x);
	} 
	for(int i=0;i<n;i++) cin>>r[i];
	sort(r.begin(),r.end());
	int ans=0;
	for(int i=0;i<n;i++){
		while(q.size()){
			int t=q.top();
			q.pop();
			int left=k-r[i];
			int num=t*(r[i]+1);
			if(num<=left){
				ans++;
				break;
			}
		}
	}
	printf("%lld\n",ans);
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




# 题目整理：CF 2170 C - Quotient and Remainder

## 1. 你的代码思路分析 (Greedy + Heap)

你的解法结合了**数学推导**和**贪心策略**，逻辑非常清晰且正确。

### 核心数学模型

题目要求找到 $x, y$ 使得：

1. $\lfloor x/y \rfloor = q_i$
    
2. $x \bmod y = r_j$
    
3. $1 \le y < x \le k$
    

根据除法原理：$x = q_i \cdot y + r_j$。

要使 $x \le k$ 成立，我们需要：

$$q_i \cdot y + r_j \le k$$

因为题目要求 $x \bmod y = r_j$，根据余数的定义，必须满足 $y > r_j$。

为了让 $x$ 尽可能小（从而更容易 $\le k$），我们需要取最小的合法 $y$。

最小的合法 $y$ 是 $r_j + 1$。

代入公式，判断条件转化为：

$$q_i \cdot (r_j + 1) + r_j \le k$$

### 贪心逻辑分析

你的代码做了如下操作：

1. **排序 $r$**：从小到大处理余数。
    
2. **大顶堆 $q$**：每次取出最大的商。
    
3. **匹配策略**：
    
    - 拿当前**最小的余数** $r_{min}$，去尝试匹配**最大的商** $q_{max}$。
        
    - 如果 $q_{max}$ 连 $r_{min}$ 都无法满足（即算出来的 $x > k$），那么这个 $q_{max}$ 肯定也无法满足更大的 $r$，因为 $r$ 越大，$x$ 越大。所以直接**丢弃**这个 $q_{max}$。
        
    - 如果满足，则匹配成功，`ans++`。
        

为什么这样是优的？

这是一个经典的资源分配贪心问题：

- **资源**：$r$ (越小的 $r$ 越“宽容”，能容纳越大的 $q$)。
    
- **需求**：$q$ (越大的 $q$ 越“难搞”，需要的 $x$ 越大)。
    
- **策略**：把“最宽容”的资源（小 $r$）留给“最难搞”的需求（大 $q$）。如果我们用小 $r$ 去匹配了一个小 $q$，那么剩下的大 $q$ 面对大 $r$ 肯定匹配不上，造成浪费。
    

### 复杂度

- **时间复杂度**：$O(N \log N)$。主要是排序 $r$ 和优先队列的操作。
    
- **空间复杂度**：$O(N)$。
    

---

## 2. 进阶分析：从 Heap 优化到 双指针 (Two Pointers)

虽然你的优先队列做法完全可以通过（$N \log N$），但这道题其实可以用**双指针**实现，逻辑更直观，且常数更小。

### 优化思路

既然我们总是用“最小的 $r$” 去试探 “最大的 $q$”，那我们可以直接对两个数组都进行排序。

1. 将 $r$ 从小到大排序。
    
2. 将 $q$ 从小到大排序。
    
3. 使用双指针：
    
    - $i$ 指向 $r$ 的开头（最小）。
        
    - $j$ 指向 $q$ 的末尾（最大）。
        

### 优化代码示例

C++

```
void solve() {
    int n; 
    long long k;
    cin >> n >> k;
    vector<long long> q(n), r(n);
    for(int i=0; i<n; i++) cin >> q[i];
    for(int i=0; i<n; i++) cin >> r[i];

    sort(q.begin(), q.end()); // q 从小到大
    sort(r.begin(), r.end()); // r 从小到大

    int ans = 0;
    int ptr_q = n - 1; // q 的指针从最大开始
    
    // 遍历每一个 r (从小到大)
    for(int i = 0; i < n; i++) {
        while(ptr_q >= 0) {
            long long current_q = q[ptr_q];
            long long val = current_q * (r[i] + 1) + r[i];
            
            if(val <= k) {
                // 匹配成功！
                ans++;
                ptr_q--; // 这个 q 用过了，移到下一个较大的 q
                break;   // 当前 r 搞定了，去下一个 r
            } else {
                // 匹配失败，current_q 太大了，连最小的 r[i] 都满足不了
                // 以后更大的 r 更不可能满足它，直接丢弃
                ptr_q--; 
            }
        }
    }
    cout << ans << "\n";
}
```

**区别**：

- 你的代码：$r$ 循环里套着 `while(pq)`。
    
- 优化代码：$r$ 循环里套着 `while(ptr)`。
    
- 本质是一样的，但数组排序比维护堆稍微快一点点（常数优势），且代码不用写 PQ。
    

---

## 3. 知识点总结 (Review)

这道题的关键在于把复杂的文字描述转化为不等式。

### 📌 技巧：数学公式化简 (Formalization)

遇到 $x, y$ 的构造题，先列出公式：

$$x = qy + r$$

然后找极值。要判定“是否存在”，往往意味着找“最小/最大”情况。

- 这里 $y > r$，为了让 $x$ 不超标，取 $y_{min} = r+1$ 是关键的一步。
    

### 📌 技巧：贪心匹配模型 (Greedy Matching)

当你手里有两把牌（数组 A 和数组 B），规则是 $A_i$ 和 $B_j$ 结合必须满足某个阈值。

- 如果条件是 $A \times B \le K$（乘积受限）：
    
    - 通常策略是：**最小配最大**。
        
    - 让最小的 $A$ 去“拯救”最大的 $B$。如果最小的 $A$ 都救不了最大的 $B$，那谁也救不了它，只能放弃这个 $B$。
        

### 📌 建议加入 Obsidian 的笔记

你可以把这个模型记为 **"Inequality Greedy Matching"** (不等式贪心匹配)。

> 模型：给定数组 $A, B$ 和常数 $K$，求最大配对数，使得 $A_i \cdot B_j \le K$。
> 
> 策略：
> 
> 1. Sort $A$ ascending, Sort $B$ ascending.
>     
> 2. Iterate $A$ (small to large).
>     
> 3. Try to match with largest available $B$.
>     
> 4. If $A_{min} \cdot B_{max} > K$, then $B_{max}$ is impossible to match with anyone. Discard $B_{max}$.
>     
> 5. Else, Match them.
>     

### 下一步

至此，Codeforces Round 185 的 A, B, C 三题你都完美解决了（暴力、二分/数学、贪心/堆）。你的代码风格很扎实。

- **A题**：学会了 Padding 技巧。
    
- **B题**：学会了“总量-个数”的 $O(1)$ 判定。
    
- **C题**：学会了不等式转化的贪心匹配。
    

是否需要我为你创建一个 Obsidian 格式的 **"Competitive Programming - Review 2025-12-01"** 笔记，把这三题的精华汇总在一起？