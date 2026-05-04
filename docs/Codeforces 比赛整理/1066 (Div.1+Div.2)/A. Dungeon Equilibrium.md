---
created: 2025-11-24 09:26
tags:
  - algorithm/贪心
  - difficulty/待定
  - status/AC
  - site/Codeforces
---



**题目链接**: [](<% tp.file.cursor(1) %>) 
**核心考点**: [[贪心]]





## A. Dungeon Equilibrium
时间限制：每个测试 1 秒
内存限制：每个测试 256 MB

## 问题描述
如果数组中每个至少出现一次的整数 $x$ 都恰好出现 $x$ 次，则该数组被称为**平衡数组**。例如，$[1,4,2,4,4,4,2]$ 是平衡的，因为：
- 数字 $1$ 出现 $1$ 次
- 数字 $2$ 出现 $2$ 次  
- 数字 $4$ 出现 $4$ 次

但 $[2]$ 和 $[2,2,2]$ 都不是平衡数组，因为数字 $2$ 出现的次数不等于 $2$。

现在给定一个包含 $n$ 个元素的数组 $a = [a_1,a_2,\dots,a_n]$。该数组当前可能不平衡，你可以通过删除一些元素来使其平衡。请问要使数组平衡，最少需要删除多少个元素？

## 输入格式
每个测试包含多个测试用例。第一行包含测试用例的数量 $t$ ($1 \le t \le 500$)。

接下来是每个测试用例的描述：
- 第一行包含一个整数 $n$ ($1 \le n \le 100$) — 数组 $a$ 的大小
- 第二行包含 $n$ 个整数 $a_1, a_2, \dots, a_n$ ($0 \le a_i \le n$) — 数组 $a$ 的元素

注意：题目没有对所有测试用例的 $n$ 之和设置约束。

## 输出格式
对于每个测试用例，输出一行包含一个整数：为使数组平衡需要删除的最少元素数量。

## 样例输入
```
4
3
1 2 2
5
1 1 2 2 3
10
1 2 3 2 4 4 4 4 5 2
1
0
```

## 样例输出
```
0
2
3
1
```

## 样例说明
- 第一个测试用例中，数组已经是平衡的，不需要删除任何元素。
- 第二个测试用例中，可以删除一个 $1$ 和一个 $3$，得到数组 $[1,2,2]$，这是平衡的。
- 第三个测试用例需要删除 $3$ 个元素。
- 第四个测试用例需要删除 $1$ 个元素（即数字 $0$）。







## 💡 核心直觉 (一句话总结)
> 我感觉看清题目，只能删除就行
> **操作的最小次数，一般都是贪心**


## ✅ 正解思路
- 我想法就是遇到多的，就删除多余的，遇到少的，那只能全部删除了
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
	int n;
	cin>>n;
	vector<int> a(n+1);
	vector<int> cnt(n+3,0);
	for(int i=1;i<=n;i++){
		cin>>a[i];
		cnt[a[i]]++;
	}
	int ans=0;
	for(int i=0;i<=n;i++){
		if(cnt[i]<i) ans+=cnt[i];
		else ans+=cnt[i]-i;
	}
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





# CF 题解思路
- 每个值 $x$ 都独立于其他值，只有其自身的频率才起作用。
- 在平衡数组中，值 $x$ 必须出现 $0$ 次或**正好** $x$ 次。
- 每个值 $x$ 都独立于其他值，只有其自身的频率才重要。特别地，对于每个值，找出其频率 $f[x]$ 。-在平衡数组中，值 $x$ 必须出现 $0$ 次或**正好** $x$ 次。

具体来说，如果它在开头出现了 $< x$ 次，则必须删除它的所有出现次数，否则，应保留 $x$ 个事件。具体来说，对于每个 $x$ ，如果 $f[x] < x$ ，则在答案中添加 $f[x]$ ，否则添加 $f[x] - x$ 。

复杂度： $O(n)$ 时间。

允许较慢的解决方案（例如 $O(n^2)$ ）。


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
        ll n; cin >> n;
        vector<ll> f(n + 1, 0);
        for (ll i = 1; i <= n; i++) {
            ll x; cin >> x; f[x]++;
        }

        ll ans = 0;
        for (ll i = 0; i <= n; i++) {
            if (f[i] >= i) ans += f[i] - i;
            else ans += f[i];
        }

        cout << ans << nl;
    }

    return 0;
}
```