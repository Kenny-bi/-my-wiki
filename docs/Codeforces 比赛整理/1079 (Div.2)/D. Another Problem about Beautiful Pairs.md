



## D. Another Problem about Beautiful Pairs

时间限制：每个测试点2秒  
内存限制：每个测试点256兆字节

## 问题描述

在数组 $a$ 中，如果一对下标 $(i, j)$ 满足以下条件，则称其为**美丽对**：

$$a_i \cdot a_j = j - i$$

请计算数组 $a$ 中美丽对的数量。

## 输入格式

每个测试包含多个测试用例。第一行包含一个整数 $t$，表示测试用例的数量（$1 \le t \le 10^4$）。  
接下来是每个测试用例的描述。

每个测试用例的第一行包含一个整数 $n$（$2 \le n \le 2 \times 10^5$）。

第二行包含 $n$ 个整数 $a_1, a_2, \ldots, a_n$（$1 \le a_i \le 10^9$）。

**输入附加约束条件**：

所有测试用例中 $n$ 的总和不超过 $2 \times 10^5$。

## 输出格式

对于每个测试用例，输出一个整数——表示该数组中美丽对的数量。

## 样例输入

```
4
5
1 1 2 100 4
6
2 2 1 1 2 2
10
1 1 2 3 4 1 1 7 3 9
2
1000000000 1000000000
```

## 样例输出

```
3
7
10
0
```

## 样例说明

在第一个示例中，共有 $3$ 个美丽对：$(1,2)$、$(1,3)$ 和 $(1,5)$。

在第二个示例中，共有 $7$ 个美丽对：$(1,3)$、$(1,5)$、$(2,4)$、$(2,6)$、$(3,4)$、$(3,5)$ 和 $(4,6)$。



# AC 代码
```cpp
// Problem: CF 2197 D
// Contest: Codeforces - Codeforces Round 1079 (Div. 2)
// URL: https://codeforces.com/contest/2197/problem/D
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N=450;

void solve(){
	int n;
	cin>>n;
	vector<int> a(n);
	for(int i=0;i<n;i++) cin>>a[i];
	
	int ans=0;
	// for(int i=0;i<n;i++){
		// for(int j=i+1;j<n;j++){
			// if(a[i]*a[j]==j-i){
				// cout<<a[i]<<" "<<a[j]<<" "<<i<<" "<<j<<endl;
			// }
		// }
	// }
	
	for(int i=1;i<=N;i++){
		for(int j=0;j<n;j++){
			int pos=j-i*a[j];
			if(pos>=0&&pos<n&&a[pos]==i) ans++;
		}
	}
	
	for(int i=0;i<n;i++){
		if(a[i]<=N) continue;
		for(int j=i+a[i];j<n;j+=a[i]){
			if(a[i]*a[j]==j-i) ans++;
		}
	}
	
	cout<<ans<<endl;
	return ;
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







# 算法笔记：CF2197D - Another Problem about Beautiful Pairs

## 1. 核心瓶颈与突破口

### 题目公式

我们需要找到满足以下条件的下标对 $(i, j)$，其中 $i < j$：

$$a_i \cdot a_j = j - i$$

### 关键观察（The Key Insight）

最朴素的暴力做法是枚举 $i$ 和 $j$，复杂度 $O(n^2)$，显然会超时（$n \le 2 \cdot 10^5$）。我们需要利用题目隐含的限制条件。

由于 $j$ 和 $i$ 是数组下标，$1 \le i < j \le n$，所以下标之差一定满足：

$$1 \le j - i < n$$

将公式代入，得到一个非常强的约束：

$$a_i \cdot a_j < n$$

**推论：**

如果两个正整数的乘积小于 $n$，那么**其中至少有一个数必须小于等于 $\sqrt{n}$**。

这个性质提示我们可以使用 **根号分治（Square Root Decomposition）** 的思想来解决问题。

---

## 2. 算法设计：根号分治

设定一个阈值 $B = \sqrt{n} \approx 450$。

我们可以根据**左边那个数（$a_i$）的大小**，将所有可能的“美丽对”分为两类互斥的情况：

1. **情况一：左边的数较小 ($a_i \le B$)**
    
2. **情况二：左边的数较大 ($a_i > B$)**
    

这两种情况覆盖了所有可能性，且互不重叠，不需要额外的去重逻辑。

### 第一部分：处理 $a_i \le B$ 的情况

- **思路**：枚举 $a_i$ 的**数值**。
    
- **逻辑**：
    
    1. 外层枚举数值 $v$ 从 $1$ 到 $B$（假设 $a_i = v$）。
        
    2. 内层遍历数组的所有位置 $j$（视作右端点）。
        
    3. 根据公式 $v \cdot a_j = j - i$，反推左端点的位置：$i = j - v \cdot a_j$。
        
    4. **检查**：计算出的 $i$ 是否合法（$0 \le i < j$），且 $a_i$ 的值是否真的等于 $v$。
        
- **复杂度**：$O(n \cdot B) \approx O(n\sqrt{n})$。
    

### 第二部分：处理 $a_i > B$ 的情况

- **思路**：枚举左端点 $i$ 的**位置**，利用倍数跳跃。
    
- **逻辑**：
    
    1. 遍历左端点 $i$。
        
    2. 如果 $a_i \le B$，直接跳过（因为这种情况已经在第一部分算过了）。
        
    3. 如果 $a_i > B$，我们去寻找右端点 $j$。根据公式 $j - i = a_i \cdot a_j$，说明 $j - i$ 必须是 $a_i$ 的倍数。
        
    4. **跳跃**：$j$ 从 $i + a_i$ 开始，每次增加 $a_i$。
        
    5. **检查**：验证公式 $a_i \cdot a_j = j - i$ 是否成立。
        
- **复杂度**：因为 $a_i > \sqrt{n}$，所以内层循环次数最多为 $n / \sqrt{n} = \sqrt{n}$。总复杂度 $O(n\sqrt{n})$。
    

---

## 3. 代码解析（基于你的 AC 代码）

C++

```
#define int long long // 必须开 long long，防止乘法溢出
const int N=450;      // 阈值 sqrt(200000) ≈ 447

void solve(){
    // ... 输入处理 ...
    
    // --- 第一部分：枚举左边数值 a[i] <= 450 ---
    // i 在这里代表“数值”，即假设 a[left] == i
    for(int i=1; i<=N; i++){
        // j 在这里代表“右边位置”，即 right
        for(int j=0; j<n; j++){
            // 核心公式变形：left = right - a[left] * a[right]
            // 代入后：pos = j - i * a[j]
            int pos = j - i * a[j]; 
            
            // 检查：位置未越界，且该位置的值确实等于我们要找的数值 i
            if(pos>=0 && pos<n && a[pos]==i) ans++;
        }
    }
    
    // --- 第二部分：枚举左边数值 a[i] > 450 ---
    // i 在这里代表“左边位置”，即 left
    for(int i=0; i<n; i++){
        // 如果左边数值很小，已经在第一部分处理过，跳过
        if(a[i]<=N) continue;
        
        // j 在这里代表“右边位置”，即 right
        // 因为 a[i] 很大，步长很大，循环次数很少
        for(int j=i+a[i]; j<n; j+=a[i]){
            // 验证公式
            if(a[i]*a[j] == j-i) ans++;
        }
    }
    // ... 输出 ...
}
```

---

## 4. 通用做题方法论：如何解决“索引与数值关联”类题目

这类题目通常具有以下形式：

$$f(\text{value}_i, \text{value}_j) = g(\text{index}_i, \text{index}_j)$$

例如本题：$a_i \cdot a_j = j - i$。

### 核心套路四步走：

1. **观察不等式约束（Bounds Check）**
    
    - 首先看右边的索引部分 $g(i, j)$。由于数组长度限制，索引运算的结果通常有上界（比如 $< n$）。
        
    - 将这个上界套用到左边的数值部分 $f(a_i, a_j)$，看能否得出数值大小的限制。
        
    - _本题案例_：$j - i < n \implies a_i \cdot a_j < n$。
        
2. **确定分治策略（Strategy Selection）**
    
    - 如果限制暗示了“数值不能同时很大”，通常使用 **根号分治**。
        
    - 如果限制是加法（如 $a_i + a_j = j - i$），通常使用 **移项 + 桶（Map/Hash）**。
        
        - 变形：$a_i + i = j - a_j$。
            
        - 做法：遍历 $j$，统计之前有多少个 $i$ 满足 $a_i + i$ 等于当前所需的 $j - a_j$。
            
3. **根号分治模板（对于乘法类限制）**
    
    - 设定阈值 $B = \sqrt{N}$。
        
    - **策略 A（分类讨论）**：
        
        - 枚举 $a_i \le B$ 的情况（暴力枚举数值）。
            
        - 枚举 $a_i > B$ 的情况（暴力跳跃索引）。
            
    - **策略 B（统一枚举，带检查）**：
        
        - 枚举较小值 $v$ 从 $1$ 到 $B$。
            
        - 遍历数组，假设当前 $a_k$ 是较大值，根据公式计算另一个数的位置。
            
        - **注意去重**：只在“计算出的那个数 $\ge$ 当前枚举的较小值 $v$”时才统计。
            
4. **注意数据溢出（Overflow Check）**
    
    - 这类题目涉及下标与数值的运算（尤其是乘法），非常容易爆 `int`。
        
    - **铁律**：凡是涉及 $a_i \cdot a_j$ 或 $i \cdot a_j$ 的，**必须使用 `long long`**。
        

### 总结

当你看到题目中出现 **$a_i \cdot a_j$** 和 **$j-i$**（或 $j+i$）混合在一起时，第一反应应该是：**乘积受限于 $N$，利用根号分治，将大数和小数分开处理。**