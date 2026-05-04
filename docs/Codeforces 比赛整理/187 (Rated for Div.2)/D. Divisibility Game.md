



## D. Divisibility Game

时间限制：每个测试2秒
内存限制：每个测试512 MB

## 问题描述

爱丽丝和鲍勃正在玩游戏。他们有一个包含 $n$ 个元素的数组 $a$ 和一个包含 $m$ 个元素的数组 $b$。

玩家轮流行动。爱丽丝先手。在每个玩家的回合中，他们从数组 $a$ 中选择一个数字 $x$，并从数组 $b$ 中选择一个数字 $y$。爱丽丝选择 $x$ 和 $y$ 有自己的规则，鲍勃也有自己的规则：

- 爱丽丝必须选择 $x$ 和 $y$，使得 $y$ 能被 $x$ 整除。
- 鲍勃必须选择 $x$ 和 $y$，使得 $y$ **不能** 被 $x$ 整除。

选择 $x$ 和 $y$ 之后，$y$ 会从数组 $b$ 中移除（但 $x$ 保留在 $a$ 中）。当 $y$ 从 $b$ 中被移除时，如果 $y$ 出现了多次，则只移除一个。无法行动的玩家输掉游戏。

如果双方都采取最优策略，谁会获胜？

## 输入格式

每个测试包含多个测试用例。第一行包含测试用例的数量 $t$ ($1 \leq t \leq 10^4$)。接下来是测试用例的描述。

每个测试用例的第一行包含两个整数 $n$ 和 $m$ ($1 \leq n, m \leq 10^6$)。

每个测试用例的第二行包含 $n$ 个整数 $a_i$ ($1 \leq a_i \leq n+m$) — 数组 $a$ 的元素。

每个测试用例的最后一行包含 $m$ 个整数 $b_i$ ($1 \leq b_i \leq n+m$) — 数组 $b$ 的元素。

输入的附加约束：

- 所有测试用例的 $n$ 之和不超过 $10^6$；
- 所有测试用例的 $m$ 之和不超过 $10^6$。

## 输出格式

对于每个测试用例，输出一个词：

- 如果爱丽丝获胜，输出 `Alice`；
- 如果鲍勃获胜，输出 `Bob`。

## 样例输入

```
3
9 3
3 2 4 2 2 4 4 2 4
6 7 12
10 3
3 2 5 4 2 5 3 4 4 4
10 7 13
1 5
1
1 2 3 4 5
```

## 样例输出

```
Alice
Bob
Alice
```

## 样例说明

考虑第一个测试用例。让我们展示爱丽丝如何通过以下步骤获胜：

1. 爱丽丝的回合：$x=3$, $y=6$ （之后，$6$ 将从 $b$ 中移除，得到 $b=[7,12]$）
2. 鲍勃的回合：$x=3$, $y=7$ （鲍勃无论如何都会在他的回合选择 $y=7$，因为在这个回合之后 $b=[12]$，不存在不能整除 $y=12$ 的 $x$）
3. 爱丽丝的回合：$x=4$, $y=12$ （此回合后，$b$ 变为空）
4. 鲍勃的回合：鲍勃输了，因为他无法行动



# TLE   代码
```Cpp
// Problem: CF 2203 D
// Contest: Codeforces - Educational Codeforces Round 187 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2203/problem/D
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define ios ios::sync_with_stdio(false);cin.tie(nullptr);

void solve(){
	int n,m;
	cin>>n>>m;
	vector<int> a(n),b(m);
	for(int i=0;i<n;i++){
		cin>>a[i];
	}
	for(int i=0;i<m;i++){
		cin>>b[i];
	}
	
	sort(a.begin(),a.end());
	a.erase(unique(a.begin(),a.end()),a.end());
	
	int same=0,cnta=0,cntb=0;
	for(int i=0;i<m;i++){
		int flaga=0,flagb=0;
		for(int j=0;j<a.size();j++){
			if(b[i]%a[j]==0) flaga=1;
			else flagb=1;
			if(flaga&&flagb) break;
		}
		
		if(flaga&&flagb) same++;
		else{
			if(flaga) cnta++;
			if(flagb) cntb++;
		}
	}
	
	if(same&1){
		if(cnta>=cntb) cout<<"Alice"<<endl;
		else cout<<"Bob"<<endl;
	}else{
		if(cntb>=cnta) cout<<"Bob"<<endl;
		else cout<<"Alice"<<endl;
	}
	
	return ;
}

int main(){
	//ios;
	int t;
	cin>>t;
	while(t--){
		solve();
	}
	return 0;
}
```





# TLE 9  代码
```cpp
// Problem: CF 2203 D
// Contest: Codeforces - Educational Codeforces Round 187 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2203/problem/D
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define ios ios::sync_with_stdio(false);cin.tie(nullptr);

void solve(){
	int n,m;
	cin>>n>>m;
	vector<int> a(n),b(m);
	for(int i=0;i<n;i++){
		cin>>a[i];
	}
	for(int i=0;i<m;i++){
		cin>>b[i];
	}
	
	sort(a.begin(),a.end());
	a.erase(unique(a.begin(),a.end()),a.end());
	
	int same=0,cnta=0,cntb=0;
	
	int maxv=n+m,lcma=1;
	vector<int> st(maxv+3,0);
	for(int i=0;i<a.size();i++){
		lcma=lcm(lcma,a[i]);
		if(lcma>maxv){
			lcma=maxv+1;
			break;
		}
	}
	
	for(int i=0;i<a.size();i++){
		if(st[a[i]]) continue;
		for(int j=a[i];j<=maxv;j+=a[i]){
			st[j]=1;
		}
	}
	
	for(int i=0;i<m;i++){
		int divall=(b[i]%lcma==0);
		int divany=st[b[i]];
		if(divall) cnta++;
		else if(!divany) cntb++;
		else same++;
	}
	
	
// 	
	// for(int i=0;i<m;i++){
		// int flaga=0,flagb=0;
		// for(int j=0;j<a.size();j++){
			// if(b[i]%a[j]==0) flaga=1;
			// else flagb=1;
			// if(flaga&&flagb) break;
		// }
// 		
		// if(flaga&&flagb) same++;
		// else{
			// if(flaga) cnta++;
			// if(flagb) cntb++;
		// }
	// }
	
	if(same&1){
		if(cnta>=cntb) cout<<"Alice"<<endl;
		else cout<<"Bob"<<endl;
	}else{
		if(cntb>=cnta) cout<<"Bob"<<endl;
		else cout<<"Alice"<<endl;
	}
	
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




# 搞了半天 T9 原来是没有开流加速
```cpp
// Problem: CF 2203 D
// Contest: Codeforces - Educational Codeforces Round 187 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2203/problem/D
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define ios ios::sync_with_stdio(false);cin.tie(nullptr);

void solve(){
	int n,m;
	cin>>n>>m;
	vector<int> a(n),b(m);
	for(int i=0;i<n;i++){
		cin>>a[i];
	}
	for(int i=0;i<m;i++){
		cin>>b[i];
	}
	
	sort(a.begin(),a.end());
	a.erase(unique(a.begin(),a.end()),a.end());
	
	int same=0,cnta=0,cntb=0;
	
	int maxv=n+m,lcma=1;
	vector<int> st(maxv+3,0);
	for(int i=0;i<a.size();i++){
		lcma=lcm(lcma,a[i]);
		if(lcma>maxv){
			lcma=maxv+1;
			break;
		}
	}
	
	for(int i=0;i<a.size();i++){
		if(st[a[i]]) continue;
		for(int j=a[i];j<=maxv;j+=a[i]){
			st[j]=1;
		}
	}
	
	for(int i=0;i<m;i++){
		int divall=(b[i]%lcma==0);
		int divany=st[b[i]];
		if(divall) cnta++;
		else if(!divany) cntb++;
		else same++;
	}
	
	
// 	
	// for(int i=0;i<m;i++){
		// int flaga=0,flagb=0;
		// for(int j=0;j<a.size();j++){
			// if(b[i]%a[j]==0) flaga=1;
			// else flagb=1;
			// if(flaga&&flagb) break;
		// }
// 		
		// if(flaga&&flagb) same++;
		// else{
			// if(flaga) cnta++;
			// if(flagb) cntb++;
		// }
	// }
	
	if(same&1){
		if(cnta>=cntb) cout<<"Alice"<<endl;
		else cout<<"Bob"<<endl;
	}else{
		if(cntb>=cnta) cout<<"Bob"<<endl;
		else cout<<"Alice"<<endl;
	}
	
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





这里为你专门整理一份 **CF 2203 D (Divisibility Game)** 的算法总结。这道题是一道非常经典的**“博弈论转化 + 数论降维打击”**的好题，你的思路非常准，这篇总结将把你的直觉沉淀为以后可以复用的解题套路。

---

# 算法总结：CF 2203 D - Divisibility Game

## 一、 题目本质（问题抽象）

在两个数组 $a$ 和 $b$ 中，Alice 想要 $x \mid y$（能整除），Bob 想要 $x \nmid y$（不能整除）。每次选中的 $y$ 会被拿走，谁先无路可走谁输。

博弈论的精髓在于**对资源进行分类**。我们站在 $y \in b$ 的视角，可以将数组 $b$ 中的数字分为三大类：

1. **Alice 的私有资源 (`cnta`)**：$y$ 能被 $a$ 中的**所有**元素整除。只有 Alice 能拿，Bob 拿不了。
    
2. **Bob 的私有资源 (`cntb`)**：$y$ 不能被 $a$ 中的**任何**元素整除。只有 Bob 能拿，Alice 拿不了。
    
3. **公共共享资源 (`same`)**：$y$ 能被 $a$ 中的**部分**元素整除。双方都能通过选择合适的 $x$ 来拿走它。
    

---

## 二、 博弈论推导（胜负判定）

在绝对理性的博弈中，**“抢夺公共资源”永远是第一优先级**。因为拿走一个公共资源，不仅自己得了一分（续命一回合），还让对方少了一分。

双方轮流消耗 `same`：

- **如果 `same` 是奇数**：Alice 先手抢，最终 Alice 会拿到最后一个公共资源。接下来轮到 Bob 动用私有资源。此时 Alice 已经占了便宜，只要 Alice 的私有资源不比 Bob 少（`cnta >= cntb`），Alice 必胜；否则 Bob 胜。
    
- **如果 `same` 是偶数**：Alice 先手抢，但 Bob 会拿到最后一个公共资源。接下来轮到 Alice 动用私有资源。此时 Bob 占了便宜，只要 Bob 的私有资源不比 Alice 少（`cntb >= cnta`），Bob 必胜；否则 Alice 胜。
    

**结论**：博弈论部分的时间复杂度是 $O(1)$。胜负仅取决于 `same % 2` 以及 `cnta` 和 `cntb` 的大小关系。

---

## 三、 算法核心：如何极速分类？（降维打击）

最直观的分类方法是双重循环（遍历 $b$ 中的每个元素，再去遍历 $a$ 测试整除性），复杂度为 $O(N \times M)$。在 $N, M \le 10^6$ 的数据下必定超时（TLE）。

**破局点**：题目隐藏了一个极强的约束条件——**$a_i, b_i \le n+m$**。

这意味着数值的上限 $V \le 2 \times 10^6$。我们可以利用**值域**的特性，用数论方法预处理，将复杂度降维！

### 1. 极速求 `cnta`：最小公倍数 (LCM)

- **原理**：一个数能被 $a$ 中所有数整除，等价于它是 $a$ 中所有数的**最小公倍数**的倍数。
    
- **操作**：遍历数组 $a$，求出整体的 LCM。如果 $y \bmod \text{LCM} == 0$，它就是 `cnta`。
    
- **致命坑点（防爆护盾）**：LCM 的增长速度呈指数级，极易溢出 `long long` 导致变负数或 0，引发除零错误（RE）。
    
    - _解法_：由于 $y \le n+m$，如果 LCM 在计算过程中**超过了 $n+m$**，说明 $b$ 中绝不可能有它的倍数。此时直接截断（设为一个大于 $n+m$ 的常数并 `break`），完美防溢出！
        

### 2. 极速求 `cntb`：调和级数 / 埃氏筛法

- **原理**：我们需要知道 $y$ 是否是 $a$ 中**任意**元素的倍数。
    
- **操作**：开一个大小为 $n+m+1$ 的布尔数组 `is_mul`。遍历 $a$ 中的每个元素 $x$，将 $x, 2x, 3x, \dots$ 标记为 `true`。
    
- **复杂度**：$\frac{V}{a_1} + \frac{V}{a_2} + \dots \le V \ln V$。这个过程的时间复杂度大约是 $O(V \log V)$，运行速度极快。
    
- 如果 `is_mul[y]` 为 `false`，说明它不是任何人的倍数，它就是 `cntb`。
    

---

## 四、 复杂度与代码模板提取

- **时间复杂度**：
    
    - 去重排序：$O(N \log N)$
        
    - 求 LCM：$O(N)$
        
    - 筛法标记倍数：$O(V \log V)$ （$V = n+m$）
        
    - 遍历判定：$O(M)$
        
    - **总体时间复杂度**：$\approx O(V \log V)$，在 2 秒时限内可谓降维打击。
        
- **空间复杂度**：$O(V)$ 用于开布尔标记数组。
    

### 🌟 实战模板提炼：值域筛法查倍数

以后如果在比赛中遇到 **“判断数组 B 中的数是否是数组 A 中元素的倍数/约数”**，且**最大数值 $V$ 不超过 $10^7$**，直接默写这段模板：

这道题是结合了**博弈论分析 + 数据范围敏感度 + 筛法优化**的综合好题，掌握这个套路，以后遇到类似的值域限制题都能秒杀！