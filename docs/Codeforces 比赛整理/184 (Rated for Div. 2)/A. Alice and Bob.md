
[[贪心]]
## A. Alice and Bob
时间限制：每个测试 2 秒
内存限制：每个测试 512 兆字节

## 问题描述
Alice 和 Bob 有一个装有 $n$ 个弹珠的袋子，第 $i$ 个弹珠上写有整数 $v_i$。他们玩下面的游戏：首先，每个玩家选择一个整数（Alice 选择的整数记为 $a$，Bob 选择的整数记为 $b$）。之后，他们以任意顺序从袋子中抽取弹珠，直到袋子为空。对于每个弹珠，分数将给予所选整数与弹珠上整数更接近的玩家；如果距离相等，则 Alice 获得该分数。

例如，若 $a=10$，$b=30$，则：
- 对于整数为 $10,1,7,18,20$ 的弹珠（以及许多其他弹珠），Alice 获得分数（注意她也会为整数 $20$ 的弹珠得分）；
- 对于整数为 $59,25,30,21$ 的弹珠（以及许多其他弹珠），Bob 获得分数。

Bob 已经设法提前知道了 Alice 将选择的整数。请帮助他选择一个整数 $b$，以最大化他获得的分数。

## 输入格式
第一行包含一个整数 $t$（$1 \le t \le 10^4$）——测试用例的数量。

每个测试用例包含两行：
- 第一行包含两个整数 $n$ 和 $a$（$1 \le n \le 3 \cdot 10^5$；$1 \le a \le 10^9$）——分别表示袋中弹珠的数量和 Alice 选择的整数。
- 第二行包含 $n$ 个整数 $v_1, v_2, \dots, v_n$（$1 \le v_1 \le v_2 \le \dots \le v_n \le 10^9$）。

输入数据的额外约束：所有测试用例的 $n$ 之和不超过 $3 \cdot 10^5$。

## 输出格式
对于每个测试用例，输出一个整数 $b$（$0 \le b \le 2 \cdot 10^9$），表示 Bob 应选择的整数以最大化其得分。若有多个满足条件的整数，输出任意一个即可。

## 样例输入
```
3
7 21
10 20 30 40 50 60 70
6 500
200 200 300 500 600 600
2 7
7 7
```

## 样例输出
```
35
333
1337
```

## 样例说明
在第一个测试用例中，若 Bob 选择 $35$，他将为整数 $30,40,50,60,70$ 的弹珠获得 $5$ 分。

在第三个测试用例中，无论 Bob 选择什么整数，他都会获得 $0$ 分。




# 赛时AC代码
```cpp
// Problem: CF 2169 A
// Contest: Codeforces - Educational Codeforces Round 184 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2169/problem/A
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include <bits/stdc++.h>
using namespace std;
typedef long long LL;
typedef pair<LL,LL> PII;
const int N=1e9+7,INF=0x3f3f3f3f;
int n,a;

LL check(LL x,vector<int>& v){
	LL ans=0;
	for(int i=0;i<n;i++){
		if(abs(v[i]-a)<=abs(v[i]-x)) ;
		else ans++;
	}
	return ans;
}

void solve() {
    
    cin>>n>>a;
    vector<int> v(n);
    for(int i=0;i<n;i++){
    	cin>>v[i];
    }
    sort(v.begin(),v.end());
    LL ans=0;
    LL num=a+1;
    ans=check(a+1,v);
    if(check(a-1,v)>ans) num=a-1;
    printf("%lld\n",num);
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
- Bob选择 $b = a$ 从来都不是最优的。因此， $b < a$ 或 $b > a$ 。在所有可能的 $b < a$ 中，我们可以证明选择 $b = a - 1$ 总是正确的：对于每个整数 $v_i < a$ ，

$a - 1$ 将比 $a$ 更接近 $v_i$ ，并且对于每个整数 $v_i \ge a$ ， $a$ 将始终比任何 $b < a$ 更接近。因此，如果我们使用 $b = a - 1$ ，Bob获得的点数将等于具有 $v_i < a$ 的弹珠的数量，并且如果我们使用任何 $b < a - 1$ ，答案不会变得更好。类似地，如果 $b > a$ ，则选择 $b = a + 1$ 总是最优的，

并且点的数量将等于具有 $v_i > a$ 的弹珠的数量。因此，只需检查 $b = a-1$ 和 $b = a+1$ 即可。

对于 $b$ 的这两个值，可以容易地计算Bob获得的点数。



# CF 题解代码
```cpp
#include <bits/stdc++.h>
 
using namespace std;
 
int t;
 
int main() {
    cin >> t;
    for (int tc = 0; tc < t; ++tc) {
        int n, a;
        cin >> n >> a;
        vector <int> v(n);
        int l = 0, r = 0;
        for (int i = 0; i < n; ++i) {
            cin >> v[i];
            if (a > v[i]) ++l;
            if (a < v[i]) ++r;
        }
        
        cout << (l > r ? a - 1 : a + 1) << endl;
    }
    return 0;
}
```