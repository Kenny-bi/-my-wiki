


## D. Blackslex and Penguin Civilization
时间限制：每测试点 2 秒  
内存限制：每测试点 256 兆字节  

## 问题描述
企鹅是文明的生物，它们使用排列进行交流。作为企鹅研究者，Blackslex 必须研究它们的交流方式。

对于一个给定的整数 $n$，考虑数组 $[0, 1, \dots, 2^n - 1]$ 的一个排列 $p$。定义
$$ S(p) = \sum_{i=0}^{2^n-1} \text{popcount}(p_0 \& p_1 \& \dots \& p_i), $$
其中 $\text{popcount}(z)$ 表示 $z$ 的二进制表示中 $1$ 的个数（例如，$\text{popcount}(5) = 2$，因为 $5 = 101_2$ 的二进制表示中有两个 $1$），而 $\&$ 表示按位与运算。

如果一个排列能使 $S(p)$ 最大化，则它被认为是**神圣的**。请找出字典序最小的神圣排列。

*长度 $n$ 的排列是一个由 $1$ 到 $n$ 这 $n$ 个不同的整数按任意顺序组成的数组。例如，$[2,3,1,5,4]$ 是一个排列，但 $[1,2,2]$ 不是排列（$2$ 在数组中出现了两次），$[1,3,4]$ 也不是排列（$n=3$ 但数组中有 $4$）。

†如果两个相同大小的数组 $a$ 和 $b$ 在第一个不同的位置上，$a$ 的元素小于 $b$ 的对应元素，则称 $a$ 字典序小于 $b$。

## 输入格式
第一行包含一个整数 $t$ $(1 \le t \le 16)$ —— 测试用例的数量。

每个测试用例包含一个整数 $n$ $(1 \le n \le 16)$。

保证所有测试用例的 $2^n$ 之和不超过 $2^{16}$。

## 输出格式
对于每个测试用例，输出 $2^n$ 个整数 $p_0, p_1, \dots, p_{2^n-1}$ —— 要求的排列。

## 样例输入
```
2
1
2
```
## 样例输出
```
1 0 
3 1 0 2 
```
## 样例说明
对于第一个测试用例，有两种可能的排列：
- $p = [0, 1]$，$S(p) = 0$
- $p = [1, 0]$，$S(p) = 1$

对于第二个测试用例，$S([3,1,0,2]) = 3$ 是神圣的。存在其他神圣的排列 $p$，例如 $p = [3,2,0,1]$，但它们不是字典序最小的。



# 错误第一版
```cpp
// Problem: CF 2179 D
// Contest: Codeforces - Codeforces Round 1071 (Div. 3)
// URL: https://codeforces.com/contest/2179/problem/D
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define ios ios::sync_with_stdio(false);cin.tie(nullptr);

void solve(){
	int n;
	cin>>n;
	vector<int> a[20];
	for(int i=0;i<1<<n;i++){
		int v=__builtin_popcount(i);
		a[v].push_back(i);
	}
	for(int i=0;i<=n;i++){
		sort(a[i].begin(),a[i].end());
	}
	
	for(int i=n;i>=0;i--){
		for(auto v:a[i]){
			cout<<v<<" ";
		}
	}
	cout<<endl;
	return ;
}

int main(){
	ios;
	int t;
	cin>>t;
	while(t--){
		solve();
	}
	return 0;
}
```



# 我的解题思路’
```cpp
// Problem: CF 2179 D
// Contest: Codeforces - Codeforces Round 1071 (Div. 3)
// URL: https://codeforces.com/contest/2179/problem/D
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define ios ios::sync_with_stdio(false);cin.tie(nullptr);

void solve(){
	int n;
	cin>>n;
	vector<int> a((1<<n)+3),st((1<<n)+3,0);
	int m=(1<<n)-1;
	cout<<m<<" ";
	st[m]=1;
	for(int i=n-1;i>=0;i--){
		m^=(1<<i);
		st[m]=1;
		cout<<m<<" ";
	}
	for(int i=1;i<1<<n;i++){
		if(!st[i]) cout<<i<<" ";
	}
	cout<<endl;
	return ;
}

int main(){
	ios;
	int t;
	cin>>t;
	while(t--){
		solve();
	}
	return 0;
}
```




# 确实是好题目
你的第二版解题思路（贪心法）**不是最优的**，虽然它在 $n=2$ 时能过样例，但在 $n$ 更大时无法得到最大分数。

### 为什么你的思路有问题？

你的思路是：$7 \to 3 \to 1 \to 0$（每次去掉最高位的一个 1）。

这种做法的问题在于过早地引入了 0。一旦排列中出现了 $0$，前缀 AND 的结果就会永久变成 $0$，后面的数字无论是什么，贡献都是 $0$。

**举个例子 ($n=3$)：**

- **你的构造：** `7, 3, 1, 0, 2, 4, 5, 6`
    
    - 前缀 AND：$7, 3, 1, 0, 0, 0, 0, 0$
        
    - Popcount：$3 + 2 + 1 + 0 + \dots = 6$
        
- **更优构造：** `7, 3, 1, 5, 0, 2, 4, 6`
    
    - 前缀 AND：
        
        - $7 \ (111) \to$ pop 3
            
        - $3 \ (011) \to$ 与上一个 $111$ 还是 $011$，pop 2
            
        - $1 \ (001) \to$ 与上一个 $011$ 还是 $001$，pop 1
            
        - $5 \ (101) \to$ 与上一个 $001$ AND 得到 $001$，**pop 还是 1**（这是关键！虽然 5 没有 bit 1，但它有 bit 0，延续了 bit 0 的寿命）
            
        - $0 \ (000) \to$ pop 0
            
    - 总分：$3 + 2 + 1 + 1 + 0 \dots = 7$
        

### 正确的解题思路（分治/位保留策略）

为了让分数最大，我们需要让每一个二进制位尽可能长时间地保持为 1。

为了让字典序最小，我们在选择“保留哪一位”时，应该优先保留低位（LSB），因为保留低位（如 bit 0）允许我们使用较小的奇数（1, 3...），而保留高位（如 bit $n-1$）会强制我们使用较大的数（$2^{n-1} \dots$）。

**构造策略如下：**

1. **第一个数**必须是 $2^n - 1$（全 1），以开启最大分数。
    
2. **接下来**，我们按照“倒序剥离”的方式来生成序列。
    
    - 所有的数可以分为两类：$bit \ 0$ 是 1 的，和 $bit \ 0$ 是 0 的。
        
    - 为了让 $bit \ 0$ 活得最久（贡献最大），我们先把所有 $bit \ 0$ 为 1 的数放前面（递归处理），等这些都放完了，再放 $bit \ 0$ 为 0 的数。
        
    - 在“$bit \ 0$ 为 1”的这一组里，我们再把 $bit \ 1$ 为 1 的放前面……
        
    - 这实际上形成了一个特定的输出顺序。
        

直接构造算法（非递归版）：

观察发现，最优且字典序最小的序列遵循以下规律：

1. 先输出 $2^n - 1$。
    
2. 从 $i = n-1$ 循环到 $0$：
    
    输出所有满足以下条件的数 $x$（按从小到大排序）：
    
    - $x$ 的第 $0$ 到 $i-1$ 位全是 1。
        
    - $x$ 的第 $i$ 位是 0。
        
    - $x$ 的高于 $i$ 的位任意。
        

这个构造保证了第 $0 \sim i-1$ 位一直保持为 1，直到我们在这一轮把第 $i$ 位变成 0。

### 优化后的代码

C++

```cpp
// Problem: CF 2179 D
// Contest: Codeforces - Codeforces Round 1071 (Div. 3)
// URL: https://codeforces.com/contest/2179/problem/D
// Memory Limit: 256 MB
// Time Limit: 2000 ms

#include<bits/stdc++.h>
using namespace std;
#define ios ios::sync_with_stdio(false);cin.tie(nullptr);

void solve(){
    int n;
    cin >> n;
    
    // 1. 首先输出全 1 的数
    cout << (1 << n) - 1 << " ";
    
    // 2. 按照层级倒序输出
    // 每一层 i 代表：我们要"放弃"第 i 位了
    // 在这之前，比 i 低的位 (0 到 i-1) 必须全是 1
    for(int i = n - 1; i >= 0; i--){
        // 后缀掩码：低 i 位全是 1
        // 例如 i=2 (放弃bit 2), mask = (1<<2)-1 = 3 (二进制 011)
        int suffix_mask = (1 << i) - 1;
        
        // 这一层的数必须满足：
        // (x & suffix_mask) == suffix_mask  (低位全1)
        // (x >> i) & 1 == 0                 (第 i 位是 0)
        
        // 我们遍历高位部分来生成这些数
        // 高位部分共有 n - 1 - i 位
        int high_bits_count = n - 1 - i;
        int max_high = 1 << high_bits_count;
        
        for(int h = 0; h < max_high; h++){
            // 拼接：高位部分 + (0) + 低位部分
            int val = (h << (i + 1)) | suffix_mask;
            cout << val << " ";
        }
    }
    cout << endl;
}

int main(){
    ios;
    int t;
    cin >> t;
    while(t--){
        solve();
    }
    return 0;
}
```

### 代码解析 ($n=3$ 的例子)

1. 输出 `7` (111)。
    
2. 循环 $i=2$ (放弃 bit 2):
    
    - 要求：低 2 位是 1 (mask `011` 的低 2 位 -> `x11` ? 不，是 `suffix_mask` 长度为 $i$)。
        
    - Wait, logic correction in explanation:
        
    - $i=2$: mask = `(1<<2)-1` = 3 (`011` 二进制).
        
    - 生成的数：Bit 2 必须是 0。低 0,1 位必须是 1。即 `...011`。
        
    - 在这个循环里，`h` 从 0 遍历到 0。`val = (0 << 3) | 3 = 3`。
        
    - 输出 `3`。
        
3. 循环 $i=1$ (放弃 bit 1):
    
    - mask = `1` (`01` 二进制)。
        
    - 生成的数：Bit 1 必须是 0。Bit 0 必须是 1。即 `...01`。
        
    - `h` 遍历 0 到 1。
        
    - `h=0`: `val = (0<<2) | 1 = 1`。
        
    - `h=1`: `val = (1<<2) | 1 = 5`。
        
    - 输出 `1, 5`。
        
4. 循环 $i=0$ (放弃 bit 0):
    
    - mask = `0`。
        
    - 生成的数：Bit 0 必须是 0。即 `...0`。
        
    - `h` 遍历 0 到 3。
        
    - `0, 2, 4, 6`。
        
    - 输出 `0, 2, 4, 6`。
        

最终结果：`7 3 1 5 0 2 4 6`。这与我分析的最优解一致。




# 最终AC 代码
```cpp
// Problem: CF 2179 D
// Contest: Codeforces - Codeforces Round 1071 (Div. 3)
// URL: https://codeforces.com/contest/2179/problem/D
// Memory Limit: 256 MB
// Time Limit: 2000 ms

#include<bits/stdc++.h>
using namespace std;
#define ios ios::sync_with_stdio(false);cin.tie(nullptr);

void solve(){
    int n;
    cin>>n;
    cout<<(1<<n)-1<<" ";
    for(int i=n-1;i>=0;i--){
        int cur=(1<<i)-1;
        int c=n-1-i;
        int mx=1<<c;
        for(int h=0;h<mx;h++){
            int v=(h<<(i+1))|cur;
            cout<<v<<" ";
        }
    }
    cout<<endl;
}

int main(){
    ios;
    int t;
    cin >> t;
    while(t--){
        solve();
    }
    return 0;
}
```