---
created: 2025-12-18 09:56
tags:
  - algorithm/待分类
  - difficulty/待定
  - status/AC
  - site/Codeforces
---



**题目链接**: [](<% tp.file.cursor(1) %>) 
**核心考点**: [[]]




# CF1873F Money Trees

## 题目描述

Luca 站在一排共有 $n$ 棵树前。第 $i$ 棵树上有 $a_i$ 个果实，高度为 $h_i$。

他想选择一个连续子数组 $[h_l, h_{l+1}, \dots, h_r]$，使得对于每个 $i$（$l \leq i < r$），$h_i$ 能被 $h_{i+1}$ 整除。然后他会收集该子数组中所有树上的果实（即收集 $a_l + a_{l+1} + \dots + a_r$ 个果实）。但是，如果他收集的果实总数超过 $k$，他就会被抓住。

Luca 能选择的最长连续子数组长度是多少，且不会被抓住？

$^\dagger$ 若 $x$ 能被 $y$ 整除，则 $\frac{x}{y}$ 是整数。

## 输入格式

第一行包含一个整数 $t$（$1 \leq t \leq 1000$），表示测试用例的数量。

每个测试用例的第一行包含两个用空格分隔的整数 $n$ 和 $k$（$1 \leq n \leq 2 \cdot 10^5$；$1 \leq k \leq 10^9$），分别表示树的数量和 Luca 能收集而不被抓住的最大果实数。

每个测试用例的第二行包含 $n$ 个用空格分隔的整数 $a_i$（$1 \leq a_i \leq 10^4$），表示第 $i$ 棵树上的果实数。

每个测试用例的第三行包含 $n$ 个用空格分隔的整数 $h_i$（$1 \leq h_i \leq 10^9$），表示第 $i$ 棵树的高度。

所有测试用例中 $n$ 的总和不超过 $2 \cdot 10^5$。

## 输出格式

对于每个测试用例，输出一个整数，表示满足条件的最长连续子数组的长度。如果不存在这样的子数组，输出 $0$。

## 输入输出样例 #1

### 输入 #1

```
5
5 12
3 2 4 1 8
4 4 2 4 1
4 8
5 4 1 2
6 2 3 1
3 12
7 9 10
2 2 4
1 10
11
1
7 10
2 6 3 1 5 10 6
72 24 24 12 4 4 2
```

### 输出 #1

```
3
2
1
0
3
```

## 说明/提示

在第一个测试用例中，Luca 可以选择 $l=1$ 且 $r=3$ 的子数组。

在第二个测试用例中，Luca 可以选择 $l=3$ 且 $r=4$ 的子数组。

在第三个测试用例中，Luca 可以选择 $l=2$ 且 $r=2$ 的子数组。

由 ChatGPT 4.1 翻译









## 💡 核心直觉 (一句话总结)

> 题目有两个限制：高度整除（决定了区间的连通性）和 果实总和（决定了区间的长度）。
> 
> 就像切香肠一样，先根据高度条件把数组切成若干个独立的连续块，然后在每个块内找和不超过 $K$ 的最长子段（通常用双指针/滑动窗口）。

---

## 🚫 易错点 & 误区 (Fail Log)

1. **STL区间左闭右开** (你的代码中唯一的逻辑Bug):
    
    - `upper_bound` 的查找范围是 `[first, last)`。
        
    - 你的代码写的是 `pre.begin()+rr`，这意味着第 `rr` 个元素并没有被包含在查找范围内。
        
    - **修正**: 应该是 `pre.begin() + rr + 1`。
        
2. **复杂度冗余**:
    
    - 你先把所有区间存下来的做法是 $O(N)$，然后对每个区间做二分是 $O(N \log N)$。虽然能过，但对于“非负数的子数组和”问题，**双指针 (Sliding Window)** 是 $O(N)$ 的更优解。
        
3. **单点特判**:
    
    - 如果一棵树的果子 $a_i > k$，长度应为 0。你的代码二分逻辑处理得当（`pos` 会回退），能够正确处理这种情况，值得点赞。
        

---

## ✅ 正解思路 (双指针/滑动窗口)

与其先把所有区间找出来再处理，不如**一边遍历一边维护窗口**。

1. **维护窗口 $[l, r]$**: 表示当前合法的子数组。
    
2. **右移 $r$**:
    
    - **检查断点**: 如果 $h_{r-1} \% h_r \neq 0$（高度无法整除），说明之前的链条断了。此时窗口必须重置，让 $l = r$，当前累加和重置为 $a_r$。
        
    - **累加果实**: `sum += a[r]`。
        
3. **收缩 $l$**:
    
    - 如果 `sum > k`，说明果子太多了，需要扔掉左边的树。`sum -= a[l]`，然后 `l++`。
        
4. **更新答案**: 每次 $r$ 移动并调整完 $l$ 后，更新 `ans = max(ans, r - l + 1)`。
    

---

## 🧠 举一反三 (Pattern)

- 看到 **"连续子数组"** + **"和不超过 K"** + **"元素非负"** $\rightarrow$ 想到 **滑动窗口 (双指针)**。
    
- 看到 **"相邻元素满足特定条件"** $\rightarrow$ 想到 **分组/断点**。如果条件不满足，直接重置窗口左端点。
    

---

## 💻 优化后的代码 (双指针版 $O(N)$)

这个写法比二分更短，且不需要额外的 `vector` 存储区间。

C++

```
#include<bits/stdc++.h>
using namespace std;
#define int long long

void solve(){
    int n, k;
    cin >> n >> k;
    vector<int> a(n), h(n);
    for(int i = 0; i < n; i++) cin >> a[i];
    for(int i = 0; i < n; i++) cin >> h[i];

    int l = 0;
    int current_sum = 0;
    int ans = 0;

    for(int r = 0; r < n; r++) {
        // 1. 检查高度条件 (从第二个元素开始检查)
        // 如果断了，左指针直接跳到当前位置，和重置
        if(r > 0 && h[r-1] % h[r] != 0) {
            l = r;
            current_sum = 0;
        }

        // 2. 进窗口
        current_sum += a[r];

        // 3. 维护和不超过 k (出窗口)
        while(l <= r && current_sum > k) {
            current_sum -= a[l];
            l++;
        }

        // 4. 更新答案
        ans = max(ans, r - l + 1);
    }

    cout << ans << endl;
}

signed main(){
    ios::sync_with_stdio(0); cin.tie(0); // 加上这句输入输出更快
    int t;
    cin >> t;
    while(t--){
        solve();
    }
    return 0;
}
```

### 对你原代码的修正建议

如果你想保留你的二分思路（复习二分也没问题），只需修改这一行：

C++

```
// 原代码:
auto p = upper_bound(pre.begin()+ll, pre.begin()+rr, pre[i-1]+k);

// 修正后 (注意 +1):
auto p = upper_bound(pre.begin()+ll, pre.begin()+rr + 1, pre[i-1]+k);
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
// Problem: F. Money Trees
// Contest: Codeforces - ZUA Grade 24 Round 3
// URL: https://codeforces.com/group/iNYFSQ3psN/contest/658622/problem/F
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long
typedef pair<int,int> PII;

void solve(){
	int n,k;
	cin>>n>>k;
	vector<int> a(n+3),h(n+3),pre(n+3,0);
	for(int i=1;i<=n;i++) cin>>a[i];
	for(int i=1;i<=n;i++){
		cin>>h[i];
		pre[i]=pre[i-1]+a[i];
	}
	vector<PII> inter;
	int p=1;
	for(int i=2;i<=n;i++){
		if(h[i-1]%h[i]!=0){
			inter.push_back({p,i-1});
			p=i;
		}
	}
	inter.push_back({p,n});
	
	int ans=0;
	for(auto [ll,rr]:inter){
		//printf("%d %d\n",l,r);
		for(int i=ll;i<=rr;i++){
			auto p=upper_bound(pre.begin()+ll,pre.begin()+rr,pre[i-1]+k);
			//if(*p-pre[i-1]>k) p--;
			int pos=p-pre.begin();
			//printf("%d %d  --   %d %d\n",i,pos,pre[i-1],pre[pos]);
			if(pre[pos]-pre[i-1]>k) pos--;
			//printf("%d %d  --   %d %d\n",i,pos,pre[i-1],pre[pos]);
			ans=max(ans,pos-i+1);
		}
		
	}
	
	
	printf("%d\n",ans);
	//printf("\n");
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
````



