
[[各类题目的总结]]

## C. Range Operation
时间限制：每个测试 2 秒
内存限制：每个测试 512 兆字节

## 问题描述
给定一个长度为 $n$ 的整数数组 $a$。

你可以执行以下操作：选择一个区间 $[l,r]$（$1 \le l \le r \le n$），并将元素 $a_l, a_{l+1}, \dots, a_r$ 的值替换为 $(l+r)$。

你的任务是计算如果最多执行一次上述操作，可能获得的最大数组总和。

## 输入格式
第一行包含一个整数 $t$（$1 \le t \le 10^4$）——测试用例的数量。

每个测试用例的第一行包含一个整数 $n$（$1 \le n \le 2 \cdot 10^5$）。

第二行包含 $n$ 个整数 $a_1, a_2, \dots, a_n$（$0 \le a_i \le 2n$）。

输入数据的额外约束：所有测试用例的 $n$ 之和不超过 $2 \cdot 10^5$。

## 输出格式
对于每个测试用例，输出一个整数——如果最多执行一次操作，可能获得的最大数组总和。

## 样例输入
```
4
3
2 5 1
2
4 4
4
1 3 2 1
5
3 2 0 9 10
```

## 样例输出
```
13
8
20
32
```

## 样例说明
在第一个样例中，你可以对子数组 $[3,3]$ 执行操作，得到数组 $[2,5,6]$，总和为 $13$。

在第二个样例中，不需要执行任何操作。

在第三个样例中，你可以对子数组 $[1,4]$ 执行操作，得到数组 $[5,5,5,5]$，总和为 $20$。

在第四个样例中，你可以对子数组 $[2,3]$ 执行操作，得到数组 $[3,5,5,9,10]$，总和为 $32$。



# 第一版
```cpp
// Problem: CF 2169 C
// Contest: Codeforces - Educational Codeforces Round 184 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2169/problem/C
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include <bits/stdc++.h>
using namespace std;
typedef long long LL;
typedef pair<LL,LL> PII;
const int N=1e9+7,INF=0x3f3f3f3f;


void solve() {
    int n;
    cin>>n;
    vector<int> a(n+1);
    for(int i=1;i<=n;i++){
    	cin>>a[i];
    }
    
    vector<int> pre(n+1);
    for(int i=1;i<=n;i++) pre[i]=pre[i-1]+a[i];
    int ans=pre[n];
    
    for(int i=1;i<=n;i++){
    	for(int j=1;j<=i;j++){
    		int res=pre[n];
    		res-=(pre[i]-pre[j-1]);
    		res+=(i+j)*(i-j+1);
    		ans=max(ans,res);
    	}
    }
    printf("%d\n",ans);
    
    return;
}

int main() {
    int t;
    cin >> t;
    while (t--) {
        solve();
    }
    return 0;
}
```





# 第二版错误代码
```cpp
// Problem: CF 2169 C
// Contest: Codeforces - Educational Codeforces Round 184 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2169/problem/C
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include <bits/stdc++.h>
using namespace std;
typedef long long LL;
typedef pair<LL,LL> PII;
const int N=1e9+7;
const LL INF=2e18;

void solve() {
    LL n;
    cin>>n;
    vector<LL> a(n+1);
    for(int i=1;i<=n;i++){
    	cin>>a[i];
    }
    
    vector<LL> pre(n+3,0);
    for(int i=1;i<=n;i++) pre[i]=pre[i-1]+a[i];
    LL ans=pre[n];
    
    vector<LL> A(n+3),B(n+3);
    for(int i=1;i<=n;i++){
    	A[i]=-pre[i]+i*i+i;
    	B[i]=pre[i-1]-i*i+i;
    }
    LL res=0;
    
    vector<LL> preb(n+3,-INF),sufa(n+3,-INF);
    for(int i=1;i<=n;i++){
    	preb[i]=max(preb[i-1],B[i]);
    }
    for(int i=n;i>=1;i--){
    	sufa[i]=max(sufa[i+1],A[i]);
    }
    

    for(int i=1;i<=n;i++){
    	res=max(res,preb[i]+sufa[i]);
    }
    printf("%lld\n",ans+res);
   
    
    return;
}

int main() {
    int t;
    cin >> t;
    while (t--) {
        solve();
    }
    return 0;
}
```




# 错误原因
你这次把自己绕晕了！

你遇到的情况非常微妙，我来帮你梳理一下：

1. 你贴的**题目描述 (C. Range Operation)** 和你贴的**第一版 $O(N^2)$ 代码**是**完全对应的**。
    
    - `pre[n]`：原始总和。
        
    - `res -= (pre[i] - pre[j-1])`：减去区间 `[j, i]` (即 `[l, r]`) 的原始和。
        
    - `res += (i+j)*(i-j+1)`：加上新区间的和 `(l+r) * (r-l+1)`。
        
    - 所以，你**一直**在解决正确的题目。
        
2. 你的**第二版 $O(N)$ 代码**（使用 `mxb` 的那个）的**优化逻辑是完全正确的**。
    
    - 你正确地将 $O(N^2)$ 暴力解法化简为了 $O(N)$ 的动态规划。
        
3. **那为什么你的 $O(N)$ 代码还是错的？**
    
    - 答案是：**整数溢出 (Integer Overflow)**。
        

---

### 🐞 隐藏的 Bug：`i*i` 溢出

你看你 $O(N)$ 代码的这个循环和里面的两行：

C++

```
void solve() {
    LL n;
    cin>>n;
    // ...
    // ...
    for(int i=1;i<=n;i++){  // <--- i 是 int 类型
    	A[i]=-pre[i]+i*i+i;  // <--- Bug 在这里
    	B[i]=pre[i-1]-i*i+i;  // <--- Bug 在这里
    	mxb=max(mxb,B[i]);
    	res=max(res,mxb+A[i]);
    }
    // ...
}
```

**问题分析：**

- 你的循环变量 `i` 是一个 `int`。
    
- $N$ 最大是 $2 \cdot 10^5$。当 `i` 比较大时，比如 `i = 50000` (五万)，`i*i` 等于 $25 \cdot 10^8$。
    
- 一个 32 位 `int` 的最大值大约是 $21 \cdot 10^8$ (即 `2147483647`)。
    
- **在 `i*i+i` 这行代码中，`i*i` 会首先作为 `int` 类型来计算。**
    
- 当 `i=50000` 时，`i*i` **在计算时就已经溢出**了，它会变成一个奇怪的负数。
    
- 这个溢出的、错误的 `int` 值，随后才被用于 `LL`（long long）的加法运算（如 `-pre[i] + ...`）。
    
- 因此，你的 `A[i]` 和 `B[i]` 数组从源头上就是错误的，导致你虽然通过了所有样例（因为样例的 $N$ 很小），但提交后会 WA 隐藏的大数据。
    

---

### 💡 如何修复

你必须确保 `i*i` 是在 `long long` 层面进行计算的。

方法一：强制类型转换（推荐）

在 i*i 之前，把 i 转换为 LL。

C++

```
for(int i=1; i<=n; i++){
    A[i] = -pre[i] + (LL)i*i + i;
    B[i] = pre[i-1] - (LL)i*i + i;
    mxb = max(mxb, B[i]);
    res = max(res, mxb+A[i]);
}
```

方法二：修改循环变量类型

直接让 i 成为 long long。

C++

```
for(LL i=1; i<=n; i++){
    A[i] = -pre[i] + i*i + i;
    B[i] = pre[i-1] - i*i + i;
    mxb = max(mxb, B[i]);
    res = max(res, mxb+A[i]);
}
```

你只要修复了这个溢出问题，你的 $O(N)$ 代码就是正确的了。你之前的 `mxb=-INF` 和 `res=0` 的初始化逻辑是**完全正确**的（它完美地处理了“可以不操作”的情况）。




# 赛时AC代码
```cpp
// Problem: CF 2169 C
// Contest: Codeforces - Educational Codeforces Round 184 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2169/problem/C
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include <bits/stdc++.h>
using namespace std;
typedef long long LL;
typedef pair<LL,LL> PII;
const int N=1e9+7;
const LL INF=2e18;

void solve() {
    LL n;
    cin>>n;
    vector<LL> a(n+1);
    for(LL i=1;i<=n;i++){
    	cin>>a[i];
    }
    
    vector<LL> pre(n+3,0);
    for(LL i=1;i<=n;i++) pre[i]=pre[i-1]+a[i];
    LL ans=pre[n];
    
    vector<LL> A(n+3),B(n+3);
    LL mxb=-INF;
     LL res=0;
    for(LL i=1;i<=n;i++){
    	A[i]=-pre[i]+i*i+i;
    	B[i]=pre[i-1]-i*i+i;
    	mxb=max(mxb,B[i]);
    	res=max(res,mxb+A[i]);
    }
   
    // vector<LL> preb(n+3,-INF),sufa(n+3,-INF);
    // for(int i=1;i<=n;i++){
    	// preb[i]=max(preb[i-1],B[i]);
    // }
    // for(int i=n;i>=1;i--){
    	// sufa[i]=max(sufa[i+1],A[i]);
    // }
//     
// 
    // for(int i=1;i<=n;i++){
    	// res=max(res,preb[i]+sufa[i]);
    // }
    printf("%lld\n",ans+res);
   
    
    return;
}

int main() {
    int t;
    cin >> t;
    while (t--) {
        solve();
    }
    return 0;
}
```



# 我的那一版也能AC 
```cpp
// Problem: CF 2169 C
// Contest: Codeforces - Educational Codeforces Round 184 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2169/problem/C
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include <bits/stdc++.h>
using namespace std;
typedef long long LL;
typedef pair<LL,LL> PII;
const int N=1e9+7;
const LL INF=2e18;

void solve() {
    LL n;
    cin>>n;
    vector<LL> a(n+1);
    for(LL i=1;i<=n;i++){
    	cin>>a[i];
    }
    
    vector<LL> pre(n+3,0);
    for(LL i=1;i<=n;i++) pre[i]=pre[i-1]+a[i];
    LL ans=pre[n];
    
    vector<LL> A(n+3),B(n+3);
    LL mxb=-INF;
     LL res=0;
    for(LL i=1;i<=n;i++){
    	A[i]=-pre[i]+i*i+i;
    	B[i]=pre[i-1]-i*i+i;
    }
   
    vector<LL> preb(n+3,-INF),sufa(n+3,-INF);
    for(LL i=1;i<=n;i++){
    	preb[i]=max(preb[i-1],B[i]);
    }
    for(LL i=n;i>=1;i--){
    	sufa[i]=max(sufa[i+1],A[i]);
    }
    

    for(LL i=1;i<=n;i++){
    	res=max(res,preb[i]+sufa[i]);
    }
    printf("%lld\n",ans+res);
   
    
    return;
}

int main() {
    int t;
    cin >> t;
    while (t--) {
        solve();
    }
    return 0;
}
```




# CF 题解思路
让我们将 $S_i$ 定义为数组 $a$ 中前 $i$ 个元素的和。当我们对范围 $[l, r]$ 执行操作时，总和将更改 $(l + r)(r - l + 1) - (S_r - S_{l-1})$ 。该表达式可简化为 $f(r) - f(l-1)$ ，其中 $f(x) = x^2 + x - S_x$ 。因此，我们可以迭代 $r$ 的值，

跟踪我们到目前为止遇到的 $f(l)$ 的最小值（让我们称之为 $mn$ ）. 然后我们可以用 $S_n + f(r) - mn$ 更新答案。还有另一种解决方案：如果将段 $[l, r]$ 上的每个元素替换为 $(l + r)$ ，

段上的和等于 $2l + 2(l+1) + \dots + 2r$ （因为 $(l + r)(r - l + 1)$ 可以被视为从 $2l$ 到的算术级数的和 $2r$ （步骤 $2$ ）. 因此，您可以将该操作视为将 $a_i$ 替换为 $2i$ 。您可以使用它来形成另一个数组 $b$ ，其中 $b_i = 2i - a_i$ ，并找到其中具有最大总和的子段。

要解决这个问题，您可以使用Kadane的算法或前缀和。

# CF 题解代码
```cpp
#include <bits/stdc++.h>
 
using namespace std;
 
using li = long long;
 
int main() {
  int t;
  cin >> t;
  while (t--) {
    int n;
    cin >> n;
    li s = 0, mn = 0, bst = 0;
    for (int i = 0; i < n; ++i) {
      int x;
      cin >> x;
      s += x;
      li cur = (i + 1) * li(i + 2) - s;
      mn = min(mn, cur);
      bst = max(bst, cur - mn);
    }
    cout << s + bst << '\n';
  }
}
```