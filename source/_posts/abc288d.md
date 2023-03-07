---
title: AtCoder Beginner Contest 288 pD 題解
date: 2023-02-14 17:29:34
tags: Constructive Algorithms
categories: [C++,競程]
top_img: /img/cover/015.jpg
cover: /img/cover/015.jpg
---

# AtCoder Beginner Contest 288 pD

## 題目

https://atcoder.jp/contests/abc288/tasks/abc288_d

給定一個長度為$n$的序列$a$和一個數字$k$，然後有$q$筆詢問，每筆詢問會有一個區間範圍$l,r$，問你$\{a_l,...,a_r\}$能不能進行數次操作，使得$\{a_l,...,a_r\}$都變成$0$，此操作如下。

* 在$\{a_l,...,a_r\}$中，選出一個長度為$k$的子區間，將該子區間的每個數字都增加任意正整數$c$

如$(3,-1,1,-2,2,0)$，可以進行三次操作：
1. 將$1\sim 3$都加上$4$，使得序列變成$(3,3,5,2,2,0)$
2. 將$2\sim 5$都加上$-2$，使得序列變成$(3,3,3,0,0,0)$
3. 將$0\sim 2$都加上$-3$，使得序列變成$(0,0,0,0,0,0)$

## Solution

這題可以將過程反過來會比較容易想到，就是對於一個一開始都是$0$的序列，我把他選擇一些長度為$k$的區間進行加值後他會變成現在的序列$x$，$x:=\{a_l,...,a_r\}$，那對於每次加值，都是加在$index \equiv 0,1,2,...,k-1 (mod k)$的位置上，因此$\displaystyle \forall i \in [1,k),\sum_{j\equiv i(mod k)}a_j$都會相等，所以我們只要檢查每個區間是否有符合該條件就可以了，而區間和的部分也可以用前綴和去做，因此時間複雜度為$O(N+KQ)$

## AC Code

```cpp
#include <bits/extc++.h>
#include <bits/stdc++.h>
#pragma gcc optimize("ofast, unroll-loops, no-stack-protector, fast-math")
#pragma gcc target("abm, bmi, bmi2, mmx, sse, sse2, sse3, ssse3, sse4, popcnt, avx, avx2, fma, tune=native")
#pragma comment(linker, "/stack:200000000")
#define IOS ios::sync_with_stdio(0),cin.tie(0),cout.tie(0)
#define int long long
#define double long double
#define pb push_back
#define sz(x) (int)(x).size()
#define all(v) begin(v),end(v)
#define debug(x) cerr<<#x<<" = "<<x<<'\n'
#define LINE cout<<"\n-----------------\n"
#define endl '\n'
#define VI vector<int>
#define F first
#define S second
#define MP(a,b) make_pair(a,b)
#define rep(i,m,n) for(int i = m;i<=n;++i)
#define res(i,m,n) for(int i = m;i>=n;--i)
#define gcd(a,b) __gcd(a,b)
#define lcm(a,b) a*b/gcd(a,b)
#define Case() int _;cin>>_;for(int Case = 1;Case<=_;++Case)
#define pii pair<int,int>
using namespace __gnu_cxx;
using namespace __gnu_pbds;
using namespace std;
template <typename K, typename cmp = less<K>, typename T = thin_heap_tag> using _heap = __gnu_pbds::priority_queue<K, cmp, T>;
template <typename K, typename M = null_type> using _hash = gp_hash_table<K, M>;
template <typename K, typename M = null_type, typename cmp = less<K>, typename T = rb_tree_tag> using _tree = tree<K, M, cmp, T, tree_order_statistics_node_update>;
template <typename K, typename M = null_type, typename cmp = less_equal<K>, typename T = rb_tree_tag> using _multitree = tree<K, M, cmp, T, tree_order_statistics_node_update>;
const int N = 1e6+5,L = 20,mod = 1e9+7,inf = 5e15+5;
const double eps = 1e-7;
void solve(){
	int n,m;
	cin>>n>>m;
	VI v(n);
	vector<vector<int>>s(m,vector<int>(n));
	for(int i = 0;i<n;++i){
		cin>>v[i];
		s[i%m][i]+=v[i];
	}
	for(int i = 0;i<m;++i){
		for(int j = 1;j<n;++j){
			s[i][j] += s[i][j-1];
		}
	}
	int q;
	cin>>q;
	while(q--){
		int l,r;
		cin>>l>>r;
		l--,r--;
		bool flag = 1;
		for(int i = 0;i<m;++i){
			if((s[i][r]-(l==0?0:s[i][l-1]))!=(s[0][r]-(l==0?0:s[0][l-1])))flag = 0;
		}
		cout<<(flag?"Yes\n":"No\n");
	}
}
signed main(){
	IOS;
	solve();	
}
```

