


## C. Spring

时间限制：每个测试点2秒
内存限制：每个测试点512兆字节

## 问题描述

爱丽丝、鲍勃和卡罗尔去泉水取水。爱丽丝每 $a$ 天（在第 $a,2a,3a,\dots$ 天）访问一次，鲍勃每 $b$ 天（在第 $b,2b,3b,\dots$ 天）访问一次，卡罗尔每 $c$ 天（在第 $c,2c,3c,\dots$ 天）访问一次。

当只有一个人来访时，他们会收集 $6$ 升水。如果有多人来访，水将平分：两人每人取 $3$ 升，三人每人取 $2$ 升。

你的任务是计算从第 $1$ 天开始，爱丽丝、鲍勃和卡罗尔在 $m$ 天内收集了多少水。

## 输入格式

第一行包含一个整数 $t$ ($1 \le t \le 10^4$) — 测试用例的数量。

每个测试用例只有一行，包含四个整数 $a, b, c$ 和 $m$ ($1 \le a,b,c \le 10^6$; $1 \le m \le 10^{17}$)。

## 输出格式

对于每个测试用例，打印三个整数 — 爱丽丝、鲍勃和卡罗尔在 $m$ 天内收集的水升数。

## 样例输入

```
4
2 1 3 10
1 1 8 5
6 20 15 1000
650650 1092 157437 100000000000000000
```

## 样例输出

```
14 38 8 
15 15 0 
881 236 281 
845294870595 549337065358857 3774389867286 
```

## 样例说明

考虑第一个示例：

- 第 $1$ 天，只有鲍勃访问泉水，他收集了 $6$ 升水；
- 第 $2$ 天，爱丽丝和鲍勃访问泉水，他们每人收集了 $3$ 升水；
- 第 $3$ 天，鲍勃和卡罗尔访问泉水，他们每人收集了 $3$ 升水；
- 第 $4$ 天，爱丽丝和鲍勃访问泉水，他们每人收集了 $3$ 升水；
- 第 $5$ 天，只有鲍勃访问泉水，他收集了 $6$ 升水；
- 第 $6$ 天，爱丽丝、鲍勃和卡罗尔访问泉水，他们每人收集了 $2$ 升水；
- 第 $7$ 天，只有鲍勃访问泉水，他收集了 $6$ 升水；
- 第 $8$ 天，爱丽丝和鲍勃访问泉水，他们每人收集了 $3$ 升水；
- 第 $9$ 天，鲍勃和卡罗尔访问泉水，他们每人收集了 $3$ 升水；
- 第 $10$ 天，爱丽丝和鲍勃访问泉水，他们每人收集了 $3$ 升水。



# 超时代码
```cpp
// Problem: CF 2204 C
// Contest: Codeforces - Educational Codeforces Round 188 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2204/problem/C
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long 

vector<int> fun(int a,int b,int c,int m){
	vector<int> ans(5,0);
	for(int i=1;i<=m;i++){
		int cnt=0;
		if(i%a==0) cnt++;
		if(i%b==0) cnt++;
		if(i%c==0) cnt++;
		
		if(cnt==0){
			continue;
		}else if(cnt==1){
			if(i%a==0) ans[1]+=6;
			if(i%b==0) ans[2]+=6;
			if(i%c==0) ans[3]+=6;
		}else if(cnt==2){
			if(i%a==0&&i%b==0) ans[1]+=3,ans[2]+=3;
			if(i%a==0&&i%c==0) ans[1]+=3,ans[3]+=3;
			if(i%c==0&&i%b==0) ans[3]+=3,ans[2]+=3;
		}else if(cnt==3){
			ans[1]+=2;
			ans[2]+=2;
			ans[3]+=2;
		}
	}
	return ans;
}

void solve(){
	int a,b,c,m;
	cin>>a>>b>>c>>m;
	int l=lcm(a,b);
	l=lcm(l,c);
	int r=m%l;
	//cout<<l<<" -- "<<r<<endl;
	
	vector<int> ans(5,0);
	auto t=fun(a,b,c,l);
	ans[1]=t[1]*(m/l);
	ans[2]=t[2]*(m/l);
	ans[3]=t[3]*(m/l);
	auto tt=fun(a,b,c,r);
	ans[1]+=tt[1];
	ans[2]+=tt[2];
	ans[3]+=tt[3];
	
	cout<<ans[1]<<" "<<ans[2]<<" "<<ans[3]<<endl;
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






# AC 代码
```cpp
// Problem: CF 2204 C
// Contest: Codeforces - Educational Codeforces Round 188 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2204/problem/C
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long 


void solve(){
	int a,b,c,m;
	cin>>a>>b>>c>>m;
	int l=lcm((int)a,(int)b);
	l=lcm((int)l,(int)c);

	int da=m/a,db=m/b,dc=m/c;
	int dab=m/lcm(a,b),dac=m/lcm(a,c),dbc=m/lcm(b,c);
	int dabc=m/l;
	
	int ansa=da*6-3*dab-3*dac+2*dabc;
	int ansb=db*6-3*dab-3*dbc+2*dabc;
	int ansc=dc*6-3*dac-3*dbc+2*dabc;
	
	cout<<ansa<<" "<<ansb<<" "<<ansc<<endl;
	
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







既然我们刚刚聊到了**容斥原理**和**最小公倍数（LCM）**，我就为你量身定制一份针对**数论与组合数学基础**的算法笔记吧！这类知识点在算法竞赛（如 Codeforces、蓝桥杯、LeetCode 困难题）中非常常见。

---

## 算法笔记：数论基础与容斥原理

### 1. 最大公约数 (GCD) 与最小公倍数 (LCM)

这是处理周期性问题、整除问题时的核心工具。

**核心性质与公式：**

- **最大公约数 (GCD)：** 通常使用“辗转相除法”（欧几里得算法）求解。时间复杂度为 $O(\log(\min(a, b)))$。
    
- **最小公倍数 (LCM)：** 两个数的乘积等于它们最大公约数与最小公倍数的乘积。
    
    推导公式：$\text{lcm}(a, b) = \frac{a \times b}{\gcd(a, b)}$
    

**C++ 实用技巧：**

从 C++17 开始，标准库 `<numeric>` 中直接提供了求 GCD 和 LCM 的函数，极其方便：

C++

```
#include <numeric>
#include <iostream>

using namespace std;

int main() {
    long long a = 12, b = 18;
    // C++17 引入的内置函数
    cout << std::gcd(a, b) << endl; // 输出: 6
    cout << std::lcm(a, b) << endl; // 输出: 36
    return 0;
}
```

> **避坑指南（防溢出）：** 如果不能使用 C++17，手写 LCM 时，**一定要先除后乘**：`a / gcd(a, b) * b`。如果直接写 `a * b / gcd(a, b)`，当 `a` 和 `b` 很大时，`a * b` 极易超出整型（甚至是 `long long`）的上限。

---

### 2. 容斥原理 (Inclusion-Exclusion Principle)

容斥原理是用来计算多个集合并集大小的数学方法。它的核心口诀是：**“奇加偶减”**（奇数个集合的交集加上，偶数个集合的交集减去）。

**经典公式：**

- **两个集合：** $|A \cup B| = |A| + |B| - |A \cap B|$
    
- **三个集合（正如我们上一题遇到的情况）：** $|A \cup B \cup C| = |A| + |B| + |C| - |A \cap B| - |A \cap C| - |B \cap C| + |A \cap B \cap C|$
    

**常见应用场景：**

1. **区间内整除问题：** 求 $1$ 到 $N$ 中，能被 $a$、$b$ 或 $c$ 整除的数字有多少个。
    
2. **分配问题（带限制条件）：** 计算满足某些条件的组合数时，先算出总数，再减去不满足条件的（利用容斥）。
    
3. **计算重叠面积/天数：** 就像上一题中，计算多个人同时去打水的利益分配。
    

---

### 3. 经典手写代码模板 (C++)

如果你遇到较老的编译器环境（不支持 C++17），可以直接套用以下模板：

C++

```
// 1. 求最大公约数 (辗转相除法)
long long gcd(long long a, long long b) {
    return b == 0 ? a : gcd(b, a % b);
}

// 2. 求最小公倍数 (先除后乘防溢出)
long long lcm(long long a, long long b) {
    if (a == 0 || b == 0) return 0;
    return (a / gcd(a, b)) * b;
}

// 3. 求三个数的最小公倍数
long long lcm3(long long a, long long b, long long c) {
    return lcm(lcm(a, b), c);
}
```

---

这份笔记涵盖了刚刚那道题目的底层核心逻辑。你想了解其他方向的算法笔记吗？（例如：二分查找模板、动态规划入门、或者图论的最短路算法），我可以继续为你提炼！