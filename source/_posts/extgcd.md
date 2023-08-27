---
title: 【筆記】擴展歐幾里得算法 (Extended Euclidean Algorithm)
date: 2023-08-20 18:39:58
categories: [競程, 筆記]
tags: [數論]
index_img:
banner_img:
---

## 引入
對於整數 $a$ 和 $b$，求出 $x$ 和 $y$ 滿足 $ax + by = \gcd(a,b)$

## 演算法
令 $g = \gcd(a, b)$，我們可以透過遞迴求出 $(x, y)$。更具體一點，我們可以在做輾轉相除法的過程中順便求出 $(x, y)$。

先來看輾轉相除法的 base case。當 $a = g$ 且 $b = 0$ 時，不難看出 $a \cdot 1 + b \cdot 0 = g$ 滿足條件。此時 $(x, y) = (1, 0)$。

現在問題變成如何透過 $\gcd(b, a \bmod b)$ 的係數 $(x^{\prime}, y^{\prime})$ 求出 $\gcd(a, b)$ 的係數 $(x, y)$？

根據定義，我們可以列出

$$b x^{\prime} + (a \bmod b) y^{\prime} = g$$

由於 $a \bmod b = a - \lfloor \frac{a}{b} \rfloor \cdot b$，替換後得到

$$b x^{\prime} + (a - \lfloor \frac{a}{b} \rfloor \cdot b) y^{\prime} = g$$

整理係數，將 $a$ 和 $b$ 提出來後得到

$$a y^{\prime} + b (x^{\prime} - \lfloor \frac{a}{b} \rfloor \cdot y^{\prime}) = g$$

比對係數，此時 $ax + by = g$ 的解為

\begin{cases}
x = y^{\prime} \\\\
y = x^{\prime} - \lfloor \frac{a}{b} \rfloor \cdot y^{\prime}
\end{cases}

## 實作
```cpp
int ext_gcd(int a, int b, int& x, int& y) {
	if(b == 0) {
		x = 1;
		y = 0;
		return a;
	}
	int x1, y1;
	int g = ext_gcd(b, a % b, x1, y1);
	x = y1;
	y = x1 - (a / b) * y1;
	return g;
}
```

## 應用

### 求模反元素 (Modular Inverse)

我們要求 $a$ 在模 $m$ ($a$ 和 $m$ 互質) 情況下的逆元 $a^{-1}$ 使得 $a \cdot a^{-1} \equiv 1 \pmod m$。列出

$$ax + my = \gcd(a, m) = 1$$

因為 $my$ 是 $m$ 的倍數，因此 $ax + my \equiv ax \equiv 1 \pmod m$，$x$ 就是 $a$ 在模 $m$ 下的模逆元 $a^{-1}$！

## Exercises
- [UVA 10104 - Euclid Problem](https://onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=1045)
- [Zerojudge a289: Modular Multiplicative Inverse](https://zerojudge.tw/ShowProblem?problemid=a289)
- [ABC315 G - Ai + Bj + Ck = X (1 <= i, j, k <= N)](https://atcoder.jp/contests/abc315/tasks/abc315_g)

## References

- [CP-Algorithms - Extended Euclidean Algorithm](https://cp-algorithms.com/algebra/extended-euclid-algorithm.html)
