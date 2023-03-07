---
title: APIO 2017 Travelling Merchant 題解
date: 2022-12-26 16:51:37
tags: Graph Theory
categories: [C++,競程] 
top_img: /img/cover/010.jpg
cover: /img/cover/010.jpg
---

# APIO 2017 Travelling Merchant

## 題目敘述

有$n$個工作站、$k$個商品，每個工作站上會告訴你每個商品的賣出與買入的價格，如果不賣或不買的話價格會標示$-1$，現在給你一張有向圖，圖的節點是工作站，邊權為從工作站$u$走到工作站$v$的時間，問你這張圖中的一個環的最大收益時間比值，也就是這張圖中環上的$\frac{總收益}{時間}$最大是多少。

## 題解

先把時間用adjacency_matrix存起來，對這個矩陣做floyd warshall，即可得知每個點對的最短時間，然後再枚舉每個點對$(u,v)$，去計算$u \to v$的最大收益是多少，一樣用adjacency_matrix儲存。

最後可以發現答案會介於$[0,10^9]$之間，且答案具有單調性，也就是假設目前答案為$x$，我們就可以發現$[0,x]$這個區間內都有辦法構成一個收益時間比值，所以我們可以用二分搜找$x$的最大值。

假設目前收益時間比值$\displaystyle\frac{\sum(S)}{\sum(T)}\ge x$，則我們就可以把$x$更新成目前的收益比值，換句話說，我們就是在二分搜當中找出$x$符合$\sum(S-xT)\ge 0$的最大值，而那個值就會是我們的最大收益比值了。

那要怎麼檢查呢？對於每個$x$，我們都可以重新建立一張圖，邊權為$-(S-xT)$，如果圖中有一個環符合$-(\sum(S-xT))\le 0$的話，那這個$x$就會成立，原因是因為$-(\sum(S-xT))\le 0 = \sum(S-xT)\ge 0$，而這個步驟也能用floyd warshall去檢查。

## Code

```cpp
#include <bits/extc++.h>
#include <bits/stdc++.h>
#pragma gcc optimize("ofast,unroll-loops,no-stack-protector,fast-math")
#pragma gcc target("abm,bmi,bmi2,mmx,sse,sse2,sse3,ssse3,sse4,popcnt,avx,avx2,fma,tune=native")
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
const int N = 1e6+5,L = 20,mod = 1e9+7,inf = 1e9+7;
const double eps = 1e-7;

void floyd(vector<vector<int>>&dp,int n){
	for(int k = 1;k<=n;++k){
		for(int i = 1;i<=n;++i){
			for(int j = 1;j<=n;++j){
				dp[i][j] = min(dp[i][j],dp[i][k]+dp[k][j]);
			}
		}
	}
}
void solve(){
	int n,m,k;
	cin>>n>>m>>k;
	vector<vector<int>>t(105,vector<int>(105,inf)),s(105,vector<int>(105));
	vector<vector<pii>>v(n+5,vector<pii>(k));
	for(int i = 1;i<=n;++i){
		for(int j = 0;j<k;++j){
			cin>>v[i][j].F>>v[i][j].S;
		}
	}
	for(int i = 0;i<m;++i){
		int a,b;
		cin>>a>>b>>t[a][b];
	}
	floyd(t,n);
	for(int i = 1;i<=n;++i){
		for(int j = 1;j<=n;++j){
			if(t[i][j]==inf)continue;
			for(int l = 0;l<k;++l){
				if(v[i][l].F==-1 or v[j][l].S==-1)continue;
				s[i][j] = max(s[i][j],v[j][l].S-v[i][l].F);
			}
		}
	}
	function<bool(int)>check = [&](int x){
		vector<vector<int>>dp(n+5,vector<int>(n+5,inf));
		for(int i = 1;i<=n;++i){
			for(int j = 1;j<=n;++j){
				dp[i][j] = -(s[i][j]-x*t[i][j]);
			}
		}
		floyd(dp,n);
		for(int i = 1;i<=n;++i){
			if(dp[i][i]<=0)return 1;
		}
		return 0;
	};
	int l = 0,r = inf,ans = 0;
	while(l<=r){
		int m = (l+r)>>1;
		if(check(m)){
			ans = m;
			l = m+1;
		}
		else{
			r = m-1;
		}
	}
	cout<<ans<<endl;
}
signed main(){
	IOS;
	//readfile();
	solve();
}
```
