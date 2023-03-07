---
title: Aliens 優化應用
categories: [競程, 演算法]
tags: [Aliens 優化]
date: 2023-03-01 12:06:45
index_img:
banner_img:
---

## 基本概念

有些題目要求進行 $k$ 次操作的最大/最小值。我們開心的列出 $dp[i][j]$ 為前 $i$ 個東西進行 $j$ 次操作的最大值，答案就是 $dp[n][k]$，但很不幸的 $O(nk)$ 會爆，因此我們要換個做法。

**Definition.** 函數 $f(k)$ 為進行 $k$ 次操作的最大值。

$f(k)$ 必須是一個凹函數 (concave function) 才能套用 Aliens 優化 (如果改成最小值的話則必須是凸函數)。凹函數的斜率非嚴格遞減，也就是說每多進行一次操作，數值增加的幅度會越來越少。

$$f(k + 1) - f(k) \leq f(k) - f(k - 1)$$

**Definition.** 函數 $g_p(k) = f(k) - kp$。

$g_p(k)$ 可以想像成對於每筆操作，我們額外收取 $p$ 元的手續費。不難看出 $g_p(k)$ 也會是一個凹函數，因為

\begin{aligned}
g_p(k + 1) - g_p(k) &= (f(k + 1) - (k + 1)p) - (f(k) - kp) \\\\
&= f(k + 1) - f(k) - p \\\\
& \leq f(k) - f(k - 1) - p \\\\
&= (f(k) - kp) - (f(k - 1) - (k - 1)p) \\\\
&= g_p(k) - g_p(k - 1) \\\\
\iff g_p(k + 1) - g_p(k) & \leq g_p(k) - g_p(k - 1)
\end{aligned}

當 $p$ 越大，$g_p(k)$ 最大值發生的位置 $k$ 會越往左邊靠 (可以想成手續費太貴，所以不進行那麼多次操作)，當 $p$ 越小，最大值發生的位置會越往右靠 (手續費太便宜，多進行幾次操作不會虧錢)。[^1]

有了以上的概念，我們就可以對 $p$ 進行二分搜，讓 $g_p(k)$ 最大值發生的位置剛好落在 $k$，也就是操作 $k$ 次，這樣我們就可以回頭算出 $f(k) = g_p(k) + kp$ 了！如果 $f(k)$ 的定義改成最小值的話就改成 $g_p(k) = f(k) + kp$，也能用同樣的邏輯求出。

其他實作的細節可以參考 [Reference](#Reference) 的文章。

## 模板

找最小值的話記得把 $+kp$ 改成 $-kp$。

```cpp
// 找最大值
template<class Func>
long long Aliens(long long l, long long r, int k, Func f) {
	while(l < r) {
		long long m = l + (r - l) / 2;
		auto [score, op] = f(m);
		if(op == k) {
			return score + m * k;
		}
		if(op < k) {
			r = m;
		} else {
			l = m + 1;
		}
	}
	return f(l).first + l * k;
}
```

## 例題

底下的例題 $dp$ 應該要是 {最大/最小值, 最少操作次數}，方便起見只寫最大/最小值，實作時記得要計算操作次數。$\Phi$ 為每筆操作而外收的手續費。

> [AI-666 賺多少](https://tioj.ck.tp.edu.tw/problems/2039)
>
> 已知 $n$ 個時間點股票的價格，手上沒有股票的話才能買入，有股票才能賣出。求買賣 $k$ 次的最大利益？

本題有 greedy 解，但我們練習用 Aliens 優化來做。

定義 $f(k)$ 為做 $k$ 次交易的最大收益。可以觀察到 $f$ 是凹函數，因為如果第 $k$ 筆交易的獲益比第 $k - 1$ 次多，我們可以交換交易的順序，把 $k - 1$ 多做的那次換成 $k$ 多做的那次。

定義 $dp$：

* $dp_0[i]$ = 時間 $i$ 時手上**沒有**股票的最大收益
* $dp_1[i]$ = 時間 $i$ 時手上**持有**股票的最大收益

轉移就會是：

\begin{aligned}
dp_0[i] &= \max(dp_0[i - 1], dp_1[i - 1] + a[i] - \Phi) \\\\
dp_1[i] &= \max(dp_1[i - 1], dp_0[i - 1] - a[i])
\end{aligned}

計算一次 $dp$ 的時間為 $O(n)$，因此總時間複雜度為 $O(n \log C)$ (以下皆用 $C$ 表示 Aliens 二分搜的範圍)。

<details><summary>Solution Code</summary>
```cpp
#include <bits/stdc++.h>
using namespace std;

// find maximum
template<class Func>
long long Aliens(long long l, long long r, int k, Func f) {
	while(l < r) {
		long long m = l + (r - l) / 2;
		auto [score, op] = f(m);
		if(op == k) {
			return score + m * k;
		}
		if(op < k) {
			r = m;
		} else {
			l = m + 1;
		}
	}
	return f(l).first + l * k;
}

int main() {
	ios::sync_with_stdio(false);
	cin.tie(0);
	int n, k;
	cin >> n >> k;
	vector<int> a(n);
	for(int i = 0; i < n; ++i) {
		cin >> a[i];
	}
	auto f = [&](long long p) {
		pair<long long, int> dp0 = {0, 0};
		pair<long long, int> dp1 = {INT_MIN, 0};
		for(int i = 0; i < n; ++i) {
			pair<long long, int> new_dp0 = max(dp0, pair<long long, int>{dp1.first + a[i] - p, dp1.second - 1});
			pair<long long, int> new_dp1 = max(dp1, pair<long long, int>{dp0.first - a[i], dp0.second});
			swap(dp0, new_dp0);
			swap(dp1, new_dp1);
		}
		dp0.second = -dp0.second;
		return dp0;
	};
	cout << Aliens(0, (int) 1e8, k, f) << "\n";
	return 0;
}
```
</details>

> [CSES - Subarray Squares](https://cses.fi/problemset/task/2086)
>
> 把長度為 $n$ 的數列切成 $k$ 段，一段的費用是和的平方，求最小費用和？

定義 $f(k)$ 為切成 $k$ 段的最小費用和。固定切割的位置，切割的先後順序不會影響答案，我們可以讓影響最小的那次切割作為第 $k$ 次，因此 $f$ 是一個凸函數。

定義 $dp[i]$ 為只考慮前 $i$ 個數字的最小費用和，轉移就會是：

$$dp[i] = \min_{j \leq i} (dp[j - 1] + (\sum_{k = j}^{i} a[i])^2 + \Phi)$$

注意轉移裡的 $\Phi$ 係數為正，因為我們的目標是找最小值。

時間複雜度：$O(n^2 \log C)$

<details><summary>Solution Code</summary>
```cpp
#include <bits/stdc++.h>
using namespace std;

// find minimum
template<class Func>
long long Aliens(long long l, long long r, int k, Func f) {
	while(l < r) {
		long long m = l + (r - l) / 2;
		auto [score, op] = f(m);
		if(op == k) {
			return score - m * k;
		}
		if(op < k) {
			r = m;
		} else {
			l = m + 1;
		}
	}
	return f(l).first - l * k;
}

int main() {
	ios::sync_with_stdio(false);
	cin.tie(0);
	int n, k;
	cin >> n >> k;
	vector<int> a(n);
	for(int i = 0; i < n; ++i) {
		cin >> a[i];
	}
	vector<long long> pref(n + 1);
	for(int i = 0; i < n; ++i) {
		pref[i + 1] = pref[i] + a[i];
	}
	const long long INF = (long long) 1e18L + 5;
	auto f = [&](long long cost) -> pair<long long, int> {
		vector<pair<long long, int>> dp(n, pair<long long, int>{INF, 0});
		for(int i = 0; i < n; ++i) {
			for(int j = i; j >= 0; --j) {
				auto cur = (j > 0 ? dp[j - 1] : pair<long long, int>{0, 0});
				cur.first += (pref[i + 1] - pref[j]) * (pref[i + 1] - pref[j]) + cost;
				cur.second += 1;
				dp[i] = min(dp[i], cur);
			}
		}
		return dp[n - 1];
	};
	cout << Aliens(0, INF, k, f) << "\n";
	return 0;
}
```
</details>

## 習題
[ZJ - 美食博覽會 (k 值加大版)](https://zerojudge.tw/ShowProblem?problemid=h926)
[CSES - Houses and Schools](https://cses.fi/problemset/task/2087/)
[CF - New Year and Handle Change](https://codeforces.com/contest/1279/problem/F)
[TIOJ - 郵局設置問題 $\infty$ EXTREME](https://tioj.ck.tp.edu.tw/problems/1986)
[IOI 2016 - Aliens](https://ioinformatics.org/files/ioi2016problem6.pdf)

# Reference

1. [[WiwiHo 的競程筆記] Aliens 優化](https://cp.wiwiho.me/aliens/)
2. [[2016建中校內培訓講義] Aliens 優化](https://tioj.ck.tp.edu.tw/uploads/attachment/5/51/10.pdf)

[^1]: 可以參考 Reference 1. 的 gif，其中藍色的函數就是 $g_p(k)$。