


## A. Yes or Yes

时间限制：每个测试点 1 秒
内存限制：每个测试点 256 MB

## 问题描述

去年圣诞节，你的朋友 Fernando 送给你一个仅由字符 `Y` 和 `N` 组成的字符串 $s$，分别代表“是”和“否”。

你可以在 $s$ 上重复应用以下操作：
选择任意两个相邻字符，并将它们替换为它们的逻辑 OR。
具体来说，在每次操作中，你可以选择一个下标 $i$ $(1 \le i \le |s|-1)$，删除字符 $s_i$ 和 $s_{i+1}$，然后插入：
- 单个 `Y`，如果 $s_i$ 或 $s_{i+1}$ 中至少有一个是 `Y`；
- 单个 `N`，如果 $s_i$ 和 $s_{i+1}$ 都是 `N`。
请注意，每次操作后，$s$ 的长度会减少 $1$。

不幸的是，Fernando 不想让你组合“Yes OR Yes”，因为他经历过与某首歌相关的创伤。

请判断是否可能通过重复应用上述操作将 $s$ 缩减为单个字符，且在此过程中**从不**组合两个 `Y`。

## 输入格式
每个测试包含多个测试用例。第一行包含测试用例的数量 $t$ $(1 \le t \le 500)$。接下来是每个测试用例的描述。

每个测试用例只有一行，包含字符串 $s$ $(2 \le |s| \le 100)$。保证 $s_i$ 是 `Y` 或 `N`。

## 输出格式
对于每个测试用例，如果字符串可以通过重复应用所述操作缩减为单个字符，则打印“YES”，否则打印“NO”。

你可以以任意大小写形式输出答案（大写或小写）。例如，字符串 "yEs"、"yes"、"Yes" 和 "YES" 都将被识别为肯定回答。

## 样例输入
```
7
YY
NN
NNY
YYYNY
NNNNN
YYYYYY
YNNNNN
```

## 样例输出
```
NO
YES
YES
NO
YES
NO
YES
```

## 样例说明
在第一个测试用例中，你无法合并 $s_1$ 和 $s_2$，因为它们都是 `Y`。因此，答案是 NO。

在第三个测试用例中，以下是一个合法的操作序列：`NN–––Y` → `NY–––` → `Y`。因此，答案是 YES。

在第四个测试用例中，第一次操作有两种可能：`YYYN–––Y` → `YYYY` 或 `YYYNY–––` → `YYYY`。然而，在两种情况下，都无法在不组合两个 `Y` 的情况下执行更多操作。因此，答案是 NO。

在第五个测试用例中，以下是一个合法的操作序列：`NNN–––NN` → `NN–––NN` → `NNN–––` → `NN–––` → `N`。因此，答案是 YES。



```cpp
// Problem: CF 2178 A
// Contest: Codeforces - Good Bye 2025
// URL: https://codeforces.com/contest/2178/problem/A
// Memory Limit: 256 MB
// Time Limit: 1000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define ios ios::sync_with_stdio(false);cin.tie(nullptr);

void solve(){
	string s;
	cin>>s;
	if(s.find("YY")!=string::npos) printf("NO\n");
	else printf("YES\n");

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