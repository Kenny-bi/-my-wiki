


## D. Christmas Tree Decoration
时间限制：每测试点 2 秒  
内存限制：每测试点 512 MB

## 问题描述
一群 $n$ 个人决定装饰圣诞树。他们有 $(n+1)$ 盒装饰品，编号从 $0$ 到 $n$。最初，第 $i$ 个盒子包含 $a_i$ 个装饰品。

我们称一个大小为 $n$ 的排列 $p$（一个大小为 $n$ 的数组，其中 $1$ 到 $n$ 的每个数字恰好出现一次）是**公平的**，如果能够通过以下过程将所有的装饰品挂到树上：
*   人 $p_1$ 从盒子 $0$ 或盒子 $p_1$ 中取一个装饰品，并将其挂在树上；
*   人 $p_2$ 从盒子 $0$ 或盒子 $p_2$ 中取一个装饰品，并将其挂在树上；
*   以此类推；
*   在 $p_n$ 之后，轮到 $p_1$，这个过程重复进行，直到所有装饰品都被挂在树上。

在这个过程中，永远不应该出现这样一种情况：人 $i$ 需要挂一个装饰品，但盒子 $0$ 和盒子 $i$ 都是空的。如果这种情况无法避免，则该排列不是公平的。然而，如果人们可以在每一步中选择从哪个盒子取装饰品，使得这种情况不会发生，那么该排列就是公平的。

你的任务是计算公平排列的数量。由于答案可能很大，请输出其对 $998244353$ 取模的结果。

## 输入格式
第一行包含一个整数 $t$（$1 \le t \le 5000$）——测试用例的数量。

每个测试用例的第一行包含一个整数 $n$（$1 \le n \le 50$）。

第二行包含 $(n+1)$ 个整数 $a_0, a_1, \dots, a_n$（$0 \le a_i \le 10^6$）。

## 输出格式
对于每个测试用例，输出一个整数——公平排列的数量，对 $998244353$ 取模。

## 样例输入
```
4
3
1 2 1 0
3
1 0 2 0
1
2 5
4
6 1 4 2 1
```

## 样例输出
```
2
0
1
12
```

## 样例说明
在第一个样例中，公平排列是 $[1,2,3]$ 和 $[1,3,2]$。

让我们仔细看一下排列 $[1,3,2]$ 的圣诞树装饰过程：
*   人 $p_1=1$ 从盒子 $1$ 中挂了一个装饰品；
*   人 $p_2=3$ 从盒子 $0$ 中挂了一个装饰品；
*   人 $p_3=2$ 从盒子 $2$ 中挂了一个装饰品；
*   人 $p_1=1$ 从盒子 $1$ 中挂了一个装饰品。

请注意，如果人 $p_1=1$ 在第一步中从盒子 $0$ 中取装饰品，那么人 $p_2=3$ 将无法执行下一步（因为盒子 $0$ 和 $3$ 都将是空的）。然而，由于这种情况可以避免，因此该排列是公平的。





# 改着改着突然过样例了，激动的我找不到错了
```cpp
// Problem: CF 2182 D
// Contest: Codeforces - Educational Codeforces Round 186 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2182/problem/D
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long
const int mod=998244353;
int fac[60],C[60][60];

void solve(){
	int n;
	cin>>n;
	vector<int> a(n+2);
	int sum=0,mx=0;
	for(int i=0;i<=n;i++){
		cin>>a[i];
		sum+=a[i];
		if(i) mx=max(mx,a[i]);
	}
	
	int mxc=0;
	for(int i=1;i<=n;i++){
		if(a[i]==mx) mxc++;
	}
	
	if(mx*n<=sum){
		int k=sum%n;
		cout<<((C[n][k]*fac[k])%mod*fac[n-k])%mod<<endl;
		return ;
	}
	
	if(sum>=(mx-1)*n){
		int k=sum%n;
		int kk=k-mxc;
		cout<<((C[n-mxc][kk]*fac[k])%mod*fac[n-k])%mod<<endl;
		return ;
	}
	cout<<0<<endl;
	 	
	// if(sum-mxc*mx>=(mx-1)*(n-mxc)){
		// cout<<fac[mxc]*fac[n-mxc]%mod<<endl;
		// return ;
	// }
	// cout<<endl;
	return ;
}


signed main(){
	fac[0]=1,fac[1]=1;
	for(int i=2;i<=55;i++){
		fac[i]=(fac[i-1]*i)%mod;
	}
	for(int i=0;i<55;i++){
		C[i][0]=1;
		for(int j=1;j<=i;j++){
			C[i][j]=(C[i-1][j]+C[i-1][j-1])%mod;
		}
	}
	
	int t;
	cin>>t;
	while(t--){
		solve();
	}
	return 0;
}
```





# 成功AC
- 果然是又踩了一遍前人走过的坑，有可能是负的
```cpp
// Problem: CF 2182 D
// Contest: Codeforces - Educational Codeforces Round 186 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2182/problem/D
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long
const int mod=998244353;
int fac[60],C[60][60];

void solve(){
	int n;
	cin>>n;
	vector<int> a(n+2);
	int sum=0,mx=0;
	for(int i=0;i<=n;i++){
		cin>>a[i];
		sum+=a[i];
		if(i) mx=max(mx,a[i]);
	}
	
	int mxc=0;
	for(int i=1;i<=n;i++){
		if(a[i]==mx) mxc++;
	}
	
	if(mx*n<=sum){
		int k=sum%n;
		cout<<((C[n][k]*fac[k])%mod*fac[n-k])%mod<<endl;
		return ;
	}
	
	if(sum>=(mx-1)*n){
		int k=sum%n;
		int kk=k-mxc;
		if(k<mxc){
			cout<<0<<endl;      //新加的
			return ;
		}
		cout<<((C[n-mxc][kk]*fac[k])%mod*fac[n-k])%mod<<endl;
		return ;
	}
	cout<<0<<endl;
	 	
	// if(sum-mxc*mx>=(mx-1)*(n-mxc)){
		// cout<<fac[mxc]*fac[n-mxc]%mod<<endl;
		// return ;
	// }
	// cout<<endl;
	return ;
}


signed main(){
	fac[0]=1,fac[1]=1;
	for(int i=2;i<=55;i++){
		fac[i]=(fac[i-1]*i)%mod;
	}
	for(int i=0;i<55;i++){
		C[i][0]=1;
		for(int j=1;j<=i;j++){
			C[i][j]=(C[i-1][j]+C[i-1][j-1])%mod;
		}
	}
	
	int t;
	cin>>t;
	while(t--){
		solve();
	}
	return 0;
}
```





# 总结

### 1. 核心思维：从“模拟”到“抽屉原理”

题目表象：

看似是一个复杂的动态过程（轮流取物、盒子是否为空、能否继续）。如果尝试直接模拟这个过程，状态空间太大，根本无从下手。

数学本质（Model Abstraction）：

这道题的本质是资源分配（Resource Allocation）。

1. **总资源（回合数）：** 总共有 $S = \sum a_i$ 个装饰品，意味着总共要进行 $S$ 个回合。
    
2. **分配规则：** 这 $S$ 个回合被分摊到 $n$ 个人身上。由于是循环轮流：
    
    - 前 $k = S \% n$ 个人，每人分到 $q + 1$ 个回合。
        
    - 剩下的 $n - k$ 个人，每人分到 $q$ 个回合。
        
    - 其中 $q = \lfloor S / n \rfloor$。
        
3. 核心约束（Capacity Constraint）：
    
    题目中复杂的“盒子为空”规则，归根结底只有一条限制：一个人能挂出的装饰品数量，绝对不可能超过他轮到的回合数。
    
    即：$a_i \le \text{Turns}_i$。
    
    - 如果 $a_i > \text{Turns}_i$，无论怎么借用公共盒子 $a_0$，他自己的盒子 $a_i$ 永远无法被清空，游戏必然失败。
        
    - 如果 $a_i \le \text{Turns}_i$，只要公共盒子 $a_0$ 足够（题目保证总数匹配），他一定能清空自己的盒子。
        

> [!TIP]
> 
> 经验 1：不要被题目复杂的“操作过程”迷惑。 > 很多博弈或构造题，最终都会收敛到一个简单的不等式约束。问自己：“什么情况下绝对不可能完成？”通常就是突破口。

---

### 2. 坑点复盘：边界检查与负数索引

你遇到的 `if(k < mxc)` 缺失问题，是组合数学题目中极易踩的坑。

**情景还原：**

- **需求**：有 `mxc` 个人手里拿着 $q+1$ 个物品，他们**必须**被安排在能提供 $q+1$ 个回合的位置上。
    
- **供给**：整个排列中，只有 $k$ 个位置能提供 $q+1$ 个回合。
    
- **Bug 逻辑**：你计算剩余位置时用了 `kk = k - mxc`。如果不检查，当需求大于供给（`mxc > k`）时，`kk` 变成负数。
    
- **后果**：
    
    1. 逻辑上：这是无解的情况，应输出 0。
        
    2. 代码上：`C[n-mxc][kk]` 变成了访问负下标，导致越界读取脏数据或程序崩溃。
        

> [!DANGER]
> 
> 经验 2：做减法前必判断！
> 
> 在组合数学或背包问题中，任何形式的 剩余资源 = 总资源 - 占用资源，在写代码时必须无条件加上 if (总资源 < 占用资源) return 0;。不要指望后续的排列组合公式能自动处理负数。

---

### 3. 代码与逻辑的简化

你的代码逻辑已经很清晰了，但其实可以从“分组”的角度更直观地理解代码构造，从而减少写错的概率。

问题重述：

我们要构造一个长度为 $n$ 的排列，其中有 $k$ 个“长回合位”（$q+1$）和 $n-k$ 个“短回合位”（$q$）。

**元素分类：**

1. **必须去长回合的人**（$a_i = q+1$）：有 `mxc` 人。
    
2. **去哪都行的人**（$a_i \le q$）：有 $n - mxc$ 人。
    

**填坑步骤：**

1. **先安排“大爷”：** `mxc` 个大爷必须坐进 `k` 个长回合位。
    
    - 选座方案：$C(k, mxc)$ （从 k 个长位里选 mxc 个给他们）。
        
    - 排座方案：$mxc!$ （大爷们之间互换位置）。
        
2. **再安排“平民”：** 剩下的 $n - mxc$ 个人填满剩下的所有位置（剩下的 $k-mxc$ 个长位 + $n-k$ 个短位）。
    
    - 因为剩下的人对位置没要求，直接全排列即可：$(n - mxc)!$。
        

最终公式：

$$\text{Ans} = C(k, mxc) \times mxc! \times (n - mxc)!$$

或者等价于你的公式（在数学上是一样的，但这个思路更顺畅）。

---

### 4. 总结：这道题你学到了什么？

1. **模型转化能力**：将“循环操作”转化为“余数分组”模型 ($S = n \times q + r$)。
    
2. 抽屉原理的应用：
    
    把 $n$ 个人（物品）放入 $n$ 个位置（抽屉），高需求的物品必须放入高容量的抽屉。
    
3. **防御性编程**：在处理计数问题时，对于“需求 > 供给”的非法情况，要显式判断并返回 0，而不是依赖公式的自然计算。
    

下一次遇到这种“排列构造”+“数量限制”的题目，你可以直接按这个模版思考：

1. 算出每个位置的**容量**。
    
2. 算出每个元素的**需求**。
    
3. 看是否能通过排列，让所有 **需求 $\le$ 容量** 成立。
    
4. 计算排列数时，注意**需求 > 容量**的非法情况。
    

下一步建议：

如果你想巩固这种思维，可以找一些 Codeforces Constructive Algorithms 或 Combinatorics 标签且难度在 1600-1800 左右的题目练习。