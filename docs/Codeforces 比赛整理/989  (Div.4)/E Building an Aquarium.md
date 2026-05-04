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


# CF1873E Building an Aquarium

## 题目描述

你喜欢鱼，因此你决定建造一个水族箱。你有一块由 $n$ 列组成的珊瑚，第 $i$ 列的高度为 $a_i$ 单位。接下来，你将按照如下方式在珊瑚周围建造水箱：

- 选择一个整数 $h \geq 1$，作为水箱的高度。在水箱的两侧建造高度为 $h$ 的墙。
- 然后，向水箱中注水，使得每一列的水面高度为 $h$，除非该列珊瑚的高度超过 $h$，此时该列不再加水。

例如，若 $a=[3,1,2,4,6,2,5]$ 且 $h=4$，你最终会用掉 $w=8$ 单位的水，如下图所示。  
![](https://cdn.luogu.com.cn/upload/vjudge_pic/CF1873E/7c1e58fc4d1641ece42f30c898039b9158eaffda.png)  
你最多可以用 $x$ 单位的水来填充水箱，但你希望建造尽可能高的水箱。你能选择的最大 $h$ 是多少？

## 输入格式

第一行包含一个整数 $t$（$1 \leq t \leq 10^4$），表示测试用例的数量。

每个测试用例的第一行包含两个正整数 $n$ 和 $x$（$1 \leq n \leq 2 \cdot 10^5$；$1 \leq x \leq 10^9$），分别表示珊瑚的列数和你最多能用的水量。

每个测试用例的第二行包含 $n$ 个用空格分隔的整数 $a_i$（$1 \leq a_i \leq 10^9$），表示每一列珊瑚的高度。

所有测试用例中 $n$ 的总和不超过 $2 \cdot 10^5$。

## 输出格式

对于每个测试用例，输出一个正整数 $h$（$h \geq 1$），表示你能建造的最大水箱高度，使得填充水箱所需的水量不超过 $x$ 单位。

我们保证在这些约束下，总能找到这样的 $h$。

## 输入输出样例 #1

### 输入 #1

```
5
7 9
3 1 2 4 6 2 5
3 10
1 1 1
4 1
1 4 3 4
6 1984
2 6 5 9 1 8
1 1000000000
1
```

### 输出 #1

```
4
4
2
335
1000000001
```

## 说明/提示

第一个测试用例如题面所示。若 $h=4$，需要 $8$ 单位的水，但若 $h$ 增加到 $5$，则需要 $13$ 单位的水，超过了 $x=9$。因此 $h=4$ 是最优的。

在第二个测试用例中，我们可以选择 $h=4$，每一列加 $3$ 单位水，总共用掉 $9$ 单位水。这是最优的。

在第三个测试用例中，我们可以选择 $h=2$，用完所有的水量，也是最优的。

由 ChatGPT 4.1 翻译











**核心考点**: `[[二分答案]]`、`[[前缀和/贪心]]`

### 💡 核心直觉 (一句话总结)

> 题目要求“最大的 $h$”，且 $h$ 越大耗水越多（具有单调性），直接计算很难，但**验证**一个给定的 $h$ 是否合法非常简单（$O(n)$），因此使用二分答案。

### 🚫 易错点 & 误区 (Fail Log)

- **数据溢出**: 水量 $x$ 和高度 $a_i$ 都在 $10^9$ 级别，计算总水量时极易爆 `int`，必须全程使用 `long long`。
    
- **二分上界 ($r$)**: 不要只设置为 $10^9$ 或 $\max(a_i)$。如果只有一根珊瑚且 $x$ 很大，高度可能达到 $2 \cdot 10^9$。上界应设为 `2e9 + 7` 或 `max(a[i]) + x`。
    
- **Check 函数溢出**: 在累加 `ans` 时，如果中间结果已经超过 `x`，应立即 `return false`，否则累加值可能超过 `long long` 范围（虽然本题 $2 \cdot 10^5 \times 2 \cdot 10^9$ 刚好在 `long long` 范围内，但养成习惯更好）。
    

### ✅ 正解思路 (解题三部曲)

1. **确定单调性**: 水箱修得越高，需要的水越多。满足单调性，确定使用二分法。
    
2. **确定边界**:
    
    - $l = 1$ (最少高度)。
        
    - $r = 2 \cdot 10^9$ (最大可能的物理高度)。
        
3. **编写 Check(mid)**:
    
    - 遍历所有珊瑚。
        
    - 如果你比 $mid$ 矮，我就得补水 `mid - a[i]`。
        
    - 如果你比 $mid$ 高，我就不用补水。
        
    - 判断总补水量是否 $\le x$。
        

### 🧠 举一反三 (Pattern)

- 看到 **"求最大的最小值"** 或 **"求最小的最大值"** $\rightarrow$ 想到 **二分答案**。
    
- 看到 **"验证答案很容易，直接算很难"** $\rightarrow$ 想到 **二分答案**。
    
- 类似题目：[砍树 (EKO)](https://www.luogu.com.cn/problem/P1873)、[进击的奶牛](https://www.luogu.com.cn/problem/P1824)。
    

---

## 💻 代码优化建议 (Code Review)

你的代码逻辑完全正确，以下是针对竞赛细节的微调建议：

C++

```
// 建议 1: 尽量不要在 main 函数外用 #define int long long，
// 虽然省事，但有时会导致 printf("%d") 输出错误或意想不到的内存开销。
// 建议显式使用 long long。

bool check(long long mid, long long x, const vector<int>& a) {
    long long needed = 0;
    for (int h : a) {
        if (h < mid) {
            needed += (mid - h);
        }
        // 优化: 剪枝，防止溢出且加速
        if (needed > x) return false; 
    }
    return needed <= x;
}

void solve() {
    int n; 
    long long x; // 注意 x 要开 long long
    cin >> n >> x;
    vector<int> a(n);
    for (int i = 0; i < n; i++) cin >> a[i];

    long long l = 1, r = 2e9 + 7; // 右边界给大一点
    while (l < r) {
        long long mid = (l + r + 1) / 2; // 向上取整，避免死循环
        if (check(mid, x, a)) {
            l = mid; // mid 可行，尝试更大的
        } else {
            r = mid - 1; // mid 不行，往小了找
        }
    }
    cout << l << endl; // 推荐用 cout 避免 printf 类型不匹配问题
}
```

下一步建议：

这道题你已经掌握得很好了。如果想巩固二分答案，建议去刷洛谷的 P1873 EKO / 砍树，那道题逻辑和这道题几乎一模一样，是检验肌肉记忆的好机会。


## 💻 代码
```cpp
// Problem: E. Building an Aquarium
// Contest: Codeforces - ZUA Grade 24 Round 3
// URL: https://codeforces.com/group/iNYFSQ3psN/contest/658622/problem/E
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long

bool check(int mid,int x,vector<int>& a){
	int ans=0;
	for(int i=0;i<a.size();i++){
		if(a[i]<=mid) ans+=mid-a[i];
		if(ans>x) return false;
	}
	return true;
}

void solve(){
	int n,x;
	cin>>n>>x;
	vector<int> a(n);
	for(int i=0;i<n;i++){
		cin>>a[i];
	}
	int l=0,r=2e9+3;
	while(l<r){
		int mid=(l+r+1)/2;
		if(check(mid,x,a)) l=mid;
		else r=mid-1;
	}
	printf("%d\n",l);
	
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



