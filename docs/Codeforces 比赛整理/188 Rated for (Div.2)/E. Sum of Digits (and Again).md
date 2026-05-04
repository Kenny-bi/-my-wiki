


## E. Sum of Digits (and Again)

时间限制：每个测试点2秒
内存限制：每个测试点512兆字节

## 问题描述

对于一个正整数（严格大于0） $x$，字符串 $S(x)$ 是通过以下过程形成的：

最初，这个字符串是空的；
然后，将数字 $x$ 的不带前导零的十进制表示形式追加到其右侧；
之后，如果 $x \le 9$，则过程结束。否则，将 $x$ 替换为 $x$ 的数字之和，并返回到步骤2。

例如：
$S(75)$ 是 75123；
$S(30)$ 是 303；
$S(9)$ 是 9。

给定一个由数字组成的字符串 $s$。你的任务是重新排列该字符串中的字符，使其成为某个数字 $x$ 的字符串 $S(x)$。不允许删除字符和/或添加新字符。如果给定的字符串 $s$ 已经是某个数字 $x$ 的字符串 $S(x)$，你可以将其保持不变。

## 输入格式

第一行包含一个整数 $t$ ($1 \le t \le 10^4$) — 测试用例的数量。

每个测试用例由包含 $s$ 的一个字符串组成 ($1 \le |s| \le 10^5$) — 一个数字序列。

输入的其他约束条件：
所有测试用例中 $s$ 的长度总和不超过 $10^5$；
保证可以重新排列 $s$ 中的数字，以得到某个正整数 $x$ 的 $S(x)$。

## 输出格式

对于每个测试用例，输出一个字符串 — 重新排列字符后的 $s$。如果有多个有效答案，输出其中任意一个。

## 样例输入

```
5
12735
1
011
99999299999999299959999999999999
4621467
```

## 样例输出

```
75123
1
101
99999999999999999999999999992529
6442167
```



# AI 思维引导
---

# 算法笔记：CF 2204E - 逆向推导与状态压缩 (Sum of Digits)

**标签**：#算法竞赛 #思维题 #数学/数论 #字符串 #暴力枚举 #降维打击

**难度**：Codeforces E 题 (2000+)

## 📝 题目模型抽象

**正向游戏规则**：给定一个数 $x_0$，把它的“各位数字之和”算出来拼在后面得到 $x_1$，如果 $x_1 > 9$ 则继续对 $x_1$ 求和得到 $x_2$ 拼在后面……直到最后得到一个个位数 $x_k$ 为止。生成的超长字符串记为 $S(x_0)$。

**逆向拼图任务**：给你一个长度最高达 $10^5$、被打乱的字符串碎块，要求你重新排列这些碎块，拼出一个合法的 $S(x_0)$。

---

## 🧠 核心破局思维 (The "Aha!" Moments)

面对这种“不知头、不知尾、不知过程”的巨型乱码，**永远去寻找打乱顺序也绝对不会改变的“不变量（锚点）”**。

### 💡 锚点 1：打乱顺序，不影响字符总和

既然碎片被打乱了，拼出 $x_0$ 的顺序不重要。因为只要碎片固定，拼出来的 $x_0$ 的**“各位数字之和”**是绝对固定的！

这意味着剩下的字符只要随便排（最高位不为0），合法性就不会被破坏。

### 💡 锚点 2：极其短小的“尾巴” (降维打击)

假设初始数字 $x_0$ 极度离谱，长达 $10^5$ 位且全都是 `9`。

它变身的第一步 $x_1 = 9 \times 10^5 = 900,000$。

**结论**：不管字符串多长、$x_0$ 多大，它身后的“尾巴” $x_1$ 撑死只有 $900,000$！

这意味着后面的变身序列 $x_1 \to x_2 \to \dots \to x_k$ 极其短暂，字符数量极少。**我们完全可以放弃猜测 $x_0$，直接暴力枚举 $x_1$！**

### 💡 锚点 3：神仙连环公式 (验证机制)

原字符串 $S(x_0)$ 是由 $[x_0] + [x_1] + \dots + [x_k]$ 拼接而成的。

设全字符串的**所有单一字符相加之和**为 $TotalSum$。

根据游戏规则：

- $x_0$ 的数字之和 $= x_1$
    
- $x_1$ 的数字之和 $= x_2$
    
- ...
    
- $x_{k-1}$ 的数字之和 $= x_k$
    
- $x_k$ 本身是个位数，它的数字之和 $= x_k$ 自身。
    

> [!important] 终极守恒定律
> 
> **$TotalSum = x_1 + x_2 + \dots + x_k + x_k$**
> 
> _(注：最后一定会多出一个 $x_k$，因为它既是上一步的和，自身也要被统计入字符串的总和中)_

---

## ⚙️ 算法执行步骤 ($O(N)$ 极简暴力)

把一个看似无从下手的 $10^5$ 级排列组合问题，降维成了一个只需要循环 $90$ 万次的微型暴力验证题。

1. **盘点家底**：
    
    - 遍历乱码字符串，统计数字 `0~9` 各有几个（存入数组 `cnt[10]`）。
        
    - 累加算出所有字符的真实数字总和 $TotalSum$。
        
2. **暴力枚举小尾巴 $x_1$**：
    
    - 写一个 `for (int x1 = 1; x1 <= 900000; x1++)` 循环。
        
3. **合法性双重验证**：
    
    - 对于猜的每一个 $x_1$，按照规则模拟生成它的尾巴序列 $x_1, x_2, \dots, x_k$。
        
    - **验证一**：这个尾巴序列是否满足神仙公式 `x1 + x2 + ... + xk + xk == TotalSum`？如果不满足，直接 `continue`。
        
    - **验证二**：拼出这个尾巴所需要的 `0~9` 的字符，我们的“家底 `cnt`”够不够扣除？不够也 `continue`。
        
4. **组装输出答案**：
    
    - 找到唯一的合法 $x_1$ 后，从家底中扣除尾巴用掉的字符。
        
    - 剩下的全部字符，就是 $x_0$ 的碎片！
        
    - 把剩下的碎片从大到小（或者随便怎么排，只要保证非0开头即可）排好拼成 $x_0$。
        
    - 将 $x_0$ 与尾巴拼接输出即可 AC！
        

---

## 🌟 认知升华 (Key Takeaways)

> [!quote]
> 
> 1. **看到“各位数字之和”**：条件反射想到 $O(1)$ 的“同余定理（模9）”，以及“极速缩水”特性（最大不超过 $9 \times 长度$）。
>     
> 2. **看到“乱序拼接/重排”**：条件反射想到“总和不变”、“频率统计不变”，放弃穷举排列组合，去寻找全局宏观的数学不变量。
>     
> 3. **状态分离**：庞大未知的主体（$x_0$） + 极小的衍生客体（尾巴）。放弃主体，直接去枚举客体，再用客体反向校验主体。
>


# 过样例代码
```cpp
// Problem: CF 2204 E
// Contest: Codeforces - Educational Codeforces Round 188 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2204/problem/E
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;

int fun(int x){
	int ans=0;
	while(x){
		ans+=x%10;
		x/=10;
	}
	return ans;
}

void add(int x,vector<int>& a){
	while(x){
		a[x%10]++;
		x/=10;
	}
}

int check(vector<int>& s,vector<int>& p){
	for(int i=0;i<10;i++){
		if(p[i]>s[i]) return 0;
	}
	return 1;
}

void solve(){
	string s;
	cin>>s;
	int sum=0;
	vector<int> num(20,0);
	
	if(s.size()<=1){
		cout<<s<<endl;
		return ;
	}
	
	
	for(int i=0;i<s.size();i++){
		sum+=s[i]-'0';
		num[s[i]-'0']++;
	}
	
	for(int i=1;i<=300000;i++){
		vector<int> need(20,0);
		int ans=0,p=i;
		while(p>9){
			ans+=p;
			add(p,need);
			p=fun(p);
		}
		ans+=p*2;
		add(p,need);
		
		if(ans==sum && check(num,need) ){
			for(int j=9;j>=0;j--){
				while(num[j]>need[j]){
					cout<<j;
					num[j]--;
				}
			}
			int t=i;
			while(t>9){
				cout<<t;
				t=fun(t);
			}
			cout<<t<<endl;
			
			break;
		}
	}
	
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








# AC 代码
- 构造的第二个数必须循环到足够大，保险且效率快的方式是循环到总和
```cpp
// Problem: CF 2204 E
// Contest: Codeforces - Educational Codeforces Round 188 (Rated for Div. 2)
// URL: https://codeforces.com/contest/2204/problem/E
// Memory Limit: 512 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;

int fun(int x){
	int ans=0;
	while(x){
		ans+=x%10;
		x/=10;
	}
	return ans;
}

void add(int x,vector<int>& a){
	while(x){
		a[x%10]++;
		x/=10;
	}
}

int check(vector<int>& s,vector<int>& p){
	for(int i=0;i<10;i++){
		if(p[i]>s[i]) return 0;
	}
	return 1;
}

void solve(){
	string s;
	cin>>s;
	int sum=0;
	vector<int> num(20,0);
	
	if(s.size()<=1){
		cout<<s<<endl;
		return ;
	}
	
	
	for(int i=0;i<s.size();i++){
		sum+=s[i]-'0';
		num[s[i]-'0']++;
	}
	
	for(int i=1;i<=sum;i++){
		vector<int> need(20,0);
		int ans=0,p=i;
		while(p>9){
			ans+=p;
			add(p,need);
			p=fun(p);
		}
		ans+=p*2;
		add(p,need);
		
		if(ans==sum && check(num,need) ){
			for(int j=9;j>=0;j--){
				while(num[j]>need[j]){
					cout<<j;
					num[j]--;
				}
			}
			int t=i;
			while(t>9){
				cout<<t;
				t=fun(t);
			}
			cout<<t<<endl;
			
			break;
		}
	}
	
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