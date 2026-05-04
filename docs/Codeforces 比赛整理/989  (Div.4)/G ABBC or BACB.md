---
created: 2025-12-18 10:07
tags:
  - algorithm/待分类
  - difficulty/待定
  - status/AC
  - site/Codeforces
---



**题目链接**: [](<% tp.file.cursor(1) %>) 
**核心考点**: [[]]






# CF1873G ABBC or BACB

## 题目描述

给你一个由字符 $\texttt{A}$ 和 $\texttt{B}$ 组成的字符串 $s$。你一开始没有硬币。你可以进行两种操作：

- 选择一个子串 $^\dagger$ $\texttt{AB}$，将其变为 $\texttt{BC}$，并获得一个硬币。
- 选择一个子串 $^\dagger$ $\texttt{BA}$，将其变为 $\texttt{CB}$，并获得一个硬币。

你最多能获得多少个硬币？$^\dagger$ 长度为 $2$ 的子串指的是字符串中两个相邻的字符序列。

## 输入格式

输入包含多组测试用例。第一行为一个整数 $t$（$1 \leq t \leq 1000$），表示测试用例的数量。

每个测试用例仅包含一行字符串 $s$（$1 \leq |s| \leq 2 \cdot 10^5$）。$s$ 的所有字符均为 $\texttt{A}$ 或 $\texttt{B}$。

所有测试用例中字符串 $s$ 的总长度不超过 $2 \cdot 10^5$。

## 输出格式

对于每个测试用例，输出一个整数，表示你最多能获得的硬币数量。

## 输入输出样例 #1

### 输入 #1

```
8
ABBA
ABA
BAABA
ABB
AAAAAAB
BABA
B
AAA
```

### 输出 #1

```
2
1
3
1
6
2
0
0
```

## 说明/提示

在第一个测试用例中，你可以进行如下操作获得 $2$ 个硬币：$\color{red}{\texttt{AB}}\texttt{BA} \to \texttt{BC}\color{red}{\texttt{BA}} \to \texttt{BCCB}$。

在第二个测试用例中，你可以进行如下操作获得 $1$ 个硬币：$\color{red}{\texttt{AB}}\texttt{A} \to \texttt{BCA}$。

在第三个测试用例中，你可以进行如下操作获得 $3$ 个硬币：$\color{red}{\texttt{BA}}\texttt{ABA} \to \texttt{CBA}\color{red}{\texttt{BA}} \to \texttt{C}\color{red}{\texttt{BA}}\texttt{CB} \to \texttt{CCBCB}$。

由 ChatGPT 4.1 翻译






## 📝 题目：CF1873G ABBC or BACB

题目链接: Codeforces 1873G

核心考点: [[贪心]]、[[思维]]、[[构造]]

### 💡 核心直觉 (一句话总结)

> 字符 `B` 是消除 `A` 的工具，一段连续的 `A` 能否被消除取决于旁边有没有 `B`。整个字符串可以看作是由 `B` 分隔开的若干段 `A`，我们拥有 $N$ 个 `B`，却有 $N+1$ 段 `A`（包括两端可能为0的段），必然有一段 `A` 无法被消除，为了硬币最多，我们**贪心地舍弃最短的那一段 `A`**。

### 🚫 易错点 & 误区 (Fail Log)

- **模拟误区**: 试图模拟 `AB -> BC` 的过程，去具体移动 `B` 的位置。这会非常复杂，因为状态多变。
    
- **边界遗漏**: 忘记考虑 `B` 在字符串开头或结尾的情况。例如 `B A A`，实际上第一段 `A` 的长度是 0（在 `B` 左边）。
    
- **BB 的理解**: 认为 `BB` 是特殊情况。其实 `BB` 仅仅意味着这两者中间夹了一段长度为 0 的 `A`。
    

### ✅ 正解思路 (解题三部曲)

1. **转化视角**: 将字符串看作是由 `B` 分隔的 `A` 数组。
    
    - `AA B A B AAA` $\rightarrow$ `[2, 1, 3]`
        
    - `B AA B` $\rightarrow$ `[0, 2, 0]` (注意 `B` 在边界或相邻时，会产生长度为 0 的 `A` 段)
        
2. **寻找规律**:
    
    - 每个 `B` 可以选择向左消除一段 `A`，或者向右消除一段 `A`。
        
    - 对于序列 `A1, B, A2, B, A3`，我们有 2 个 `B`，3 段 `A`。我们只能消除其中 2 段。
        
    - 为了总收益最大，我们显然希望保留最长的那几段，**放弃最短的那一段**。
        
3. **计算答案**:
    
    - 答案 = `所有 A 的总数` - `最短的一段 A 的长度`。
        
    - 注意：如果出现了 `BB` 或者 `B` 在开头/结尾，最短段长度就是 0，相当于拿满了所有 `A`。
        

### 🧠 举一反三 (Pattern)

- **"资源覆盖问题"**: 有 $N$ 个资源（B），$N+1$ 个任务（A段），每个资源只能做一个任务，求最大收益 $\rightarrow$ 舍弃最小的一个任务。
    
- 类似思维题目：**环形数组断开成链**的问题，往往也是枚举断点或者舍弃最小值。
    

---

### 💻 代码优化 (Code Review)

你的代码使用了 flag 来标记是否有 BB，其实这个逻辑是包含在“分段”逻辑里的。

当 BB 出现时，你的代码逻辑中 a.push_back(cnt) 会推入一个 0。

当 0 存在于数组 a 中时，排序后 a[0] 就是 0，ans -= a[0] 也就相当于没减，得出了正确答案。

因此，flag 变量其实是多余的，代码可以更简洁：

C++

```
#include<bits/stdc++.h>
using namespace std;

void solve(){
    string s;
    cin >> s;
    vector<int> a;
    int cnt = 0;
    
    // 遍历字符串，统计每段 A 的长度
    for(char c : s) {
        if(c == 'A') {
            cnt++;
        } else {
            a.push_back(cnt); // 遇到 B，结算上一段 A
            cnt = 0;          // 重置计数器
        }
    }
    a.push_back(cnt); // 别忘了最后一段
    
    // 计算总和 和 最小值
    int sum_val = 0;
    int min_val = 1e9;
    
    for(int x : a) {
        sum_val += x;
        min_val = min(min_val, x);
    }
    
    // 贪心：总数 - 最小的一段
    cout << sum_val - min_val << endl;
}

int main(){
    int t;
    cin >> t;
    while(t--){
        solve();
    }
    return 0;
}
```

#### 你的代码逻辑复盘

- **输入**: `ABBA`
    
- **你的运行逻辑**:
    
    1. `A`: `cnt=1`
        
    2. `B`: 推入 `1`, `cnt=0`
        
    3. `B`: 推入 `0`, `cnt=0` (`flag=1`)
        
    4. `A`: `cnt=1`
        
    5. 结束: 推入 `1`
        
    6. 数组 `a`: `[1, 0, 1]`。
        
    7. 排序后 `[0, 1, 1]`. `ans` 初始累加为 `2`。
        
    8. `flag` 为 1，不执行减法。输出 `2`。
        
- **优化后逻辑**:
    
    - 数组 `a`: `[1, 0, 1]`。
        
    - Sum = 2, Min = 0。
        
    - Result = 2 - 0 = 2。
        
- **结论**: 两种写法结果一致，优化版利用数学性质省去了 `if` 判断，更具有通用性（比如涵盖了 `B` 在开头的情况，如 `BA` -> `[0, 1]`，最小是0，全拿）。

---
## 💻 代码

```cpp
// Problem: CF 1873 G
// Contest: Codeforces - Codeforces Round 898 (Div. 4)
// URL: https://codeforces.com/contest/1873/problem/G
// Memory Limit: 256 MB
// Time Limit: 1000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;

void solve(){
	string s;
	cin>>s;
	int flag=0;
	vector<int> a;
	int cnt=0;
	for(int i=0;i<s.size();i++){
		if(s[i]=='A'){
			cnt++;
		}else{
			a.push_back(cnt);
			cnt=0;
		}
		if(i&&s[i]=='B'&&s[i-1]=='B') flag=1;
	}
	a.push_back(cnt);
	int ans=0;
	sort(a.begin(),a.end());
	for(int i=0;i<a.size();i++) ans+=a[i];
	if(!flag) ans-=a[0];
	printf("%d\n",ans);
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
````

