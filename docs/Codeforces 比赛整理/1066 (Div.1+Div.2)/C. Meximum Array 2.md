---
created: 2025-11-24 09:26
tags:
  - algorithm/构造
  - difficulty/待定
  - status/AC
  - site/Codeforces
---



**题目链接**: [](<% tp.file.cursor(1) %>) 
**核心考点**: [[构造]]





## C. Meximum Array 2
时间限制：每个测试 2 秒
内存限制：每个测试 256 MB

## 问题描述
给定三个正整数 $n$, $k$, 和 $q$。同时给出 $q$ 个元组 $(c,l,r)$，其中 $1 \le c \le 2$ 且 $1 \le l \le r \le n$。

一个数组 $a_1,a_2,\dots,a_n$ 被称为 **meximum** 如果对于每个 $i \in [1,n]$ 都有 $0 \le a_i \le 10^9$，并且对于每个给定的元组 $(c,l,r)$：

- 如果 $c=1$，则 $\min(a_l,a_{l+1},\dots,a_r) = k$；
- 如果 $c=2$，则 $\mathrm{MEX}^*(a_l,a_{l+1},\dots,a_r) = k$。

注意参数 $k$ 对所有条件都是相同的。

请找出一个长度为 $n$ 的 meximum 数组 $a_1,a_2,\dots,a_n$。输入保证总是存在有效的数组。如果有多个可能的数组，你可以输出其中任意一个。

$^*$ 整数集合 $a_1,a_2,\dots,a_k$ 的**最小排除值（MEX）**定义为不在该集合中出现的最小非负整数 $x$。

## 输入格式
每个测试包含多个测试用例。第一行包含测试用例的数量 $t$ ($1 \le t \le 500$)。

接下来是每个测试用例的描述：
- 第一行包含三个整数 $n$, $k$, $q$ ($1 \le k \le n \le 100$, $1 \le q \le 100$) — 分别表示数组 $a_1,a_2,\dots,a_n$ 的长度、元组中描述的 $\min$ 和 $\mathrm{MEX}$ 计算结果、以及元组的数量
- 接着 $q$ 行，第 $i$ 行包含一个元组 $(c,l,r)$，表示对 $a_l,a_{l+1},\dots,a_r$ 的约束条件

保证存在与给定输入对应的有效数组。

注意：题目没有对所有测试用例的 $n$, $k$ 或 $q$ 之和设置约束。

## 输出格式
对于每个测试用例，输出一行包含一个 meximum 数组 $a_1,a_2,\dots,a_n$。

## 样例输入
```
4
6 2 2
1 1 3
2 2 6
3 3 1
2 1 3
3 3 2
1 1 1
1 3 3
3 2 2
2 1 2
2 2 3
```

## 样例输出
```
2 5 4 3 0 1
2 0 1
3 3 3
1 0 1
```

## 样例说明
在第一个测试用例中，需要构建一个 $n=6$, $k=2$ 的 meximum 数组，且有 $q=2$ 个约束条件：

- 元组 $(1,1,3)$: $\min(a_1,a_2,a_3)$ 必须为 $2$
- 元组 $(2,2,6)$: $\mathrm{MEX}(a_2,a_3,a_4,a_5,a_6)$ 必须为 $2$

一个满足所有约束条件且元素在 $0 \le a_i \le 10^9$ 范围内的可能数组是 $[2,5,4,3,0,1]$。

在第二个测试用例中，需要构建一个 $n=3$, $k=3$ 的 meximum 数组，且有 $q=1$ 个约束条件：

- 元组 $(2,1,3)$: $\mathrm{MEX}(a_1,a_2,a_3)$ 必须为 $3$

一个满足此约束条件的可能数组是 $[2,0,1]$。














## 💡 核心直觉 (一句话总结)
> 对于一组min   ,那就是这一组里面最小值一定是k而且k一定出现
> 对于一组mex  ,那就是这一组里面一定不能出现k而且所有小于k的数字均出现
> 且题目保证一定有解，那我们可以大胆构造

>正确的思维导向，分析每一个位置的状态


## ✅ 正解思路
- 我的想法是先构造mex,由于题目保证一定有解，所以我构造时候选择最简便的做法，直接下标%k
- 接着就要看最小值了，首先是Min  mex  里的k值一定不冲突，所以我们开一个st  数组，把没填的位置先填一个k   然后没填过而且小于k的位置全部换成K
- 最后对于全局位置扫一遍，没填过的填K,只要   $\geq k$   都行

## 🧠 举一反三 (Pattern)
* 下次看到 **"..."** -> 想到 **...**

---
## 💻 代码
```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long LL;
typedef pair<int,int> PII;
const int N=1e6+7;
const int mod=1e9+7;



void solve(){
	int n,k,q;
	cin>>n>>k>>q;
	vector<int> a(n+3,-1);
	
	vector<PII> op2,op1;
	
	for(int i=0;i<q;i++){
		int op,l,r;
		cin>>op>>l>>r;
		if(op==1) op1.push_back({l,r});
		else op2.push_back({l,r});
	}
	
	vector<int> st(n+3,0);
	for(int i=0;i<op2.size();i++){
		auto [l,r]=op2[i];
		for(int j=l;j<=r;j++){
			a[j]=j%k;
			st[j]=1;
		}
	}
	for(int i=0;i<op1.size();i++){
		auto [l,r]=op1[i];
		for(int j=l;j<=r;j++){
			if(!st[j]){
				a[j]=k;
				break;
			}
		}
		for(int j=l;j<=r;j++){
			if(a[j]==-1||a[j]<k) a[j]=k+3;
		}
	}
	
	for(int i=1;i<=n;i++){
		if(a[i]==-1) a[i]=k+2;
		printf("%d ",a[i]);
	}
	printf("\n");
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



# 从本题我应学到的直觉
这道题是一个非常经典的**构造题 (Constructive Algorithm)**。通过对比你的解法和官方题解，你可以学到以下几个核心思维模型：

### 1. 从“过程模拟”转向“状态分类” (State Classification)

这是本题最大的思维跃迁点。

- **你的思路 (过程导向)**：
    
    - 先处理所有的 MEX 限制（填入 `j%k`）。
        
    - 再处理所有的 MIN 限制（填入 `k`）。
        
    - 这是一个“先做A，再修补B”的逻辑。虽然也能过，但在逻辑复杂时容易出现“拆东墙补西墙”的冲突。
        
- **CF 题解思路 (状态导向)**：
    
    - 它没有按顺序处理限制，而是**预处理**了每个位置 $i$ 的**属性**。
        
    - 对于每个位置 $i$，它只可能有 4 种状态：
        
        1. 只被 MIN 覆盖
            
        2. 只被 MEX 覆盖
            
        3. 被两者同时覆盖
            
        4. 都没覆盖
            
    - **核心洞察**：
        
        - **冲突区 (两者同时覆盖)**：MIN 要求 $\ge k$，MEX 要求 $\neq k$（且需要 $<k$ 的数来凑 MEX）。这意味着这个位置既不能放 $k$，也不能放 $<k$ 的数。**它对于满足限制是“废弃”的**，只能填一个无关紧要的大数（如 $k+1$）。
            
        - **功能区 (只被一种覆盖)**：
            
            - 只被 MIN 覆盖：这是唯一能安全放 $k$ 的地方。
                
            - 只被 MEX 覆盖：这是唯一能安全放 $0 \dots k-1$ 的地方。
                

> 学到的点：
> 
> 在处理多重限制的构造题时，不要急着填数。先画个“韦恩图”，把每个位置根据它受到的限制进行分类（打标记）。一旦分类完成，填什么数通常就是显而易见的了。

### 2. 循环构造的威力 ($i \pmod k$)

在处理 MEX 相关的构造时，**模运算**是神技。

- 题目要求：某个区间 MEX 为 $k$。这意味着区间内必须包含 $0, 1, \dots, k-1$。
    
- 最简单的构造法：让整个数组变成 $0, 1, 2, \dots, k-1, 0, 1, \dots$。
    
- **公式**：$a[i] = i \% k$。
    
- **为什么有效？**：只要区间长度 $\ge k$，或者题目保证有解（意味着即使区间短，也足够容纳必要的数），$i \% k$ 这种均匀分布的填法能最大概率地“自然满足”各种区间对小数字的需求，而不需要你去精细计算每个区间缺哪个数。
    

### 3. 利用“保证有解”逆推策略

题目说 **"It is guaranteed that a valid array always exists."** (保证存在有效数组)。这句话是巨大的提示，意味着你可以采取**最贪心、最极端**的策略。

- 既然保证有解，说明：
    
    - 那些“被两者同时覆盖”的废弃位置，去掉它们后，剩下的位置一定足够填满需要的数。
        
    - 我们不需要做复杂的匹配（比如这个 $k$ 到底给哪个 MIN 区间用），直接**各司其职**即可：
        
        - 是 MIN 的地盘就填 $k$。
            
        - 是 MEX 的地盘就填 $i \% k$。
            
        - 冲突的地方就填垃圾值。
            

### 4. 差分/扫描线思维的雏形 (虽本题数据小)

虽然本题 $N, Q \le 100$，可以直接 $O(NQ)$ 暴力打标记。但如果 $N, Q$ 很大（例如 $10^5$），CF 的这种思路可以无缝升级为差分数组：

1. 用差分数组快速标记哪些区间被 MIN 覆盖，哪些被 MEX 覆盖。
    
2. 扫一遍数组恢复标记。
    
3. $O(N+Q)$ 线性时间出解。
    

你的代码里用 `st` 数组其实已经是在做类似的事情，这是一个很好的直觉。

### 总结：下次遇到这种题该怎么想？

1. **读题**：看到多个区间限制（MIN, MAX, MEX, SUM, GCD...）。
    
2. **抽象**：不要把限制看作“动作”，要把限制看作“区域属性”。
    
3. **分类**：每个下标 $i$ 到底背负了哪些属性？
    
    - 属性 A 要求 $a_i \in S_1$
        
    - 属性 B 要求 $a_i \in S_2$
        
    - ...
        
4. **求交集**：
    
    - 如果 $i$ 同时有属性 A 和 B，那 $a_i$ 必须在 $S_1 \cap S_2$ 里。
        
    - 如果交集为空或者对目标无贡献，那这个位置就是“废弃位”。
        
5. **构造**：根据分类后的属性，选择最简单的满足条件的数（比如 $k$ 或 $i\%k$）。









# 类似题目的拓展


针对你提到的场景（$N, Q$ 很大，需要快速标记区间覆盖情况），最核心的板子就是 **一维差分 (1D Difference Array)**。

这是 competitive programming 中处理“静态区间修改，单点/区间查询”最基础也最最高效的工具。

### 🚀 核心模版：一维差分 (通用版)

这个模版适用于：给你 $M$ 个操作，每个操作是“把 $[l, r]$ 区间内的所有数加上 $v$（或者标记为类型 $v$）”，最后问你数组长什么样。

C++

```
// 设定一个稍微大一点的N，防止 r+1 越界
const int MAXN = 200005; 
int diff[MAXN]; // 差分数组
int a[MAXN];    // 原数组（复原后）

// 核心操作：区间 [l, r] 增加 val
// 时间复杂度：O(1)
void add(int l, int r, int val) {
    diff[l] += val;
    diff[r + 1] -= val; 
}

// 核心操作：构建最终数组（求前缀和）
// 时间复杂度：O(N)
void build(int n) {
    // 假设下标从 1 开始
    for (int i = 1; i <= n; i++) {
        a[i] = a[i - 1] + diff[i]; 
        // 此时 a[i] 就是位置 i 被覆盖的层数/数值
    }
}

// 多组测试用例时记得清空
void clear(int n) {
    for(int i = 0; i <= n + 1; i++) {
        diff[i] = 0;
        a[i] = 0;
    }
}
```

---

### 💡 针对 `Meximum Array 2` 的逻辑升级

如果是像上一题那样，有**两种不同性质**的区间（Min类型 和 Mex类型），你可以开两个差分数组，或者用位运算状态压缩（如果类型互斥或叠加）。

**双差分数组写法（最清晰，不易错）：**

C++

```
vector<int> diff_min(n + 2, 0);
vector<int> diff_mex(n + 2, 0);

// 读入操作
for(auto [op, l, r] : queries) {
    if (op == 1) { // Min 约束
        diff_min[l]++;
        diff_min[r+1]--;
    } else {       // Mex 约束
        diff_mex[l]++;
        diff_mex[r+1]--;
    }
}

// 扫描复原 (O(N))
int cur_min_cover = 0;
int cur_mex_cover = 0;

for(int i = 1; i <= n; i++) {
    cur_min_cover += diff_min[i];
    cur_mex_cover += diff_mex[i];
    
    // 直接根据覆盖情况分类讨论
    bool is_min = (cur_min_cover > 0);
    bool is_mex = (cur_mex_cover > 0);
    
    if (is_min && is_mex) {
        a[i] = k + 1; // 废弃位
    } else if (is_min) {
        a[i] = k;     // Min 专属位
    } else if (is_mex) {
        a[i] = i % k; // Mex 专属位
    } else {
        a[i] = k;     // 自由位
    }
}
```

---

### ⚔️ 练手好题推荐

为了巩固这个技巧，我推荐一道非常经典、专门考察差分思维的题目。这道题如果只会暴力 $O(NQ)$ 会 TLE，必须用差分优化到 $O(N+Q)$。

#### **题目：Codeforces 816B - Karen and Coffee**

链接: CF 816B

难度: 1400 - 1500 (Div. 2 B/C 级别)

题意描述：

Karen 正在准备咖啡。她有 $n$ 本食谱（$n \le 200,000$）。

第 $i$ 本食谱建议在 $[l_i, r_i]$ 温度范围内煮咖啡。

一个温度被称为“可接受的 (Admissible)”，如果它至少被 $k$ 本食谱推荐（即被覆盖 $\ge k$ 次）。

接下来有 $q$ 个查询，每个查询给你一个范围 $[a, b]$，问在这个范围内有多少个整数温度是“可接受的”？

**为什么推荐这道题？**

1. **区间覆盖计数**：你需要知道每个温度点被多少个食谱覆盖 $\rightarrow$ **典型差分**。
    
2. **二次前缀和**：题目问的是“可接受温度”的**数量**。你需要先算出每个点是否可接受（0或1），然后再对结果做一次前缀和，以便 $O(1)$ 回答区间查询。
    
3. **数据范围**：$N, Q, \text{Coordinate} \le 200,000$，暴力必死，完美契合差分算法。
    

---

### 💻 参考代码 (先自己试试，不行再看)

```cpp
#include <bits/stdc++.h>
using namespace std;

const int MAXN = 200005;
int diff[MAXN]; // 差分数组
int sum[MAXN];  // 标记后的前缀和数组

void solve() {
    int n, k, q;
    cin >> n >> k >> q;

    // 1. 差分处理覆盖次数 (O(N))
    for (int i = 0; i < n; i++) {
        int l, r;
        cin >> l >> r;
        diff[l]++;
        diff[r + 1]--;
    }

    // 2. 差分还原 + 转换为“是否可接受” (O(MAXN))
    // 这里的 cur 表示当前温度被覆盖了多少次
    int cur = 0;
    for (int i = 1; i < MAXN; i++) {
        cur += diff[i];
        // 如果覆盖次数 >= k，则这个温度是合法的，记为 1
        if (cur >= k) {
            sum[i] = 1;
        } else {
            sum[i] = 0;
        }
    }

    // 3. 对合法状态做前缀和，以便快速回答查询 (O(MAXN))
    for (int i = 1; i < MAXN; i++) {
        sum[i] += sum[i - 1];
    }

    // 4. 回答查询 (O(1) per query)
    while (q--) {
        int l, r;
        cin >> l >> r;
        // 区间和公式：S[r] - S[l-1]
        cout << sum[r] - sum[l - 1] << "\n";
    }
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(0);
    solve();
    return 0;
}
```



**Next Step**: 你可以直接去切掉 CF 816B，验证一下这个板子。做完告诉我感觉如何？








# CF 解题思路
- 对于每个元素，确定它是否属于 $\texttt{MEX}$ 约束、 $\min$ 约束、两者或无。
- 如果一个元素同时属于 $\texttt{MEX}$ 约束和 $\min$ 约束，则该元素对这两个约束都是无用的。
- 如果元素仅属于 $\min$ 约束，则为方便起见，可以将其设置为 $k$ 。
- 满足 $\texttt{MEX}$ 约束的“最快”方法是什么？如果同时有几个约束呢？
- 对于每个元素，确定它是否属于 $\texttt{MEX}$ 约束、 $\min$ 约束、两者或无。-如果元素被 $\min$ 约束覆盖，则它必须是 $\geq k$ ，

理想情况下，它应该是 $= k$ （以便立即满足约束）.-如果元素被 $\texttt{MEX}$ 约束覆盖，则它必须是 $\neq k$ ，

理想情况下，它应该是 $< k$ （以使约束更容易满足）. 

然后

-如果元素未被任何约束覆盖，则没有关系，您可以将其设置为任何值。

-如果一个元素同时属于 $\texttt{MEX}$ 约束和 $\min$ 约束，则该元素对这两个约束都是无用的。

但必须同时将其设置为 $\neq k$ 和 $\geq k$ 。例如，您可以将其设置为 $k+1$ 。

-如果元素仅属于 $\min$ 约束，则最好将其设置为 $k$ 。-其余元素是仅由 $\texttt{MEX}$ 约束覆盖的元素。

为了得到满足， $\texttt{MEX}$ 约束必须至少包含 $k$ 个这样的元素（因为它必须包含 $0$ 和 $k-1$ 之间的所有元素，这将违反 $\min$ 约束）.特别是，在第 $i$ 个此类元素处分配 $i \text{ mod } k$ 是可行的（如果 $\texttt{MEX}$ 约束包含 $k$ 此类元素，它们的值是 $[0, 1, \ldots, k-1]$ ，可能是移位的）. 

复杂性：每个测试用例 $O(nq)$ 或 $O(n+q)$ 。

额外的好处：在上述解决方案中，我们将 $i \text{ mod } k$ 分配给第 $i$ 个仅由 $\texttt{MEX}$ 约束覆盖的元素。

但是，将 $a_i = i \text{ mod } k$ 分配给这样的元素（其中 $i$ 是整个数组中的位置，而不是仅考虑这些元素的位置）也很有效。你能证明吗？




# CF  题解代码
```cpp
#include <bits/stdc++.h>
using namespace std;

#define nl "\n"
#define nf endl
#define ll long long
#define pb push_back
#define _ << ' ' <<

#define INF (ll)1e18
#define mod 998244353
#define maxn 110

int main() {
    ios::sync_with_stdio(0);
    cin.tie(0);

    #if !ONLINE_JUDGE && !EVAL
        ifstream cin("input.txt");
        ofstream cout("output.txt");
    #endif

    ll t; cin >> t;
    while (t--) {
        ll n, k, q; cin >> n >> k >> q;
        vector<ll> a(n + 1, 0);

        vector<ll> mn(n + 1, 0), mx(n + 1, 0);
        while (q--) {
            ll c, l, r; cin >> c >> l >> r;
            for (ll i = l; i <= r; i++) {
                if (c == 1) mn[i] = 1;
                else mx[i] = 1;
            }
        }

        for (ll i = 1; i <= n; i++) {
            if (mn[i] == 1 && mx[i] == 1) a[i] = k + 1;
            else if (mx[i] == 1) a[i] = i % k;
            else a[i] = k;
        }

        for (ll i = 1; i <= n; i++) cout << a[i] << ' ';
        cout << nl;
    }

    return 0;
}
```