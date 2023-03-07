---
title: AtCoder Beginner Contest 289 pE 題解
date: 2023-02-13 09:22:18
tags: [Graph Theory]
categories: [C++,競程]
top_img: /img/cover/013.jpg
cover: /img/cover/013.jpg
---
# AtCoder Beginner Contest 289 pE

## 題目

https://atcoder.jp/contests/abc289/tasks/abc289_e

有兩個人，分別在節點$1$和節點$n$，兩個人每次移動都可以移動到相鄰的節點，並且兩個人移動到的節點顏色必須相異，求第一個人移動到$n$且第二個人移動到$1$的最短路。

## Solution

這題我賽中是想到多源點BFS，只是後來發現每個點可能不只會被同一個人經過一次，所以就燒雞了。

賽後同學提示可以把兩個人所在的位置看成一個狀態，所以就往dp的方向去想了，$dp[i][j]$代表第一個人從$1$走到$i$且第二個人從$n$走到$j$的最短路徑。

初始狀態$dp[1][n] = 0$，然後只要跑一次最短路求出$dp[n][1]$就好了，這裡是用dijkstra演示

## AC Code

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
#define pb push_back
#define IOS ios::sync_with_stdio(0),cin.tie(0),cout.tie(0)
#define endl '\n'
const int inf = 2e18+5;
void solve(){
	int n,m;
	cin>>n>>m;
	vector<vector<int>>dp(n+5,vector<int>(n+5,inf)),g(n+5),vis(n+5,vector<int>(n+5));
	vector<int>c(n+5);
	for(int i = 1;i<=n;++i)cin>>c[i];
	for(int i = 0;i<m;++i){
		int u,v;cin>>u>>v;
		g[u].pb(v);
		g[v].pb(u);
	}
	if(c[1]==c[n]){
		cout<<-1<<endl;
		return;
	}
	dp[1][n] = 0;
	priority_queue<tuple<int,int,int>,vector<tuple<int,int,int>>,greater<tuple<int,int,int>>>pq;
	pq.push({dp[1][n],1,n});
	while(!pq.empty()){
		auto [dis,u1,u2] = pq.top();
		pq.pop();
		if(vis[u1][u2])continue;
		vis[u1][u2] = 1;
		vector<int>tmp[2];
		for(auto v2:g[u2]){
			tmp[c[v2]].pb(v2);
		}
		for(auto v1:g[u1]){
			for(auto v2:tmp[c[v1]^1]){
				if(vis[v1][v2])continue;
				if(dp[v1][v2]>dp[u1][u2]+1){
					dp[v1][v2] = dp[u1][u2]+1;
					pq.push({dp[v1][v2],v1,v2});
				}
			}
		}
	}
	if(dp[n][1]==inf)cout<<-1<<endl;
	else cout<<dp[n][1]<<endl;
}
signed main(){
	IOS;
	int t;
	cin>>t;
	while(t--){
		solve();
	}
}
```
