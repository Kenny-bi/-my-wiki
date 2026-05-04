


## E. Blackslex and Girls
时间限制：每测试点 2 秒  
内存限制：每测试点 256 兆字节  

## 问题描述
在尝试使用固定长度的比特串的 De Bruijn 序列搭讪女孩失败后，Blackslex 将注意力转向了政治。

由于其高魅力，他现在负责为自己的国家的 $n$ 个选区绘制边界。在 Blackslex 的国家，有 $x$ 个支持 A 党的选民和 $y$ 个支持 B 党的选民。凭借他惊人的绘图技巧，他可以将任何政党的选民分配到他选择的任何选区。

他与比特串的渊源让他思考，是否能够分配选民，使得每个选区的胜者遵循某种特定的比特串模式。为了避免引起怀疑，他还必须为每个选区分配至少 $p_i$ 名选民。请告诉他这是否可能！

形式化地，给定一个长度为 $n$ 的二进制字符串 $s$，一个长度为 $n$ 的数组 $p$，以及两个整数 $x$ 和 $y$。

你需要判断是否存在两个长度为 $n$ 的非负整数数组 $a$ 和 $b$，满足以下条件：
1. $a_1 + a_2 + \dots + a_n = x$
2. $b_1 + b_2 + \dots + b_n = y$
3. 对于每个 $1 \le i \le n$，$a_i + b_i \ge p_i$
4. 对于每个 $1 \le i \le n$：
   - 如果 $s_i = 0$，则 $a_i > b_i$
   - 如果 $s_i = 1$，则 $b_i > a_i$

## 输入格式
第一行包含一个整数 $t$ $(1 \le t \le 10^4)$ —— 测试用例的数量。

每个测试用例的第一行包含三个整数 $n$、$x$ 和 $y$ $(1 \le n \le 2 \cdot 10^5, 1 \le x, y \le 10^9)$。

第二行包含一个长度为 $n$ 的二进制字符串 $s$。

第三行包含 $n$ 个整数 $p_1, p_2, \dots, p_n$ $(1 \le p_i \le 10^9)$。

保证所有测试用例的 $n$ 之和不超过 $2 \cdot 10^5$。

## 输出格式
对于每个测试用例，如果存在满足所有条件的数组 $a, b$，则输出 `"YES"`（不区分大小写），否则输出 `"NO"`。

## 样例输入
```
6
3 5 5
010
2 4 3

4 2 3
0001
1 1 1 1

2 4 2
00
3 3
4 23 20
1111
2 2 2 2
1 25 26
0
51
2 4 2
00
3 4
```
## 样例输出
```
YES
NO
YES
NO
NO
NO
```
## 样例说明
在第一个测试用例中，一种可能的选民分配方案是：$a = [2, 0, 3]$ 和 $b = [0, 4, 1]$。

在第三个测试用例中，一种可能的选民分配方案是：$a = [2, 2]$ 和 $b = [1, 1]$。

对于其他测试用例，可以证明不存在满足条件的选民分配方案。





# WA  2了
```cpp
// Problem: CF 2179 E
// Contest: Codeforces - Codeforces Round 1071 (Div. 3)
// URL: https://codeforces.com/contest/2179/problem/E
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define ios ios::sync_with_stdio(false);cin.tie(nullptr);
#define int long long

void solve(){
	int n,x,y;
	cin>>n>>x>>y;
	string s;
	cin>>s;
	vector<int> p(n);
	int na=0,nb=0;
	int flag1=0,flag2=0;
	for(int i=0;i<n;i++){
		cin>>p[i];
		if(s[i]=='0'){
			na=na+p[i]/2+1;
			nb=nb+(p[i]-p[i]/2-1);
			flag1=1;
		}else{
			nb+=(p[i]/2+1);
			na=na+(p[i]-p[i]/2-1);
			flag2=1;
		}
	}
	
	if(na>x||nb>y){
		printf("NO\n");
		return ;
	}
	
	if(flag1&&flag2){
		printf("YES\n");
	}else if(flag1&&flag2==0){
		if(x>=y+n) printf("YES\n");
		else printf("NO\n"); 
	}else{
		if(x+n<=y) printf("YES\n");
		else printf("NO\n"); 
	}
	
	
	return ;
}

signed main(){
	ios;
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
// Problem: CF 2179 E
// Contest: Codeforces - Codeforces Round 1071 (Div. 3)
// URL: https://codeforces.com/contest/2179/problem/E
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long

void solve(){
	int n,x,y;
	cin>>n>>x>>y;
	string s;
	cin>>s;
	vector<int> p(n);
	int na=0,nb=0;
	int flag1=0,flag2=0;
    int tot=0;
	for(int i=0;i<n;i++){
		cin>>p[i];
		tot+=p[i];
		if(s[i]=='0'){
			na=na+p[i]/2+1;
			flag1=1;
		}else{
			nb+=(p[i]/2+1);
			flag2=1;
		}
	}
	
	if(na>x||nb>y){
		printf("NO\n");
		return ;
	}
	
	if(x+y<tot){
		printf("NO\n");
		return ;
	}
	if(flag1&&flag2==0&&x<y+n){
		printf("NO\n");
		return ;
	}
	if(flag2&&flag1==0&&x+n>y){
		printf("NO\n");
		return ;
	}
	printf("YES\n");
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




# WA 2的原因
这是对之前错误的**彻底修正**。

之前的核心误区在于：**我这种“输家必须填坑”的假设是完全错误的。**

### 1. 错误思路 vs 正确思路

- **❌ 之前的错误思路（刚性填坑）：**
    
    - 我认为：如果选区 $i$ 需要 10 票，赢家出了 6 票，那么输家**必须**出 4 票。
        
    - **导致的问题：** 这强制要求输家手里必须有票。但实际上，如果赢家很有钱，赢家可以一个人出 10 票，输家出 0 票，这也是合法的（10 > 0 且 10+0 >= 10）。
        
- **✅ 正确的思路（总量池子）：**
    
    - **赢家的责任是刚性的：** 想要赢，必须出 $\lfloor p_i/2 \rfloor + 1$ 票。这是门票，少一张都不行。
        
    - **输家的责任是弹性的：** 输家**不一定**要出钱。
        
    - **剩余的票数（$p_i$ 的缺口）：** 只要 $x$ 和 $y$ 的**总和**足够大，能够填满所有选区的 $p_i$ 需求即可。不管是 A 出还是 B 出，只要填满了 $p_i$ 且不破坏赢家的地位就行。
        

---

### 2. 重新分析：三个独立判定条件

要把这道题做对，需要检查且仅需要检查以下三个维度的限制。只要满足，就是 `YES`，否则 `NO`。

#### 条件一：赢家付得起“门票费”吗？

这是最优先的。不管总票数多少，A 必须拿下所有 $s_i=0$ 的局，B 必须拿下所有 $s_i=1$ 的局。

- $Cost_A = \sum_{s_i=0} (\lfloor p_i/2 \rfloor + 1)$
    
- $Cost_B = \sum_{s_i=1} (\lfloor p_i/2 \rfloor + 1)$
    
- **判定：** 如果 $x < Cost_A$ 或者 $y < Cost_B$，直接 **NO**。
    

#### 条件二：总票数够填满所有 $p_i$ 吗？

赢家付完门票后，所有选区 $i$ 还剩下 $Rem_i = p_i - (\lfloor p_i/2 \rfloor + 1)$ 的票数缺口。这些缺口需要用 A 和 B 剩余的票来填。

实际上不需要算这么细，宏观来看：

- **判定：** 如果 $x + y < \sum p_i$，说明连最低总票数要求都达不到，直接 **NO**。
    

#### 条件三：防止“一边倒”的数学限制

这是唯一的特殊情况。

- **如果 A 全赢（$s$ 全是 '0'）：**
    
    - 因为每个选区都要求 $a_i > b_i$（即 $a_i \ge b_i+1$）。
        
    - 累加起来：$\sum a_i \ge \sum b_i + n$。
        
    - 即 $x \ge y + n$。
        
    - **判定：** 如果是全 0 且 $x < y + n$，直接 **NO**。
        
- **如果 B 全赢（$s$ 全是 '1'）：**
    
    - 同理，要求 $y \ge x + n$。
        
    - **判定：** 如果是全 1 且 $y < x + n$，直接 **NO**。
        

> 为什么“混色”的情况不需要判定？
> 
> 如果既有 A 赢也有 B 赢，我们总能把多余的 A 票塞进 A 赢的区（扩大优势），把多余的 B 票塞进 B 赢的区（扩大优势），从而消耗掉所有多余票数而不破坏规则。只有在“一边倒”时，输家的票没地方扔，才会导致限制。

---

### 3. 正确逻辑的代码实现

这份代码完全基于上述三点逻辑，去掉了所有不必要的假设。

C++

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <numeric>

using namespace std;

typedef long long ll;

void solve() {
    int n;
    ll x, y;
    if (!(cin >> n >> x >> y)) return;
    
    string s;
    cin >> s;
    
    vector<ll> p(n);
    ll sum_p = 0;
    
    // 统计必须支出的“门票费”
    ll min_cost_a = 0;
    ll min_cost_b = 0;
    
    bool a_wins_something = false;
    bool b_wins_something = false;
    
    for (int i = 0; i < n; i++) {
        cin >> p[i];
        sum_p += p[i]; // 统计总需求
        
        ll win_cost = p[i] / 2 + 1;
        
        if (s[i] == '0') {
            min_cost_a += win_cost;
            a_wins_something = true;
        } else {
            min_cost_b += win_cost;
            b_wins_something = true;
        }
    }
    
    // 【判定 1】赢家必须付得起门票
    if (x < min_cost_a || y < min_cost_b) {
        cout << "NO\n";
        return;
    }
    
    // 【判定 2】总票数必须够填满所有需求
    if (x + y < sum_p) {
        cout << "NO\n";
        return;
    }
    
    // 【判定 3】一边倒的特殊限制
    // 如果全是 A 赢，A 的总票数必须比 B 多至少 n
    if (!b_wins_something && x < y + n) {
        cout << "NO\n";
        return;
    }
    // 如果全是 B 赢，B 的总票数必须比 A 多至少 n
    if (!a_wins_something && y < x + n) {
        cout << "NO\n";
        return;
    }
    
    // 如果通过了所有考验
    cout << "YES\n";
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int t;
    if (cin >> t) {
        while (t--) {
            solve();
        }
    }
    return 0;
}
```

### 总结错误的教训

这次的教训在于**贪心策略的边界**。

- **之前的错误**：我把局部最优（让每个选区正好卡在 $p_i$）当成了全局限制。我担心“票浪费了怎么办”，所以强行让输家填坑。
    
- **现在的修正**：这道题的核心不在于“省票”，而在于“只要总票够，怎么分都行”。**输家是没有义务出票的**，只要总票数够填满 $p_i$，具体的分配可以非常灵活（比如赢家全包）。
    

现在这个逻辑是严丝合缝的。