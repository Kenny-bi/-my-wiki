[[分类讨论]]
[[数学]]



## B. Tab Closing
时间限制：每测试点 1.5 秒  
内存限制：256 MB

## 问题描述
你盯着电脑屏幕太久了，是时候休息一下去活动活动了。

你的屏幕是一条长度为 $a$ 的线段，上面显示着 $n$ 个标签页。你希望通过点击每个标签页右端点的 "x" 来关闭所有标签页。

每个标签页的长度为 $len = \min(b, \frac{a}{m})$，其中 $m$ 是当前剩余的标签页数量。这些标签页总是从屏幕左端点开始紧密排列，也就是说，"x" 的位置分别在距离左端点 $len, 2 \cdot len, 3 \cdot len, \dots, m \cdot len$ 单位处。请注意，当你关闭标签页时，每个标签页的长度会发生变化。

你的光标初始位于屏幕的左端点。你想知道关闭所有标签页所需的最少鼠标移动次数是多少。

## 输入格式
每个测试包含多个测试用例。第一行包含测试用例数量 $t$（$1 \leq t \leq 10^4$）。接下来是每个测试用例的描述。

每个测试用例包含一行三个整数 $a$, $b$, $n$（$1 \leq b \leq a \leq 10^9$, $1 \leq n \leq 10^9$）。

## 输出格式
对于每个测试用例，输出一个整数 —— 关闭所有标签页所需的最少鼠标移动次数。

## 样例输入
```
12
8 1 6
9 6 2
10 3 1
10 1 10
9 2 1
5 5 6
6 2 7
9 1 9
3 2 6
8 1 7
8 1 9
8 2 4
```

## 样例输出
```
1
2
1
1
1
1
2
1
2
1
2
1
```

## 样例说明
- 第一个测试用例中，可能的操作是将光标移动到位置 1 并点击 6 次。
- 第二个测试用例中，可能的操作是先将光标移动到位置 4.5 点击一次，然后将光标移动到位置 6 再点击一次。可以证明无法用少于 2 次移动完成。


# 赛时AC代码
```cpp
// Problem: CF 2166 B
// Contest: Codeforces - Codeforces Round 1064 (Div. 2)
// URL: https://codeforces.com/contest/2166/problem/B
// Memory Limit: 256 MB
// Time Limit: 1500 ms
// 
// Powered by CP Editor (https://cpeditor.org)


#include<bits/stdc++.h>
using namespace std;
typedef long long LL;
typedef pair<int,int> PII;

void solve(){
	double a,b,n;
	cin>>a>>b>>n;
	if(b*n<=a||b>=a) printf("1\n");
	else printf("2\n");
	
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
```