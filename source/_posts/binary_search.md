---
date: 2023-02-21 21:12:44
tags: Binary Search
categories: [C++,競程]
title: 二分搜尋法 (Binary Search)
cover: /img/cover/017.jpg
top_img: /img/cover/017.jpg
---

# 二分搜尋法 (Binary Search)

## Usage

比線性還要更快速的查找方式 (查找的數列必須具有單調性！)

## 概念

假設有個數列`a = {1,3,4,6,7,8,10,13,14}`，我們要在裡面尋找`4`這個數字，我們就可以試著用"二分搜"去做。

**注意！a數列是單調遞增的，如果不具有遞增或遞減性的數列是不能做二分搜的喔。**

## 實作

給予一個包含${\displaystyle n}$個帶值元素的陣列${\displaystyle A}$或是記錄${\displaystyle A_{0},\cdots ,A_{n-1}}$ ，使${\displaystyle A_{0}\leq \cdots \leq A_{n-1}}$ ，以及目標值${\displaystyle T}$，還有下列用來搜尋${\displaystyle T}$在${\displaystyle A}$中位置的子程式。

1. 令${\displaystyle L}$為${\displaystyle 0}$，${\displaystyle R}$為${\displaystyle n-1}$。
2. 如果${\displaystyle L>R}$，則搜尋以失敗告終。
3. 令${\displaystyle m}$（中間值元素）為${\displaystyle \lfloor (L+R)/2\rfloor }$。（具體實現中，為防止算術溢位，一般採用${\displaystyle \lfloor L+(R-L)/2\rfloor }$代替。）
4. 如果${\displaystyle A_{m}<T}$，令${\displaystyle L}$為${\displaystyle m+1}$並回到步驟二。
5. 如果${\displaystyle A_{m}>T}$，令${\displaystyle R}$為${\displaystyle m-1}$並回到步驟二。
6. 當${\displaystyle A_{m}=T}$，搜尋結束；回傳值${\displaystyle m}$。

這個疊代步驟會持續透過兩個變數追蹤搜尋的邊界。有些實際應用會在演算法的最後放入相等比較，讓比較迴圈更快，但平均而言會多一層疊代。

![](https://i.imgur.com/mIzhlyU.png)


## 例題

zerojudge d732

按照上面說的二分搜流程做就行了，只是要記得題目的序列是1-base的，搜尋區間為$[1,n]$。

```cpp
#include<iostream>
#include<vector>
#include<algorithm>
using namespace std;
int main(){
	int n,m;
	cin>>n>>m;
	vector<int>v(n+5);
	for(int i = 1;i<=n;++i){
		cin>>v[i];
	}
	sort(begin(v)+1,begin(v)+n+1);
	for(int i = 0;i<m;++i){
		int t;
		cin>>t;
		int l = 1,r = n,ans = 0;//[1,n],ans==0 : no answer
		while(l<=r){
			int m = (l+r)/2;
			if(v[m]>t){
				r = m-1;
			}
			else if(v[m]<t){
				l = m+1;
			}
			else{
				ans = m;
				break;
			}
		}
		cout<<ans<<endl;
	}
}
```

序列長度為$n$，搜尋次數為$k$，時間複雜度為$O(k\log(n))$
