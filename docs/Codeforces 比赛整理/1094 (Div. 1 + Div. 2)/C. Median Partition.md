






## C. Median Partition

时间限制：2 秒  
内存限制：256 MB  

## 问题描述  

你有一个长度为奇数 $n$ 的数组 $a$，由正整数组成。你需要将序列划分为若干个子数组*，每个子数组的长度为奇数且**中位数**† 相同。你的任务是找出**子数组的最大数量**。  

更正式地说，你需要找到一个长度为 $p+1$ 的严格递增序列 $k$，满足 $k_1 = 1$ 且 $k_{p+1} = n+1$，使得对于每个 $1 \le i \le p$，序列 $[a_{k_i}, a_{k_i+1}, \dots, a_{k_{i+1}-1}]$ 的中位数全相等。同时 $k_i$ 和 $k_{i+1}$ 的奇偶性应不同。你的任务是找出最大的可能的 $p$ 值。  

* 数组 $b$ 是数组 $a$ 的子数组，如果 $b$ 可以通过删除 $a$ 开头若干个（可能为零或全部）元素以及末尾若干个（可能为零或全部）元素得到。  

† 奇数长度 $x$ 的数组的中位数是数组按非递减顺序排序后的第 $\lceil \frac{x}{2} \rceil$ 个元素。  

## 输入格式  

每个测试包含多个测试用例。第一行包含整数 $t$（$1 \le t \le 1000$），表示测试用例的数量。接下来是每个测试用例的描述。  

每个测试用例的第一行包含一个整数 $n$（$1 \le n < 5000$，$n$ 为奇数）——数组 $a$ 的长度。  

第二行包含 $n$ 个整数 $a_1, a_2, \dots, a_n$（$1 \le a_i \le 10^9$）——数组 $a$ 的元素。  

保证所有测试用例的 $n^2$ 之和不超过 $5000^2$。  

## 输出格式  

对于每个测试用例，输出一个整数——子数组的最大数量。  

## 样例输入  

```
10
5
3 3 2 4 3
7
9 5 7 7 4 7 7
9
1 1 1 1 1 1 1 1 1
1
5
3
1 2 3
3
2 2 2
5
1 2 3 4 5
5
2 1 3 2 2
7
2 2 1 2 3 2 2
9
2 1 2 3 2 1 2 3 2
```  

## 样例输出  

```
3
3
9
1
1
3
1
3
5
5
```  

## 样例说明  

**第一个测试用例**：最优划分方式为 $[3,3,2,4,3]$。——（注：原文说明似乎不完整，根据输出推断划分成了 $[3,3]$ 和 $[2,4,3]$ 等，但需实际验证。此处按题意理解。）  

**第二个测试用例**：最优划分方式为 $[9,5,7]$、$[7]$、$[4,7,7]$。  

**第三个测试用例**：由于所有元素相同，可以将每个元素单独划分为一个子数组。







# AC 代码
- 有意思的DP  题目
- 首先观察到中位数就是全局中位数
- 其次我们就可以开始DP 了
- 这里面有一个很妙的技巧，我又开始一直想着怎么记录位置，因为比如正着来一会儿就会遇见一次big=less   ，那我怎么记录这些位置呢，所以正着是有点复杂的
- 所以我们直接倒着循环，解决了从哪里转移的问题
- 其次就是判断有效，长度奇数，来的转移有效，而且big less  有要求
```cpp
// Problem: CF 2222 C
// Contest: Codeforces - Spectral::Cup 2026 Round 1 (Codeforces Round 1094, Div. 1 + Div. 2)
// URL: https://codeforces.com/contest/2222/problem/C
// Memory Limit: 256 MB
// Time Limit: 2000 ms
// 
// Powered by CP Editor (https://cpeditor.org)

#include<bits/stdc++.h>
using namespace std;


void solve(){
	int n;
	cin>>n;
	vector<int> a(n+1);
	for(int i=1;i<=n;i++){
		cin>>a[i];
	}
	vector<int> b=a;
	sort(b.begin()+1,b.begin()+n+1);
	int mid=b[(n+1)/2];
	
	vector<int> f(n+3,-1);
	f[0]=0;
	for(int i=1;i<=n;i++){
		int big=0,less=0;
		// int pre=1;
		// for(int j=1;j<=i;j++){
			// if(a[j]>mid) big++;
			// if(a[j]<mid) less++;
			// if(big==less){
				// f[j]=max(f[j],f[pre]+1);
				// pre=j;
			// }
		// }
		
		for(int j=i;j>=1;j--){
			if(a[j]>=mid) big++;
			if(a[j]<=mid) less++;
			int len=i-j+1;
			if(f[j-1]!=-1 && len&1 &&big>len/2 &&less>len/2){
				f[i]=max(f[i],f[j-1]+1);
			}
		}
	}
	
	cout<<f[n]<<endl;
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