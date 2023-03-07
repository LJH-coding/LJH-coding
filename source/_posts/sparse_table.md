---
date: 2023-02-23 07:42:43
tags: Data Structure
title: 稀疏表 (Sparse Table)
categories: [C++,競程]
top_img: /img/cover/018.jpg
cover: /img/cover/018.jpg
---

# 稀疏表 (Sparse Table)

## 概念

將原序列從$arr$第$i$個開始長度為$2^j$的連續子序列的答案用陣列儲存起來。
那要怎麼存呢？這裡會用到一點$DP$的想法，令$dp_{i,j}$為原本定義的答案
初始狀態$dp_{0} = arr$
$dp_{i,j} = cmp(dp_{i-1,j},dp_{i-1,j+2^{i-1}})$
$cmp$為合併兩個答案的函式，如果是區間最大值的話就用$\max$，如果是最小值就用$\min$

## 用法

### 預處理

用剛剛推出來的轉移式去做就好了
```cpp
vector<vector<int>>mat(30);
void build(const vector<int>&v){
    int n = v.size();
    mat[0] = a;
    for(int i = 1;(1<<i)<=n;++i) {
        mat[i].resize(n-(1<<i)+1);
        for(int j = 0;j<=n-(1<<i);++j) {
            mat[i][j] = cmp(mat[i-1][j],mat[i-1][j+(1<<(i-1))]);
        }
    }
}
```
時間複雜度為$O(n\log n)$

### 區間查詢

查詢$[l,r]$區間的答案

$let\ k=log_2(r-l+1)$
$ans = cmp(dp_{k,l},dp_{k,r-(2^k)+1})$
$cmp$一樣視題目去做調整，要記得，詢問的答案必須滿足可重複貢獻性質，因為我們的$dp$紀錄的答案區間會有重疊的情況發生，所以如果重複貢獻會影響答案的話(如區間和、區間$xor$等)就不能用這樣的方式去進行詢問，通常會拿來進行區間$\max$、區間$\min$、區間$\gcd$等查詢。

```cpp
int query(int ql, int qr) {
    int k = __lg(qr - ql + 1);
    return cmp(st[k][ql], st[k][qr - (1<<k) + 1]);
}
```

時間複雜度為$O(cmp)$，如果是$\max$或是$\min$的話就是$O(1)$，如果是$\gcd$的話就是$O(\log A)$，$A$為值域。
要記得，取$\log$的方式盡量避免用內建的$\log2()$函式，避免$\text{TLE}$，內建的`__lg()`是使用位元運算實作的，所以速度很快，或者你也可以使用建表的方式。

## 練習

### Codeforces 475D

https://codeforces.com/contest/475/problem/D

#### 題目敘述

給你一個長度為$N$的序列，然後有$Q$筆詢問，每次詢問你這個序列當中一共有多少連續子序列的最大公因數為$x$。
$1\le N\le 10^5$
$1\le Q\le 3\times 10^5$
$1\le x\le 10^9$

#### 題解

我們可以先預處理詢問的答案，用一個hash_map存，key對應到$x$，value對應到答案。
那計算答案的部分該怎麼做呢，我們可以用二分搜，先枚舉左界$l$，尋找一個區間$[L,R]$，使得$\gcd(a_l,...,a_L)$到$\gcd(a_l,...,a_R)$都等於$x$，為甚麼可以使用二分搜了，因為固定左界的時候，我們就發現從左界開始的最小公因數有單調遞減的特性，一共有$\log A$個公因數，$A$是值域，然後二分搜花了$\log N\log A$的時間，所以總時間複雜度為$O(N\log N\log^2 A)$

#### AC Code
```cpp
template<class T,T (*op)(T,T)>struct sparse_table{
	int n;
	vector<vector<T>>mat;
	sparse_table(): n(0){}
	sparse_table(const vector<T>&v){
		n = (int)(v.size());
		mat.resize(30);
		mat[0] = v;
		for(int i = 1;(1<<i)<=n;++i){
			mat[i].resize(n-(1<<i)+1);
			for(int j = 0;j<n-(1<<i)+1;++j){
				mat[i][j] = op(mat[i-1][j],mat[i-1][j+(1<<(i-1))]);
			}
		}
	}
	T query(int ql,int qr){
		int k = __lg(qr-ql+1);
		return op(mat[k][ql],mat[k][qr-(1<<k)+1]);
	}
};
struct custom_hash {
	static uint64_t splitmix64(uint64_t x) {
		x += 0x9e3779b97f4a7c15;
		x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
		x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
		return x ^ (x >> 31);
	}
	size_t operator()(uint64_t x) const {
		static const uint64_t FIXED_RANDOM = chrono::steady_clock::now().time_since_epoch().count();
		return splitmix64(x + FIXED_RANDOM);
	}
};
template<class T,class U>using hash_map = gp_hash_table<T,U,custom_hash>;
void solve(){
	hash_map<int,int>mp;
	int n;
	cin>>n;
	VI v(n+5);
	for(int i = 1;i<=n;++i){
		cin>>v[i];
	}
	sparse_table<int,__gcd>st(v);
	for(int i = 1;i<=n;++i){
		function<void(int,int)>f = [&](int val,int s){
			if(s>n)return;
			int l = s,r = n,ans = st.query(i,s);
			while(l<=r){
				int m = (l+r)>>1;
				if(st.query(i,m)>=val){
					l = m+1;
					ans = m;
				}
				else{
					r = m-1;
				}
			}
			mp[val]+=(ans-s+1);
			f(st.query(i,ans+1),ans+1);
		};
		f(v[i],i);
	}
	int m;cin>>m;
	while(m--){
		int x;
		cin>>x;
		cout<<mp[x]<<endl;
	}
}
```
:::

### Codeforces 307093G

https://codeforces.com/edu/course/2/lesson/9/2/practice/contest/307093/problem/G

#### 題目敘述

給你一個長度為$N$的序列，求出連續子序列$\gcd$為$1$的最小長度
$1\le N\le 10^5$

#### 題解

跟上面那題類似，固定左界二分搜找區間$\gcd$為$1$的右界
時間複雜度為$O(N\log N\log A)$

#### AC Code
```cpp
template<class T,T (*op)(T,T)>struct sparse_table{
	int n;
	vector<vector<T>>mat;
	sparse_table(): n(0){}
	sparse_table(const vector<T>&v){
		n = (int)(v.size());
		mat.resize(30);
		mat[0] = v;
		for(int i = 1;(1<<i)<=n;++i){
			mat[i].resize(n-(1<<i)+1);
			for(int j = 0;j<n-(1<<i)+1;++j){
				mat[i][j] = op(mat[i-1][j],mat[i-1][j+(1<<(i-1))]);
			}
		}
	}
	T query(int ql,int qr){
		int k = __lg(qr-ql+1);
		return op(mat[k][ql],mat[k][qr-(1<<k)+1]);
	}
};
void solve(){
	int n,mn = inf;
	cin>>n;
	VI v(n+5);
	for(int i = 1;i<=n;++i){
		cin>>v[i];
	}
	sparse_table<int,__gcd>st(v);
	for(int i = 1;i<=n;++i){
		int l = i,r = n,ans = i;
		while(l<=r){
			int m = (l+r)>>1;
			if(st.query(i,m)>1){
				l = m+1;
			}
			else{
				ans = m;
				r = m-1;
			}
		}
		if(st.query(i,ans)==1)mn = min(mn,ans-i+1);
	}
	cout<<(mn>1000000?-1:mn)<<endl;
}
```

### Codeforces 514D

https://codeforces.com/problemset/problem/514/D

#### 題目敘述

有$M$個長度為$N$的序列，可以對這$N$個序列選一個連續區間進行$K$次操作，每次操作就是把其中一個序列的連續區間減$1$，請你求出連續區間最大時對於每個序列分別要進行幾次操作。
$1\le N\le 10^5$
$1\le M\le 5$
$1\le K\le 10^9$

#### 題解

枚舉連續區間的左界，檢查每個序列的連續區間最大值總和$\le K$的最大右界是多少，右界的部分可以用二分搜的方式去找，時間複雜度為$O(NM\log N)$

#### AC Code
```cpp
template<class T,T (*op)(T,T)>struct sparse_table{
	int n;
	vector<vector<T>>mat;
	sparse_table(): n(0){}
	sparse_table(const vector<T>&v){
		n = (int)(v.size());
		mat.resize(30);
		mat[0] = v;
		for(int i = 1;(1<<i)<=n;++i){
			mat[i].resize(n-(1<<i)+1);
			for(int j = 0;j<n-(1<<i)+1;++j){
				mat[i][j] = op(mat[i-1][j],mat[i-1][j+(1<<(i-1))]);
			}
		}
	}
	T query(int ql,int qr){
		int k = __lg(qr-ql+1);
		return op(mat[k][ql],mat[k][qr-(1<<k)+1]);
	}
};
int Max(int a,int b){return max(a,b);}
void solve(){
	int n,m,k;
	cin>>n>>m>>k;
	vector<vector<int>>v(m,vector<int>(n));
	vector<int>tmp(m);
	sparse_table<int,Max>st[m];
	for(int i = 0;i<n;++i){
		for(int j = 0;j<m;++j){
			cin>>v[j][i];
		}
	}
	for(int i = 0;i<m;++i){
		st[i] = sparse_table<int,Max>(v[i]);
	}
	int mx = 0;
	for(int i = 0;i<n;++i){
		int l = i,r = n-1,ans = -1;
		while(l<=r){
			int mid = (l+r)>>1;
			int sum = 0;
			for(int j = 0;j<m;++j){
				sum+=st[j].query(i,mid);
			}
			if(sum<=k){
				ans = mid;
				l = mid+1;
			}
			else{
				r = mid-1;
			}
		}
		if((ans-i+1)>mx and ans!=-1){
			mx = (ans-i+1);
			for(int j = 0;j<m;++j){
				tmp[j] = st[j].query(i,ans);
			}
		}
	}
	for(int i = 0;i<m;++i){
		cout<<tmp[i]<<' ';
	}
	cout<<endl;
}
```