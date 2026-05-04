



## D. Table Cut
时间限制：每个测试点 2 秒
内存限制：每个测试点 256 MB

## 问题描述
给定一个大小为 $n \times m$ 的表格，其中每个单元格包含 $0$ 或 $1$。任务是通过一个从左上角到右下角的切割线将其分成两部分。切割线只能向右或向下移动。

设 $a$ 为切割后表格一部分中 $1$ 的数量，$b$ 为表格另一部分中 $1$ 的数量。目标是最大化 $a \cdot b$ 的值。

## 输入格式
每个测试包含多个测试用例。第一行包含测试用例的数量 $t$ $(1 \le t \le 10^4)$。接下来是测试用例的描述。

每个测试用例的第一行包含两个整数 $n$ 和 $m$ $(1 \le n, m \le 3 \cdot 10^5$, $2 \le n \cdot m \le 3 \cdot 10^5)$ —— 分别表示表格的行数和列数。

接下来的 $n$ 行中，每行包含 $m$ 个整数，其中第 $i$ 行的第 $j$ 个数对应值 $a_{i,j}$ $(0 \le a_{i,j} \le 1)$。

保证所有测试用例的 $n \cdot m$ 值之和不超过 $3 \cdot 10^5$。

## 输出格式
对于每个测试用例，在输出数据的第一行输出一个数字 —— 乘积的最大值。

在第二行，输出一个由 $n$ 个字符 'D' 和 $m$ 个字符 'R' 组成的字符串，表示下一步切割的方向，其中 'D' 表示向下切割，'R' 表示向右切割。

## 样例输入
```
3
5 5
1 0 1 1 0
0 1 0 1 1
1 0 1 0 0
0 1 0 1 0
0 0 0 0 1
5 4
0 0 1 0
0 1 1 1
1 0 0 1
0 1 0 1
0 0 1 0
3 2
1 0
0 1
1 1
```

## 样例输出
```
30
RDRDRDRDDR
20
DRRDRDDDR
4
DRDRD
```



**Note**

The images show the correct cuts for each of the first and second test cases, at which the maximum value of the product is achieved.

![](https://espresso.codeforces.com/bf5a95bd1bfc341b05232d999b0048817fc8b08f.png) ![](https://espresso.codeforces.com/9e50e43cf6573882647584be3bda8171ba34d3f0.png)











# 如此凑数居然不对么，奇怪
```cpp
// Problem: CF 2194 D
// Contest: Codeforces - Codeforces Round 1078 (Div. 2)
// URL: https://codeforces.com/contest/2194/problem/D
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;

void solve(){
	int n,m;
	cin>>n>>m;
	vector< vector<int> > a(n+3,vector<int> (m+2,0));
	int cnt=0;
	for(int i=0;i<n;i++){
		for(int j=0;j<m;j++){
			cin>>a[i][j];
			if(a[i][j]) cnt++;
		}
	}
 	
 	int t=cnt/2;
 	cout<<t*(cnt-t)<<endl;
 	int x=0,y=0;
 	int cur=0;
 	for(int i=0;i<n;i++){
 		for(int j=m-1;j>=0;j--){
 			if(a[i][j]) cur++;
 			if(cur==t){
 				x=i+1,y=j+1;
 				break;
 			}
 		}
 		if(cur==t) break;
 	}
	//cout<<x<<" "<<y<<endl;
	
	
	for(int i=1;i<x;i++) cout<<"D";
	for(int i=1;i<y;i++) cout<<"R";
	cout<<"D";
	for(int i=1;i<=m-y+1;i++) cout<<"R";
	for(int i=1;i<=n-x;i++) cout<<"D";
	cout<<endl;
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





# 原来是这样
- 之前那一份代码无法处理0  的情况，我们需要反过来，将较大的数字作为cur
- 之后又错了一次，是因为爆int   了，此事已经不知道犯过多少次了
```cpp
// Problem: CF 2194 D
// Contest: Codeforces - Codeforces Round 1078 (Div. 2)
// URL: https://codeforces.com/contest/2194/problem/D
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;
#define int long long

void solve(){
	int n,m;
	cin>>n>>m;
	vector< vector<int> > a(n+3,vector<int> (m+2,0));
	int cnt=0;
	for(int i=0;i<n;i++){
		for(int j=0;j<m;j++){
			cin>>a[i][j];
			if(a[i][j]) cnt++;
		}
	}
 	
 	int t=cnt/2;
 	cout<<t*(cnt-t)<<endl;
 	int x=0,y=0;
 	int cur=0;
 	for(int i=0;i<n;i++){
 		for(int j=m-1;j>=0;j--){
 			if(a[i][j]) cur++;
 			if(cur==cnt-t){
 				x=i+1,y=j+1;
 				break;
 			}
 		}
 		if(cur==cnt-t) break;
 	}
	//cout<<x<<" "<<y<<endl;
	
	
	for(int i=1;i<x;i++) cout<<"D";
	for(int i=1;i<y;i++) cout<<"R";
	cout<<"D";
	for(int i=1;i<=m-y+1;i++) cout<<"R";
	for(int i=1;i<=n-x;i++) cout<<"D";
	cout<<endl;
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










# 📝 算法笔记：CF 2194D - Table Cut

### 1. 问题核心

**目标**：在一个 $n \times m$ 的 01 矩阵中，画一条只能“向下 (D)”或“向右 (R)”的切割线，将矩阵分为两部分。使得两部分中 `1` 的数量乘积 $a \cdot b$ 最大。

**约束**：$n, m \le 3 \cdot 10^5$，时间限制 2s。

### 2. 核心思维

**数学直觉**：

设总 `1` 的数量为 $S$。我们要最大化 $x \cdot (S - x)$，根据二次函数性质，当 $x$ 越接近 $S/2$ 时，乘积越大。

**几何构造（介值定理）**：

是否存在一条路径，能够恰好圈出任意数量 $k$ ($0 \le k \le S$) 的 `1`？

- **结论**：是的。
    
- **证明思路**：
    
    - 如果我们贴着最左和最下走，圈出的右上角区域包含 0 个 `1`。
        
    - 如果我们贴着最上和最右走，圈出的右上角区域包含 $S$ 个 `1`。
        
    - 每次我们将路径上的一个“拐角”从 $RD$ 变为 $DR$（即把一个格子从路径上方移到路径下方），右上角区域的 `1` 的数量变化量只能是 $0$ 或 $1$。
        
    - 因此，我们可以取到 $0$ 到 $S$ 之间的**任意整数**。
        

**贪心策略（扫描法）**：

虽然我们可以通过移动路径来微调，但更简单的实现方式是**固定扫描顺序**。

我们按照**“先拿上面的行，每行从右往左拿”**的顺序（即 `Top-Row -> Right-to-Left`）来收集 `1`。这种顺序收集出来的集合，天然构成一个“倒阶梯形”，一定可以用合法的路径圈出来。

### 3. 算法流程

1. **统计总数**：遍历矩阵，计算 `1` 的总数 `cnt`。
    
2. **确定目标**：计算目标数量 `target`。
    
    - 为了防止 `cnt=1` 时 `cnt/2=0` 的边界问题，建议取**较大的一半**，即 `target = cnt - cnt/2` 或 `(cnt + 1) / 2`。
        
3. **寻找分割点**：
    
    - 双重循环遍历：`i` 从 $0 \to n-1$，`j` 从 $m-1 \to 0$。
        
    - 累加遇到的 `1`。
        
    - 当累加值等于 `target` 时，记录当前坐标 $(x, y)$ 并退出。
        
4. **构造路径**：
    
    - 目标是把 $(0,0)$ 到 $(x, y)$ 以及 $(x, y)$ 右边的所有格子划入右上角。
        
    - 路径走法：
        
        1. 先向下走 $x$ 步（到达第 $x$ 行）。
            
        2. 再向右走 $y$ 步（到达第 $y$ 列）。
            
        3. **向下走 1 步**（关键：这一步把 $(x,y)$ 这个格子“关”进了右上角的区域）。
            
        4. 走完剩余的向右步数。
            
        5. 走完剩余的向下步数。
            
5. **计算答案**：输出 `target * (cnt - target)`。注意使用 `long long`。
    

### 4. 踩坑记录 (Debug Log)

- **坑点 1：边界情况 `cnt=1`**
    
    - **错误逻辑**：`target = cnt / 2`。当 `cnt=1` 时，`target=0`。如果代码逻辑是“找到第 0 个 1 就停止”，但循环里是先判断 `if(a[i][j]) cur++` 再判断 `== target`，会导致 `cur` 直接变成 1，永远无法命中 0。
        
    - **修正**：取 `target = cnt - cnt/2`（向上取整的效果），保证 `target >= 1`（除非总数本来就是0）。
        
- **坑点 2：数据溢出**
    
    - **现象**：`n, m` 只有 $3 \cdot 10^5$，但 `cnt` 接近 $3 \cdot 10^5$ 时，积可以达到 $2.25 \cdot 10^{10}$，超出了 `int` 范围（约 $2 \cdot 10^9$）。
        
    - **修正**：使用 `#define int long long` 或显式定义 `long long ans`。
        

### 5. 标准代码模板 (AC Code)

C++

```
#include<bits/stdc++.h>
using namespace std;
#define int long long // 1. 防溢出

void solve(){
    int n, m;
    cin >> n >> m;
    // 使用 padding 防止越界，虽非必须但好习惯
    vector<vector<int>> a(n, vector<int>(m));
    int cnt = 0;
    for(int i = 0; i < n; i++){
        for(int j = 0; j < m; j++){
            cin >> a[i][j];
            if(a[i][j]) cnt++;
        }
    }
    
    // 2. 核心修正：取较大的一半，避免找 0 的情况
    int t = cnt - cnt / 2; 
    
    cout << t * (cnt - t) << endl;
    
    // 特判全0情况，随便输出一条路径
    if(cnt == 0) {
        for(int i=0; i<n; i++) cout << "D";
        for(int i=0; i<m; i++) cout << "R";
        cout << endl;
        return;
    }

    int cur = 0;
    int x = -1, y = -1;
    
    // 3. 扫描顺序：行优先，列倒序
    for(int i = 0; i < n; i++){
        for(int j = m - 1; j >= 0; j--){
            if(a[i][j]) cur++;
            if(cur == t){
                x = i; // 记录 0-indexed 坐标
                y = j;
                break;
            }
        }
        if(x != -1) break;
    }
    
    // 4. 路径构造：
    // 目标：将 (x,y) 包含在右上区域
    // 动作：先走到 (x,y) 的左上角，然后从它左边切下去
    
    // 输出 x 个 D (到达第 x 行)
    for(int i = 0; i < x; i++) cout << "D";
    // 输出 y 个 R (到达第 y 列)
    for(int i = 0; i < y; i++) cout << "R";
    // 关键一刀：向下，把 (x,y) 划给右上
    cout << "D";
    // 补齐剩下的 R
    for(int i = 0; i < m - 1 - y + 1; i++) cout << "R"; // 注意这里的数量逻辑
    // 实际上更简单的写法是：
    // string ans = string(x, 'D') + string(y, 'R') + "D" + string(m - y, 'R') + string(n - 1 - x, 'D');
    
    // 你的循环写法修正版：
    // m - (y + 1) 是剩下没走的列，加上刚才那个 'D' 导致我们还在第 y 列，
    // 其实这里只要保证总数是对的：
    // R 的总数是 m, D 的总数是 n
    // 刚才输出了 x 个 D, y 个 R, 1 个 D
    // 还差 m - y 个 R
    for(int i = 0; i < m - y; i++) cout << "R";
    // 还差 n - 1 - x 个 D
    for(int i = 0; i < n - 1 - x; i++) cout << "D";
    
    cout << endl;
}

signed main(){
    ios::sync_with_stdio(false);
    cin.tie(0);
    int t;
    cin >> t;
    while(t--){
        solve();
    }
    return 0;
}
```