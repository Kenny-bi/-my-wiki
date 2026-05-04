


## D. Simons and Beating Peaks

时间限制：每个测试点2秒
内存限制：每个测试点512兆字节

我最美好的愿望消散在空气中；十八岁，我把梦想倾注进麦克风里分享。

— 舜, 《720》

我们称长度为 $m$ 的数组 $b$ 是 cool 的，当且仅当：

不存在索引 $i$ ($1 < i < m$) 使得 $b_i = \max(\{b_{i-1}, b_i, b_{i+1}\})$。

西蒙斯有一个大小为 $n$ 的数组 $a$。最初，该数组是一个排列*。

他必须重复执行以下操作，直到数组变得 cool：

选择一个索引 $i$ ($1 < i < n$) 使得 $a_i = \max(\{a_{i-1}, a_i, a_{i+1}\})$。

然后，他可以删除 $a_{i-1}$ 或 $a_{i+1}$ 中的一个。删除后，数组中会出现一个空隙，空隙的左右两侧将重新连接在一起。

找出西蒙斯所需执行的最少操作次数。

*长度为 $n$ 的排列是由 $n$ 个从 $1$ 到 $n$ 的不同整数按任意顺序组成的数组。例如，$[2,3,1,5,4]$ 是一个排列，但 $[1,2,2]$ 不是排列（$2$ 在数组中出现了两次），$[1,3,4]$ 也不是排列（$n=3$ 但数组中却有 $4$）。

## 输入格式

每个测试包含多个测试用例。第一行包含测试用例的数量 $t$ ($1 \le t \le 5 \cdot 10^4$)。测试用例的描述如下。

每个测试用例的第一行包含一个整数 $n$ ($3 \le n \le 5 \cdot 10^5$) — 数组 $a$ 的大小。

第二行包含 $n$ 个整数 $a_1, a_2, \dots, a_n$ ($1 \le a_i \le n$，所有 $a_i$ 互不相同) — 西蒙斯最初的数组。

保证所有测试用例的 $n$ 之和不超过 $5 \cdot 10^5$。

## 输出格式

对于每个测试用例，输出一个整数 — 西蒙斯所需执行的最少操作次数。

## 样例输入

```
5
3
1 2 3
5
4 1 3 2 5
6
4 5 3 6 2 1
7
6 5 1 7 4 2 3
15
7 4 10 12 9 14 5 3 8 11 1 15 2 13 6
```

## 样例输出

```
0
1
3
3
9
```

## 样例说明

在第一个测试用例中，数组初始就是 cool 的，所以西蒙斯无法执行任何操作。答案是 $0$。

在第二个测试用例中，西蒙斯可以按如下方式操作：

选择索引 $3$，因为 $a_3 = \max(\{a_2, a_3, a_4\})$。然后，他从数组中删除 $a_2$。数组变为 $[4,3,2,5]$。
我们可以看到现在数组已经 cool 了。因此，答案是 $1$。

在第三个测试用例中，西蒙斯可以按如下方式操作：

选择索引 $2$。然后，他从数组中删除 $a_1$。数组变为 $[5,3,6,2,1]$。
选择索引 $3$。然后，他从数组中删除 $a_2$。数组变为 $[5,6,2,1]$。
选择索引 $2$。然后，他从数组中删除 $a_1$。数组变为 $[6,2,1]$。
因此，西蒙斯用 $3$ 次操作使数组变得 cool。

## 题解

### 思路

**Cool 数组的结构**：只能是单调递增、单调递减或 V 形（先减后增）。因为一旦序列开始上升就必须继续上升，否则转折点会成为峰。

**关键洞察**：
- 全局最大值 n 不能被直接删除（操作只能删峰的邻居，n 是峰）
- n 必须在最终数组的端点（否则它是峰）

**转化**：找最长子序列，包含 n，n 在端点，子序列是 V 形的。

**答案** = `n - 1 - max(L_left, L_right)`

### 代码

```cpp
#include <bits/stdc++.h>
using namespace std;

// 计算最长 V 形（先减后增）子序列长度
int longestVShape(const vector<int>& a) {
    if (a.empty()) return 0;
    int n = a.size();

    // dec[i] = 以 a[i] 结尾的最长递减子序列长度
    vector<int> dec(n);
    vector<int> minTail;
    for (int i = 0; i < n; i++) {
        int pos = upper_bound(minTail.begin(), minTail.end(), a[i], greater<int>()) - minTail.begin();
        if (pos == minTail.size()) minTail.push_back(a[i]);
        else minTail[pos] = a[i];
        dec[i] = pos + 1;
    }

    // inc[i] = 以 a[i] 开头的最长递增子序列长度
    vector<int> inc(n);
    minTail.clear();
    for (int i = n - 1; i >= 0; i--) {
        int pos = lower_bound(minTail.begin(), minTail.end(), a[i]) - minTail.begin();
        if (pos == minTail.size()) minTail.push_back(a[i]);
        else minTail[pos] = a[i];
        inc[i] = pos + 1;
    }

    int result = 0;
    for (int i = 0; i < n; i++) {
        result = max(result, dec[i] + inc[i] - 1);
    }
    return result;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int t;
    cin >> t;

    while (t--) {
        int n;
        cin >> n;
        vector<int> a(n);
        int maxPos = 0;
        for (int i = 0; i < n; i++) {
            cin >> a[i];
            if (a[i] > a[maxPos]) maxPos = i;
        }

        vector<int> left(a.begin(), a.begin() + maxPos);
        vector<int> right(a.begin() + maxPos + 1, a.end());

        int L_left = longestVShape(left);
        int L_right = longestVShape(right);

        cout << n - 1 - max(L_left, L_right) << '\n';
    }

    return 0;
}
```

