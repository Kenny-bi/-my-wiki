

## C. Game with a Fraction

时间限制：每个测试点2秒  
内存限制：每个测试点256兆字节

## 问题描述

Alice 和 Bob 有两个整数 $p$ 和 $q$，他们正在用这些数字玩游戏。玩家轮流进行操作，Alice 先手。轮到一名玩家时，他可以选择以下两种操作之一：

- 将 $p$ 减一（当 $p > 0$ 时可以执行此操作）；
- 将 $q$ 减一（当 $q > 1$ 时可以执行此操作）。

游戏在 $p = 0$ 且 $q = 1$ 时结束。

如果在游戏过程中的任意时刻分数 $\frac{p}{q}$ 的值恰好等于 $\frac{2}{3}$，则 Bob 获胜。否则，Alice 获胜。

给定 $p$ 和 $q$ 的初始值，在双方都采取最优策略的情况下，判断游戏的获胜者。

## 输入格式

每个测试包含多个测试用例。第一行包含一个整数 $t$，表示测试用例的数量（$1 \le t \le 10^4$）。  
接下来是每个测试用例的描述。

每个测试用例为单独一行，包含两个整数 $p$ 和 $q$（$1 \le p, q \le 10^{18}$）。

## 输出格式

对于每个测试用例，输出：

- 如果 Alice 获胜，输出 `"Alice"`；
- 如果 Bob 获胜，输出 `"Bob"`。

## 样例输入

```
6
4 6
10 14
15 15
7 12
7000000000000000 10487275715782582
1000000000000000000 1000000000000000000
```

## 样例输出

```
Bob
Bob
Alice
Alice
Bob
Alice
```

## 样例说明

在第一个测试用例中，分数 $\frac{4}{6}$ 的值已经等于 $\frac{2}{3}$，因此 Bob 获胜。

在第二个测试用例中，一种可能的游戏过程如下：

- 初始：$p = 10, q = 14$；
- Alice 操作后：$p = 9, q = 14$；
- Bob 操作后：$p = 9, q = 13$；
- Alice 操作后：$p = 9, q = 12$；
- Bob 操作后：$p = 8, q = 12$。

Bob 获胜，因为 $\frac{8}{12} = \frac{2}{3}$。可以证明，在此示例中，双方均采取最优策略时，Bob 总是获胜。

在第三个测试用例中，Alice 的最优策略是尽可能多地减少 $q$。在这种情况下，无论 Bob 如何行动，游戏都将以 Alice 获胜告终。


# 我现在感觉做博弈题目，就是先找到一种很占优势的走法，然后看看劣势那一方什么时候可以赢，以此类推


# 错误代码
```cpp
// Problem: CF 2197 C
// Contest: Codeforces - Codeforces Round 1079 (Div. 2)
// URL: https://codeforces.com/contest/2197/problem/C
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long

void solve(){
	int a,b;
	cin>>a>>b;
	int flag=0;
	
	int ta=a/2,tb=b/3;
	int t=min(ta,tb);
	int rea=a-t*2,reb=b-t*3;
	if(rea==reb) flag=1;
	//if(a==b) flag=1;
	
	if(flag) cout<<"Bob"<<endl;
	else cout<<"Alice"<<endl;
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


# 有点那个意思，但是不够
```cpp
// Problem: CF 2197 C
// Contest: Codeforces - Codeforces Round 1079 (Div. 2)
// URL: https://codeforces.com/contest/2197/problem/C
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long

void solve(){
	int a,b;
	cin>>a>>b;
	int cha=abs(a-b);
	if(cha*2>a||cha*3>b||cha==0) cout<<"Alice"<<endl;
	else cout<<"Bob"<<endl;

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




# AC  代码
```cpp
// Problem: CF 2197 C
// Contest: Codeforces - Codeforces Round 1079 (Div. 2)
// URL: https://codeforces.com/contest/2197/problem/C
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long

void solve(){
	int a,b;
	cin>>a>>b;
	
	if(a<b&&a*3>=2*b) cout<<"Bob"<<endl;
	else cout<<"Alice"<<endl;

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


# 公式推导
这个逻辑的核心在于将博弈问题转化为了一个**数学不等式问题**。

为了让你完全理解“为什么”，我们需要拆解 Bob 获胜的两个关键要素：**“目标点”** 和 **“控制策略”**。

### 1. Bob 的目标是什么？

题目说 Bob 获胜的条件是 $\frac{p}{q} = \frac{2}{3}$。

这等价于 $3p = 2q$。

因为 2 和 3 互质，这意味着 $p$ 必须是 2 的倍数，$q$ 必须是 3 的倍数。

所以，Bob 的所有潜在获胜点（目标点）都可以写成：

$$(p, q) = (2k, 3k)$$

其中 $k$ 是某个正整数（比如 $k=1$ 时是 $(2,3)$， $k=5$ 时是 $(10,15)$）。

### 2. 核心变量：$q - p$ （差值）

让我们看看 $q - p$ 这个差值在目标点 $(2k, 3k)$ 是多少：

$$\text{差值} = 3k - 2k = k$$

**这是一个极其重要的发现：**

如果你想在点 $(2k, 3k)$ 获胜，那么那个点的 $q$ 和 $p$ 的差值必须正好是 $k$。

### 3. Bob 的必胜策略（镜像策略）

游戏开始时，设初始值为 $P_{start}$ 和 $Q_{start}$，初始差值为 $K = Q_{start} - P_{start}$。

如果 Bob 想要获胜，他唯一能确定的目标点就是由这个初始差值 $K$ 决定的点：**$(2K, 3K)$**。

**为什么？因为 Bob 可以强行锁住这个差值 $K$ 不变。**

- **Alice 的回合**：
    
    - 如果 Alice 把 $p$ 减 1，差值会变大 1。
        
    - 如果 Alice 把 $q$ 减 1，差值会变小 1。
        
- **Bob 的回合（应对）**：
    
    - 如果 Alice 刚才把 $p$ 减了 1（差值变大了），Bob 就把 $q$ 减 1（差值变回来）。
        
    - 如果 Alice 刚才把 $q$ 减了 1（差值变小了），Bob 就把 $p$ 减 1（差值变回来）。
        

通过这种“你动我就跟着动”的镜像策略，Bob 可以强行让每一轮（Alice动完+Bob动完）结束后，**$q - p$ 的差值维持在 $K$ 不变**。

只要一直维持这个差值，数字又在不断变小，最终一定会落入那个特定的目标点 $(2k, 3k)$。

### 4. 推导获胜条件

基于上面的策略，Bob 想要赢，必须满足两个硬性条件：

#### 条件一：目标必须存在（$q > p$）

目标点 $(2k, 3k)$ 要求 $k$ 是正整数。

如果初始 $q \le p$，那么差值 $k \le 0$，目标点就不存在或者不合法（比如 $p=0, q=0$）。

**所以必须满足：$q > p$**。

#### 条件二：目标必须在“下方”（还没错过）

游戏只能减小数字，不能增大。所以初始的 $p$ 必须大于等于目标点的 $p$。

目标点的 $p$ 是 $2k$。

所以必须满足：$p \ge 2k$。

我们将 $k = q - p$ 代入这个不等式：

$$p \ge 2(q - p)$$

$$p \ge 2q - 2p$$

$$3p \ge 2q$$

或者写成分数形式：$\frac{p}{q} \ge \frac{2}{3}$。

**解释：** 如果 $3p < 2q$（即 $\frac{p}{q} < \frac{2}{3}$），说明虽然差值是 $k$，但当前的 $p$ 已经比目标点 $(2k, 3k)$ 的 $p$ 还要小了。既然已经错过了目标点，又不能往回走，Bob 就永远到不了了。

---

### 总结举例

假设输入是 `10 14`。

1. **计算差值 $k$**：$14 - 10 = 4$。
    
2. **确定目标点**：$(2\times4, 3\times4) = (8, 12)$。
    
3. **判断能否到达**：
    
    - 当前 $p=10$，目标 $p=8$。
        
    - 因为 $10 \ge 8$，说明还没错过。
        
    - Bob 只要死守“维持差值为4”的策略，不管 Alice 怎么走，最终一定会经过 $(8, 12)$。
        
    - **Bob 必胜。**
        

假设输入是 `4 6`。

1. **差值 $k$**：$6 - 4 = 2$。
    
2. **目标点**：$(4, 6)$。
    
3. **判断**：当前就是目标点，Bob 直接赢。
    

假设输入是 `7 12`。

1. **差值 $k$**：$12 - 7 = 5$。
    
2. **目标点**：$(10, 15)$。
    
3. **判断**：当前 $p=7$，目标 $p=10$。
    
4. **结论**：已经比目标点小了（错过了），永远无法到达 $(10, 15)$。Alice 必胜。
    

### 代码逻辑对应

这就是为什么代码只需要一行判断：

C++

```
if (q > p && 3 * p >= 2 * q)
```

- `q > p`：保证 $k$ 是正数，目标存在。
    
- `3 * p >= 2 * q`：保证还没错过目标点（即 $p \ge 2k$）。