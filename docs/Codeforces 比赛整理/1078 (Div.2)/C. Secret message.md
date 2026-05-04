



## C. Secret message
时间限制：每个测试点 2 秒
内存限制：每个测试点 256 MB

## 问题描述
天才间谍 Bob 截获了一条加密信息。他推测这条信息包含秘密情报，并正积极地试图破解它。

落入间谍手中的便条由 $k$ 张纸条组成，每张纸条的长度为 $n$，包含严格的小写拉丁字母。凭借破解此类文档的丰富经验，Bob 猜测他所感兴趣的信息（便条的破解结果）也是一个长度为 $n$ 的字符串，并且该信息的第 $i$ 个字母对应于其中某张纸条的第 $i$ 个字母。

Bob 认为，一个字符串 $s$ 的信息性被定义为最小的正整数 $d$，使得存在一个长度为 $d$ 的字符串 $t$，且 $s$ 可以通过将 $t$ 重复若干次来构成。例如，字符串 "aaaa" 的信息性是 1，字符串 "abab" 的信息性是 2，字符串 "abcd" 的信息性是 4。

Bob 推测，便条的作者为了确保信息可靠传输，将消息中的数据重复了若干次。因此，他认为一个更合理的便条破解结果应该具有尽可能小的信息性。

帮助这位间谍：找到那个能够破解便条，并且具有最小可能信息性的消息。

## 输入格式
每个测试包含多个测试用例。第一行包含测试用例的数量 $t$ $(1 \le t \le 10^4)$。接下来是测试用例的描述。

每个测试用例的第一行包含数字 $n$ 和 $k$ $(2 \le n, k \le 50000$, $4 \le n \cdot k \le 10^5)$ —— 分别表示纸条的长度和纸条的数量。

在接下来每个测试用例的 $k$ 行中，每行包含一个由 $n$ 个小写拉丁字母组成的序列 —— 下一张纸条的内容。

保证所有测试用例的 $n \cdot k$ 值之和不超过 $10^5$。

## 输出格式
对于每个测试用例，输出一个长度为 $n$ 的字符串 —— 具有最小可能信息性的便条破解结果。如果有多个合适的答案，可以输出其中任意一个。

## 样例输入
```
3
3 2
abc
baa
9 2
iiinnnfff
nnfiffinn
4 2
acbd
bdac
```

## 样例输出
```
aaa
infinfinf
acac
```

## 样例说明
在第一个测试用例中，最小可能的信息性是 $1$：
- 纸条1：`abc`
- 纸条2：`baa`

在第二个测试用例中，最小可能的信息性是 $3$：
- 纸条1：`iiinnnfff`
- 纸条2：`nnfiffinn`




# AI  牛逼
```cpp
// Problem: CF 2194 C
// Contest: Codeforces - Codeforces Round 1078 (Div. 2)
// URL: https://codeforces.com/contest/2194/problem/C
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;

void solve(){
	int n,k;
	cin>>n>>k;
	vector<int> a(n,0);
	vector<string> s(k);
	for(int i=0;i<k;i++){
		cin>>s[i];
		for(int j=0;j<n;j++){
			a[j]|=(1<<(s[i][j]-'a'));
		}
	}
	
	for(int d=1;d<=n;d++)
	{
		if(n%d!=0) continue;
		string p;
		int flag=1;
		for(int i=0;i<d;i++)
		{
			int cur=(1<<26)-1;
			for(int j=i;j<n;j+=d){
				cur&=a[j];
			}
			if(!cur) flag=0;
			else{
				for(int k=0;k<26;k++){
					if((1<<k)&cur){
						p+=('a'+k);
						break;
					}
				}
			}
		}
		
		if(flag){
			string ans="";
			for(int i=0;i<n/d;i++) ans+=p;
			cout<<ans<<endl;
			return ;
		}
		
	}
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





# AI 解题思路

### 核心思维路径

1. **“竖”着看数据**：
    
    题目说结果字符串 $S$ 的第 $j$ 个字符必须来自输入的那 $k$ 张纸条的第 $j$ 列。
    
    这意味着：对于结果字符串的第 $j$ 位，我们并不关心它来自哪一张纸条，我们只关心**这一列里有哪些字符是可用的**。
    
    - 比如样例 1：
        
        Plaintext
        
        ```
        abc
        baa
        ```
        
        - 第 0 列可用的字符是 `{a, b}`
            
        - 第 1 列可用的字符是 `{b, a}`
            
        - 第 2 列可用的字符是 `{c, a}`
            
2. **理解“信息性”（周期性）**：
    
    题目定义“信息性”为最小的 $d$，使得字符串是由长度为 $d$ 的片段重复构成的。
    
    这意味着：
    
    - $n$ 必须能被 $d$ 整除。
        
    - 结果字符串 $S$ 必须满足：$S[0] = S[d] = S[2d] = \dots$
        
    - 同理， $S[1] = S[1+d] = S[1+2d] = \dots$
        
3. **结合两点：求交集**：
    
    如果我们假设答案的周期是 $d$，那么对于模式串的第 $i$ 个字符（$0 \le i < d$），它必须同时存在于原数据的第 $i$ 列、第 $i+d$ 列、第 $i+2d$ 列……中。
    
    也就是：
    
    $$S[i] \in (\text{第 } i \text{ 列的字符集合}) \cap (\text{第 } i+d \text{ 列的字符集合}) \cap \dots$$
    
    只要这个交集不是空的，我们就可以随便从里面选一个字符作为 $S[i]$。
    

### 算法步骤

1. **预处理**：
    
    不要存字符串，直接存**每一列的字符掩码（Bitmask）**。
    
    因为只有 26 个小写字母，我们可以用一个 `int` 整数来表示一列里有哪些字符。比如第 $j$ 列有 'a' 和 'c'，那么 `mask[j]` 的第 0 位和第 2 位就是 1。
    
    这一步复杂度是 $O(nk)$，非常快。
    
2. **枚举周期 $d$**：
    
    我们要最小的信息性，所以从 $d=1$ 开始枚举，直到 $d=n$。
    
    只有当 $n \% d == 0$ 时，才去检查这个 $d$ 是否合法。
    
3. **检查合法性**：
    
    对于当前的 $d$，我们遍历 $0$ 到 $d-1$ 的每一个偏移量 $r$：
    
    - 算出所有对应列的掩码交集：`result = mask[r] & mask[r+d] & mask[r+2d] ...`
        
    - 如果 `result` 变成了 0（空集），说明这个 $d$ 不行，直接跳出。
        
    - 如果 `result` 不为 0，说明有公共字符。我们随便取一个（比如最低位的 1 对应的字符）记下来。
        
4. **构造答案**：
    
    如果对于 $0$ 到 $d-1$ 的所有位置都找到了公共字符，那么我们就成功了！因为我们要找最小的 $d$，所以找到的第一个解就是最优解。把这个长度为 $d$ 的模式串重复 $n/d$ 次输出即可。
    

### 图解示例 (样例 2)

Plaintext

```
n=9, k=2
纸条1: i i i n n n f f f
纸条2: n n f i f f i n n
```

**检查 $d=3$**：

- **第 1 位 (index 0, 3, 6)**:
    
    - Col 0: {i, n} -> `mask_0`
        
    - Col 3: {n, i} -> `mask_3`
        
    - Col 6: {f, i} -> `mask_6`
        
    - `mask_0 & mask_3 & mask_6` = {i}。有效！选 'i'。
        
- **第 2 位 (index 1, 4, 7)**:
    
    - Col 1: {i, n}
        
    - Col 4: {n, f}
        
    - Col 7: {f, n}
        
    - 交集是 {n}。有效！选 'n'。
        
- **第 3 位 (index 2, 5, 8)**:
    
    - Col 2: {i, f}
        
    - Col 5: {n, f}
        
    - Col 8: {f, n}
        
    - 交集是 {f}。有效！选 'f'。
        

成功！模式串是 "inf"，重复 3 次得到 "infinfinf"。

### AC 代码

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

void solve() {
    int n, k;
    if (!(cin >> n >> k)) return;

    // 用 vector<int> 存储每一列的字符掩码
    // mask[j] 的第 b 位为 1 表示第 j 列存在字符 ('a' + b)
    vector<int> col_masks(n, 0);

    for (int i = 0; i < k; ++i) {
        string s;
        cin >> s;
        for (int j = 0; j < n; ++j) {
            col_masks[j] |= (1 << (s[j] - 'a'));
        }
    }

    // 从小到大枚举可能的周期长度 d
    for (int d = 1; d <= n; ++d) {
        // 只有当 d 是 n 的约数时，才可能是合法的周期
        if (n % d == 0) {
            string pattern = "";
            bool possible = true;

            // 检查这个周期 d 下，每一个位置是否都有公共字符
            for (int i = 0; i < d; ++i) {
                // 初始化交集为全 1 (即所有字母都假设存在)
                int common = (1 << 26) - 1;

                // 遍历所有对应的列：i, i+d, i+2d...
                for (int j = i; j < n; j += d) {
                    common &= col_masks[j];
                }

                if (common == 0) {
                    possible = false;
                    break; // 这个 d 不行，换下一个
                } else {
                    // __builtin_ctz 返回二进制末尾 0 的个数，即最低位 1 的位置
                    // 这就是我们要找的公共字符
                    int char_idx = __builtin_ctz(common);
                    pattern += (char)('a' + char_idx);
                }
            }

            if (possible) {
                // 找到了最小的 d！构造并输出结果
                string ans = "";
                for (int i = 0; i < n / d; ++i) {
                    ans += pattern;
                }
                cout << ans << endl;
                return; 
            }
        }
    }
}

int main() {
    // 开启加速，防止 IO 超时
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);
    
    int t;
    if (cin >> t) {
        while (t--) {
            solve();
        }
    }
    return 0;
}
```

### 复杂度分析

- **预处理**：我们需要遍历所有字符一次来构建掩码，复杂度是 $O(N \cdot K)$。题目保证 $\sum NK \le 10^5$，所以这步非常快。
    
- **检查**：我们要枚举 $N$ 的因子。对于每个因子 $d$，我们需要遍历 $N$ 个掩码。最坏情况下（比如 $N$ 有很多因子），总操作数大概是 $O(N \times \text{因子个数})$。但是 $N \le 50000$ 的数因子很少（最多几十个），所以这步也极快。
    

别怕暴力枚举，只要枚举的方向（从小到大枚举因子）是对的，再配合位运算加速集合求交集，这就是正解！












# 📝 算法总结：CF 2194 C - Secret Message

#### 1. 问题本质

我们需要构造一个长度为 $n$ 的字符串 $S$，满足两个条件：

1. **列约束**：$S[j]$ 必须存在于输入 $k$ 个字符串的第 $j$ 列字符集合中。
    
2. **周期约束**：$S$ 由一个长度为 $d$ 的模式串重复构成，且我们要让 $d$ 最小。
    

#### 2. 核心思维转换

- **横向转纵向**：不要把输入看作 $k$ 个行字符串，而要看作 $n$ 个“列集合”。第 $j$ 列所有出现过的字符，就是 $S[j]$ 的候选池。
    
- **周期性的数学表达**：如果 $S$ 的周期为 $d$，意味着 $S[i] = S[i+d] = S[i+2d] \dots$。
    
    因此，这一系列位置 $(i, i+d, i+2d \dots)$ 对应的列集合必须有**公共交集**。
    

#### 3. 数据结构技巧 (Bitmask)

由于字符集很小（只有 'a'-'z' 共 26 个），使用 `std::set` 或 `bool` 数组效率太低。

- **技巧**：用一个 `int` 整数代表一列的字符集合。
    
- **操作**：第 $j$ 列包含字符 `c` $\rightarrow$ `mask[j] |= (1 << (c - 'a'))`。
    
- **求交集**：直接使用位运算与 `&`，即 `intersection = mask[a] & mask[b]`，这是 $O(1)$ 的操作。
    

#### 4. 算法流程

1. **预处理**：遍历输入，生成 $n$ 个整数掩码 `col_masks`。
    
2. **枚举周期**：从小到大枚举 $d$ ($1 \to n$)。
    
    - **剪枝**：如果 $n \% d \neq 0$，直接跳过。
        
3. **验证周期**：对于当前 $d$，检查每一个偏移量 $r \in [0, d-1]$：
    
    - 计算该组所有位置的交集：`common = mask[r] & mask[r+d] & mask[r+2d] ...`
        
    - 如果 `common == 0`，说明该组位置没有共同字符，当前 $d$ 无效。
        
    - 如果 `common > 0`，取 `__builtin_ctz(common)` (最低位 1) 对应的字符作为模式串的第 $r$ 个字符。
        
4. **输出**：找到的第一个有效 $d$ 即为最小值，按该模式串生成结果并输出。
    

#### 5. 复杂度分析

- **时间复杂度**：
    
    - 预处理：$O(N \cdot K)$。
        
    - 枚举与检查：$O(\sum_{d|N} \frac{N}{d} \cdot d) = O(\sigma_0(N) \cdot N)$。
        
    - 由于 $N \le 50000$，因数个数很少，整体运行极快，远低于 2秒限制。
        
- **空间复杂度**：$O(N)$，仅需存储每一列的掩码。
    

#### 💡 刷题心得

遇到“字符集很小”且涉及“集合求交/并”的问题，第一时间想到 **位运算 (Bitmask)** 压缩状态。这通常能将复杂度降低常数级甚至一个维度。