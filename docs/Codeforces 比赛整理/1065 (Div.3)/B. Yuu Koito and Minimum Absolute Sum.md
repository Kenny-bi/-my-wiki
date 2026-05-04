---
created: 2025-11-22 11:50
tags:
  - algorithm/差分
  - difficulty/待定
  - status/AC
  - site/Codeforces
---



**题目链接**: [](<% tp.file.cursor(1) %>) 
**核心考点**: [[差分]]

---




## B. Yuu Koito and Minimum Absolute Sum
时间限制：每测试点2秒  
内存限制：每测试点256 MB  

## 问题描述
少女漫画和情歌中的话语……它们总是闪闪发光。我不需要字典就能理解它们的含义……但我从未亲身感受过。  
— Yuu Koito  

Yuu正在尝试加入学生会！不幸的是，她被迫做一些文书工作……Touko想让她填写各种学生会文件中的空白。  

您将得到一个部分填充的非负整数数组 $a_1,a_2,\ldots,a_n$，其中空白元素用 $-1$ 表示。您需要用非负整数填充空白元素，使得其差分数组中元素之和的绝对值最小。  

更正式地说，设 $b$ 是长度为 $n-1$ 的数组，使得 $b_i = a_{i+1} - a_i$ 对所有 $1 \leq i \leq n-1$ 成立。在填充 $a$ 空白元素的所有可能方法中，找出 $|b_1 + b_2 + \cdots + b_{n-1}|$ 的最小可能值。  

此外，输出达到此最小值的数组。如果有多个这样的数组，则输出字典序最小的那个$^*$。  

$^*$对于两个任意长度为 $n$ 的数组 $c$ 和 $d$，如果存在索引 $i$ ($1 \leq i \leq n$) 使得对所有 $j < i$ 有 $c_j = d_j$，且 $c_i < d_i$，则我们说 $c$ 在字典序上小于 $d$。换句话说，$c$ 和 $d$ 在至少一个索引上不同，且在它们不同的第一个索引上，$c_i$ 小于 $d_i$。

## 输入格式
第一行包含一个整数 $t$ ($1 \leq t \leq 10^4$) — 测试用例的数量。  

每个测试用例的第一行包含一个整数 $n$ ($2 \leq n \leq 2 \cdot 10^5$)。  

每个测试用例的第二行包含 $n$ 个整数 $a_1, a_2, \ldots, a_n$ ($-1 \leq a_i \leq 10^6$)。  

保证所有测试用例的 $n$ 之和不超过 $2 \cdot 10^5$。

## 输出格式
对于每个测试用例，在第一行输出 $|b_1 + b_2 + \cdots + b_{n-1}|$ 的最小可能值。  
在第二行输出 $n$ 个整数，即达到此最小值的字典序最小的数组 $a_1, a_2, \ldots, a_n$ 的值。

## 样例输入
```
6
4
2 -1 7 1
4
-1 2 4 -1
8
2 -1 1 5 11 12 1 -1
3
-1 -1 -1
3
2 5 4
2
-1 5
```

## 样例输出
```
1
2 0 7 1
0
0 2 4 0
0
2 0 1 5 11 12 1 2
0
0 0 0
2
2 5 4
0
5 5
```

## 样例说明
在第一个样例中，我们填充数组 $a = [2, 0, 7, 1]$，得到差分数组 $b = [-2, 7, -6]$。  

$b$ 中元素之和的绝对值为 $1$。可以证明这是最小的可能值。此外，可以证明这是达到该最小值的字典序最小的数组 $a$。




# 赛时AC代码
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
	for(int i=1;i<=n;i++){
		cin>>a[i];
	}
	
	if(a[1]==-1||a[n]==-1){
		if(a[1]==-1&&a[n]==-1){
			a[1]=0,a[n]=0;
			printf("0\n");
			for(int i=1;i<=n;i++){
				if(a[i]==-1) a[i]=0;
				printf("%d ",a[i]);
			}
			printf("\n");
		}else{
			if(a[1]==-1) a[1]=a[n];
			if(a[n]==-1) a[n]=a[1];
			printf("0\n");
			for(int i=1;i<=n;i++){
				if(a[i]==-1) a[i]=0;
				printf("%d ",a[i]);
			}
			printf("\n");
			
		}
		
	}else{
		printf("%d\n",abs(a[n]-a[1]));
		for(int i=1;i<=n;i++){
			if(a[i]==-1) a[i]=0;
			printf("%d ",a[i]);
		}
		printf("\n");
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



# CF 题解思路
**步骤1**

给定的表达式等于 $|(a_2 - a_1) + (a_3 - a_2) + \dots + (a_n - a_{n-1})|$ 。

**步骤2**

这简化为 $|a_n - a_1|$ 。

**步骤3**

我们希望最小化第一个和最后一个元素之间的绝对差异。要做到这一点，如果其中一个设置了，而另一个没有，我们将它们设置为相等。现在，剩余的空白元素不会影响给定的表达式，因此我们可以将它们全部设置为零。

# CF 题解代码

```cpp
#include <bits/stdc++.h>
using namespace std;
 
void solve(){
    int n;
    cin >> n;
    vector<int> a(n);
    for(int i=0; i<n; i++)
        cin >> a[i];
    if(a[0] == -1)
        a[0] = a[n-1];
    if(a[n-1] == -1)
        a[n-1] = a[0];
    for(int i=0; i<n; i++)
        if(a[i] == -1)
            a[i] = 0;
    cout << abs(a[n-1] - a[0]) << '\n';
    for(int i=0; i<n; i++)
        cout << a[i] << " \n"[i == n-1];
}
 
int main(){
    ios::sync_with_stdio(false);
    cin.tie(NULL);
    int t;
    cin >> t;
    while(t--) solve();
}```










## 💡 核心直觉 (一句话总结)
> 题目的公式直接推导之后发现只和初始位置还有结尾位置有关系



## ✅ 正解思路
1. 
2. 
3. 

## 🧠 举一反三 (Pattern)
* 下次看到 **"差分"** -> 想到 **先动手推导结论**

---
## 💻 代码
```cpp

````



