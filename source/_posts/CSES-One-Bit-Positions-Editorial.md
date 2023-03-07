---
title: CSES - One Bit Positions 題解
date: 2023-01-04 03:25:17
categories: [競程, 題解, CSES]
tags: [FFT, NTT]
---

[題目連結](https://cses.fi/problemset/task/2112/)

## 題解

先將字串轉換成多項式的形式表示($\text{"10011"} \to 1x^0 + 0x^1 + 0x^2 + 1x^3 + 1x^4$)。
假設轉換成多項式 $a = \sum_{i = 0}^{n - 1} a_i \cdot x^i$，我們要求的是對於所有 $1 \leq k \leq n - 1$，$\sum_{i - j = k} a_i \cdot a_j$。
可以觀察到如果 $i - j = k$，那麼 $i + (n - 1 - j) = n - 1 + k$。
建立 $a^\prime = \sum_{i = 0}^{n - 1} a_{n - 1 - i} \cdot x^i$，答案就會是多項式 $a$ 和 $a^\prime$ 的卷積 $P$，$k$ 的答案即為$[x^{n - 1 + k}]P$。
由於 $n \leq 10^5$，$O(n^2)$ 的多項式卷積會 TLE，可以使用 [FFT](https://oi-wiki.org/math/poly/fft/) 或是 [NTT](https://oi-wiki.org/math/poly/ntt/) 在 $O(n \log n)$ 求出。

<details><summary>Solution Code</summary>
```cpp
#include <bits/stdc++.h>
using namespace std;

using cd = complex<double>;
const double PI = acos(-1); 

void FFT(vector<cd>& a, bool inv) {
	int n = (int) a.size();
	for(int i = 1, j = 0; i < n; ++i) {
		int bit = n >> 1;
		for(; j & bit; bit >>= 1) {
			j ^= bit;
		}
		j ^= bit;
		if(i < j) {
			swap(a[i], a[j]);
		}
	}
	for(int len = 2; len <= n; len <<= 1) {
		const double ang = 2 * PI / len * (inv ? -1 : +1);
		cd rot(cos(ang), sin(ang));
		for(int i = 0; i < n; i += len) {
			cd w(1);
			for(int j = 0; j < len / 2; ++j) {
				cd u = a[i + j], v = a[i + j + len / 2] * w;
				a[i + j] = u + v;
				a[i + j + len / 2] = u - v;
				w *= rot;
			}
		}
	}
	if(inv) {
		for(auto& x : a) {
			x /= n;
		}
	}
}

vector<int> multiply(const vector<int>& a, const vector<int>& b) {
	vector<cd> fa(a.begin(), a.end());
	vector<cd> fb(b.begin(), b.end());
	int n = 1;
	while(n < (int) a.size() + (int) b.size() - 1) {
		n <<= 1;
	}
	fa.resize(n);
	fb.resize(n);
	FFT(fa, false);
	FFT(fb, false);
	for(int i = 0; i < n; ++i) {
		fa[i] *= fb[i];
	}
	FFT(fa, true);
	vector<int> c(a.size() + b.size() - 1);
	for(int i = 0; i < (int) c.size(); ++i) {
		c[i] = round(fa[i].real());
	}
	return c;
}

int main() {
	ios::sync_with_stdio(false);
	cin.tie(0);
	string s;
	cin >> s;
	int n = (int) s.size();
	vector<int> a(n), b(n);
	for(int i = 0; i < n; ++i) {
		a[i] = b[n - 1 - i] = s[i] - '0';
	}
	auto c = multiply(a, b);
	for(int i = 1; i < n; ++i) {
		cout << c[n - 1 + i] << " \n"[i == n - 1];
	}
	return 0;
}
```
</details>