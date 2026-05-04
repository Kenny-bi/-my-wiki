[[分类讨论]]

[[隐藏很深的逻辑错误]]
## B. Drifting Away
时间限制：每个测试 2 秒
内存限制：每个测试 512 兆字节

## 问题描述
在 Monocarp 的房子前有一条河流，可以表示为一排单元格。在某些单元格中有强水流，而在其他单元格中没有水流。这可以用一个字符串 $s$ 表示，包含以下字符：

- 小于号 ('<') —— 向左的水流；
- 大于号 ('>') —— 向右的水流；
- 星号 (' *  ') —— 没有水流。

最初，Monocarp 选择一个单元格开始他的河流之旅。

如果 Monocarp 当前所在的单元格有水流，他会被冲到水流方向的相邻单元格。如果没有相邻单元格（即在第 1 个单元格有向左的水流，或在第 $n$ 个单元格有向右的水流），Monocarp 会上岸。每次移动花费 1 分钟。

如果 Monocarp 当前所在的单元格没有水流，他可以向左或向右划到相邻单元格。如果 Monocarp 决定划向的方向没有相邻单元格，他会上岸。每次移动同样花费 1 分钟。

Monocarp 希望在河流上航行尽可能长的时间。如果 Monocarp 可以无限航行，输出 $-1$。否则，输出 Monocarp 在上岸前能在河流上航行的最长时间。

## 输入格式
第一行包含一个整数 $t$（$1 \le t \le 10^4$）——测试用例的数量。

每个测试用例只有一行，包含一个字符串 $s$（$1 \le |s| \le 3 \cdot 10^5$），仅由字符 '<'（向左水流）、'>'（向右水流）、'*'（无水流）组成。它们的 ASCII 码分别是 60、62 和 42。

输入数据的额外约束：所有测试用例的字符串 $s$ 总长度不超过 $3 \cdot 10^5$。

## 输出格式
对于每个测试用例，输出一个整数：
- 如果 Monocarp 可以无限航行，输出 $-1$；
- 否则输出 Monocarp 能在河流上航行的最长时间。

## 样例输入
```
4
*****
<<<>
>*<
*

```

## 样例输出
```
-1
3
-1
1
```





# 错误原因
你离正确答案非常近了！你添加 `*<` 和 `>*` 检查是完全正确的。

你现在的问题出在你**保留了**之前那个不完备的 `l < r` 检查，这个检查现在反而成了 bug 的来源。

---

### 🐞 Bug 分析

问题在这行代码：

C++

```
if(l<r) flag=1;
```

`l` 是**第一个** `>` 的索引，`r` 是**最后一个** `<` 的索引。

让我们用一个简单的反例来测试它： `s = "<"`

1. 你的代码开始运行 `solve()`。
    
2. `flag = 0`。
    
3. `**` 循环：不运行 (s.size() == 1)。
    
4. `l` 循环：没有 `>`，所以 `l` 保持为 **-1**。
    
5. `r` 循环：`s[0]` 是 `<`，所以 `r` 变为 **0**。
    
6. **执行 `if(l < r) flag = 1;`**
    
7. `l` 是 -1，`r` 是 0。
    
8. `-1 < 0` 是 **true**。
    
9. `flag` 被错误地设置成了 `1`。
    
10. 你的代码最后打印出 **-1**（无限循环）。
    

然而，对于 `s = "<"`，正确的答案应该是 **1**（从 `s[0]` 开始，被推到岸上，耗时 1）。

你那个 `l < r` 的检查，本意是想捕捉 `><` 这样的情况，但它错误地将 `l = -1` (没有 `>`) 且 `r` 合法 (有 `<`) 的情况也判断为无限循环了。

---

### 💡 如何修复

你有两个选择，第一个更简单，第二个更规范：

#### 方案一：修正 `l < r` 检查

你只需要确保 `l` 不是 -1 就行了：

C++

```
// 错误的代码
if(l<r) flag=1;

// 修正后的代码
if(l != -1 && l < r) flag=1;
```

这样，当 `s = "<"` 时，`l != -1` 为 false，检查就不会通过。

#### 方案二：彻底的邻接检查（推荐）

你为 `**`、`*<`、`>*` 做了邻接检查。无限循环的第四种情况是 `><`。你根本不需要 `l` 和 `r` 循环，你只需要把 `><` 也加入你的邻接检查循环就行了。

这个方案更清晰、更不容易出错。

**最终的正确代码（只修改 `solve` 函数）：**

C++

```
void solve() {
    string s;
    cin>>s;
    int flag=0;
    
    // 统一检查所有4种无限循环的邻接模式
    for(int i=1;i<(int)s.size();i++){
    	if(s[i-1]=='*'&&s[i]=='*') flag=1;
    	if(s[i-1]=='*'&&s[i]=='<') flag=1;
    	if(s[i-1]=='>'&&s[i]=='*') flag=1;
    	if(s[i-1]=='>'&&s[i]=='<') flag=1; // <-- 添加这个
    	if(flag) break; // 找到一个就可以退出了
    }
    
    // (删除所有关于 l 和 r 的循环)
    
    if(flag) printf("-1\n");
    else{
        // 你的 else 块逻辑是完全正确的
    	int n = (int)s.size();
    	int ans=0;
    	for(int i=0;i<n;i++){
    		if(s[i]=='>') break;
    		else ans++;
    	}
    	int res=0;
    	for(int i=n-1;i>=0;i--){
    		if(s[i]=='<') break;
    		else res++;
    	}
    	ans=max(ans,res);
    	printf("%d\n",ans);
    }
    
    return;
}
```


# 赛时AC代码
```cpp
// Problem: CF 2169 B
// Contest: Codeforces - Educational Codeforces Round 184 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2169/problem/B
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include <bits/stdc++.h>
using namespace std;
typedef long long LL;
typedef pair<LL,LL> PII;


void solve() {
    string s;
    cin>>s;
    int flag=0;
//     
    // for(int i=1;i<(int)s.size();i++){
    	// if(s[i]=='*'&&s[i-1]=='*'){
    		// flag=1;
    		// break;
    	// }
    // }
//     
    // int l=-1,r=-1;
    // for(int i=0;i<(int)s.size();i++){
    	// if(s[i]=='>'){
    		// l=i;
    		// break;
    	// }
    // }
    // for(int i=s.size()-1;i>=0;i--){
    	// if(s[i]=='<'){
    		// r=i;
    		// break;
    	// }
    // }
    // if(l<r) flag=1;
    
    for(int i=1;i<s.size();i++){
    	if(s[i-1]=='*'&&s[i]=='<') flag=1;
    	if(s[i-1]=='>'&&s[i]=='*') flag=1;
    	if(s[i-1]=='*'&&s[i]=='*') flag=1;
    	if(s[i-1]=='>'&&s[i]=='<') flag=1;
    }
    
    
    if(flag) printf("-1\n");
    else{
    	int n = (int)s.size();
    	int ans=0;
    	for(int i=0;i<n;i++){
    		if(s[i]=='>') break;
    		else ans++;
    	}
    	int res=0;
    	for(int i=n-1;i>=0;i--){
    		if(s[i]=='<') break;
    		else res++;
    	}
    	ans=max(ans,res);
    	printf("%d\n",ans);
    }
    
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




# CF题解思路
- 我们来简化问题——假设字符串只包含字符 '<' 和 '>'。

我们先来弄清楚答案何时为 $-1$。如果字符串中某处存在子串 "><"，那么答案肯定是 $-1$，因为 Monocarp 可以从这些格子中的一个开始，并在它们之间无限地来回航行。

如果这样的子串不存在，那么所有的 '<' 字符都在所有的 '>' 字符之前，这意味着字符串看起来像 <<...<<>>...>>。显然，无论 Monocarp 从哪里开始，他最终都会上岸。为了尽可能长时间地航行，Monocarp 可以穿过所有向左的水流格子，或者穿过所有向右的水流格子。因此，答案是它们数量的最大值。

如果字符串中有 '*' 字符，如何解决问题？题目陈述指出，Monocarp 每次落在一个星号上时，可以选择划船的方向。然而，应该很清楚，如果 Monocarp 访问了一个星号，选择了一个方向，然后又再次返回该星号，那么选择相同的方向是最优的。如果对所有访问过的星号都这样做，那么这个循环将是无限的。因此，我们需要用 '<' 或 '>' 替换所有的 '*'，以使得没有 '*' 时问题的答案最大化。

所以，如果存在相邻的符号可以形成 "><"，那么答案是 $-1$。如果这样的配对不存在，字符串会是什么样子？显然，字符串中没有两个连续的 '*' 字符。然而，可以做出一个更强的断言——字符串中最多只能有一个 '*'。我们来证明这一点。考虑两个星号，中间有一些箭头。首先，有向左的箭头，然后有向右的箭头。如果至少有一个向左的箭头，那么左边的星号可以替换为 '>'。如果至少有一个向右的箭头，那么右边的星号可以替换为 '<'。因此，中间应该没有任何箭头，但那样的话星号就是相邻的，并且它们可以被替换为 "><"。

因此，字符串可能看起来像 <<...<<>>...>> 或者 <<...<<*>>...>>。在第二种情况下，星号可以被替换为出现次数更多的那个箭头。因此，为了解决这个问题，只需计算每种箭头的数量即可。

总体复杂度：每个测试用例 $O(|s|)$。



# CF题解代码
```python
for _ in range(int(input())):
    s = input()
    for i in range(len(s) - 1):
        if s[i] != '<' and s[i + 1] != '>':
            print(-1)
            break
    else:
        print(len(s) - min(s.count('<'), s.count('>')))
```
```cpp
#include <iostream>
#include <string>
using namespace std;

int main() {
    int t;
    cin >> t;
    while (t--) {
        string s;
        cin >> s;
        bool found = false;
        for (int i = 0; i < s.length() - 1; i++) {
            if (s[i] != '<' && s[i + 1] != '>') {
                cout << -1 << endl;
                found = true;
                break;
            }
        }
        if (!found) {
            int count_less = 0, count_greater = 0;
            for (char c : s) {
                if (c == '<') count_less++;
                else if (c == '>') count_greater++;
            }
            cout << s.length() - min(count_less, count_greater) << endl;
        }
    }
    return 0;
}
```