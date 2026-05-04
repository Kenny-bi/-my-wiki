




## C. Test Generator

时间限制：每个测试2秒  
内存限制：每个测试512 MB  

## 问题描述  

您正在开发一个测试生成器。它接受两个整数 $s$ 和 $m$ 作为输入。您需要构造一个非负整数数组 $a=[a_1,a_2,\dots,a_n]$，满足以下条件：  

- $\sum_{i=1}^{n} a_i = s$；  
- 对于每个 $i$，条件 $a_i \& m = a_i$ 成立，其中 $\&$ 表示按位与运算符。  

换句话说，在每个数字 $a_i$ 中，被设置为 1 的位只能位于数字 $m$ 中的位也被设置为 1 的位置。  

判断是否存在至少一个这样的数组。如果存在，求最小可能长度 $n$。  

## 输入格式  

每个测试包含多个测试用例。第一行包含测试用例的数量 $t$ ($1 \leq t \leq 10^4$)。接下来是测试用例的描述。  

每个测试用例由一行包含两个整数 $s$ 和 $m$ ($1 \leq s, m \leq 10^{18}$) 组成，即生成器的参数。  

## 输出格式  

对于每个测试用例，输出一个整数：  

- 如果这样的数组不存在，输出 $-1$；  
- 否则，输出最小可能的 $n$，即数组的长度。  

## 样例输入  

```
6
13 5
13 3
13 6
1000000007 2776648
99999999999 1
998244353 1557287
```

## 样例输出  

```
3
5
-1
-1
99999999999
642
```

## 样例说明  

分析一些例子：  

- 对于 $s=13, m=5$，答案为 $3$，因为存在合适的数组 $a=[5,4,4]$；  
- 对于 $s=13, m=3$，答案为 $5$，因为存在合适的数组 $a=[3,3,3,3,1]$；  
- 对于 $s=13, m=6$，答案为 $-1$，因为不存在合适的数组。







# AC  代码
```cpp
// Problem: CF 2203 C
// Contest: Codeforces - Educational Codeforces Round 187 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2203/problem/C
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long 

int check(int n,int s,int m){
	int l=s,r=s;
	
	for(int i=0;i<62;i++){
		if(l==0&&r==0) break;
		if( (m>>i)&1 ){
			if(l<=n) l=0;
			else l=(l-n+1)/2;
			r/=2;
			
			if(l>r) return 0;
		}else{
			l=(l+1)/2;
			r/=2;
			if(l>r) return 0;
		}
	}
	return l==0;
}


void solve(){
	int s,m;
	cin>>s>>m;
	int re=m&-m;
	if(s%re!=0){
		cout<<-1<<endl;
		return ;
	}
	
	int l=0,r=1e18;
	while(l<r){
		int mid=(l+r)/2;
		if(check(mid,s,m)) r=mid;
		else l=mid+1;
	}
	cout<<l<<endl;
	
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






# 抄jiangly 代码
```cpp
// Problem: CF 2203 C
// Contest: Codeforces - Educational Codeforces Round 187 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2203/problem/C
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long 

int check(int n,int s,int m){
	int res=s;
	for(int i=60;i>=0;i--){
		if((m>>i)&1){
			res-=min(res>>i,n)<<i;
		}
	}
	return res==0;
}


void solve(){
	int s,m;
	cin>>s>>m;
	int re=m&-m;
	if(s%re!=0){
		cout<<-1<<endl;
		return ;
	}
	
	int l=0,r=1e18;
	while(l<r){
		int mid=(l+r)/2;
		if(check(mid,s,m)) r=mid;
		else l=mid+1;
	}
	cout<<l<<endl;
	
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







# jiangly 代码
```cpp
#include <bits/stdc++.h>

using i64 = long long;
using u64 = unsigned long long;
using u32 = unsigned;

using u128 = unsigned __int128;
using i128 = __int128;

void solve() {
    i64 s, m;
    std::cin >> s >> m;
    
    i64 low = m & -m;
    if (s % low) {
        std::cout << -1 << "\n";
        return;
    }
    
    i64 lo = 0, hi = s;
    while (lo < hi) {
        i64 x = (lo + hi) / 2;
        
        i64 res = s;
        for (int i = 59; i >= 0; i--) {
            if (m >> i & 1) {
                res -= std::min(res >> i, x) << i;
            }
        }
        if (res == 0) {
            hi = x;
        } else {
            lo = x + 1;
        }
    }
    
    std::cout << lo << "\n";
}

int main() {
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);
    
    int t;
    std::cin >> t;
    
    while (t--) {
        solve();
    }
    
    return 0;
}
```




# CF 2203 C Test Generator 二分答案与位运算贪心 #算法/二分答案 #算法/位运算 #算法/贪心

## 1. 核心思想

> 结合**二分答案**与**位运算贪心**，通过二分探求最小数组长度 $n$。在已知长度 $n$ 的限制下，由于二进制高位面值总是低位的倍数，采用从高位到低位的贪心策略，优先消耗高位额度以最快凑齐目标总和 $s$。

## 2. 算法流程/图解

1. **前置合法性判定**：
    
    提取掩码 $m$ 的最低有效位（`lowbit`）。因为只能使用 $m$ 中为 1 的位进行加和，所以最终生成的任何数以及总和 $s$ 必定是该 `lowbit` 的整数倍。若 $s$ 不能被 `lowbit` 整除，则直接判定无解，返回 $-1$。
    
2. **二分数组长度**：
    
    目标数组长度 $n$ 的理论最小值为 $0$，最大值不超过 $s$（最坏情况下全用 $1$ 凑）。在区间 $[0, s]$ 内二分查找最小长度。
    
3. **贪心 Check 逻辑**：
    
    - 设当前二分的数组长度限制为 $x$。
        
    - 维护一个剩余待凑总和 $res$（初始值为 $s$）。
        
    - 从第 $59$ 位向下遍历到第 $0$ 位（由高到低）。
        
    - 如果 $m$ 的第 $i$ 位可用（即 $(m \gg i) \& 1$ 为真），则在不超过当前长度限制 $x$ 且不超过剩余总和 $res$ 的前提下，尽可能多地使用面值 $2^i$。
        
    - 最后检查 $res$ 是否被完全抵消为 $0$。
        

## 3. 黄金代码模板

C++

```
#include <bits/stdc++.h>

using i64 = long long;

// 核心验证逻辑：在长度限制为 x 的情况下，能否利用 m 的子集掩码凑出 s
bool check(i64 x, i64 s, i64 m) {
    i64 res = s; // 剩余需要凑出的和
    // 从高位到低位贪心，充分利用二进制倍数关系的性质
    for (int i = 59; i >= 0; i--) {
        if (m >> i & 1) { // 如果 m 的第 i 位允许使用
            // res >> i 等价于 res / (2^i)，即理论上最多需要多少个 2^i
            // 但每个位最多只能填 x 个 1（受限于数组长度）
            // 取二者最小值进行扣除
            res -= std::min(res >> i, x) << i; 
        }
    }
    return res == 0; // 判断是否刚好凑完
}

void solve() {
    i64 s, m;
    std::cin >> s >> m;
    
    // 1. 前置合法性判断：s 必须是 m 最低可用位的倍数
    i64 low = m & -m;
    if (s % low != 0) {
        std::cout << -1 << "\n";
        return;
    }
    
    // 2. 二分最小数组长度
    i64 lo = 0, hi = s;
    while (lo < hi) {
        i64 mid = (lo + hi) / 2;
        if (check(mid, s, m)) {
            hi = mid; // 当前长度可行，尝试寻找更小的长度
        } else {
            lo = mid + 1; // 当前长度不可行，必须增加长度
        }
    }
    
    std::cout << lo << "\n";
}

int main() {
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);
    
    int t;
    std::cin >> t;
    while (t--) {
        solve();
    }
    return 0;
}
```

## 4. 易错点/Debug清单

> [!WARNING] **核心剪枝/无解判定不可漏**
> 
> 必须使用 `m & -m` 获取 `lowbit` 进行整除判断 `$s \% (m \& -m) \neq 0$`。如果省略这一步，奇偶性不匹配会导致贪心策略陷入死胡同或二分无限循环。

> [!NOTE] **位移操作的优先级与溢出**
> 
> 左移和右移运算符的优先级低于加减运算。在写 `std::min(res >> i, x) << i` 时，注意位移的数据类型。因为 $s, m$ 高达 $10^{18}$，必须全程使用 `long long` (代码中的 `i64`) 以防溢出。循环上限设为 $59$ 是因为 $2^{59} > 10^{18}$，完全足以覆盖 $s$ 和 $m$ 的取值范围。

## 5. 参考资料

- [Codeforces Educational Round 187 - Problem C: Test Generator](https://codeforces.com/contest/2203/problem/C)
    

---

需要我为你整理其他算法题目的卡片笔记吗？