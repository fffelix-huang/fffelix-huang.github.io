---
title: Codeforces 1780F Three Chairs 題解
date: 2023-01-27 18:02:37
categories: [競程, 題解, Codeforces]
tags: [莫比烏斯反演]
index_img:
banner_img:
---

[題目連結](https://codeforces.com/contest/1780/problem/F)

## 題目大意

給定一個長度為 $n$ 的數列 $a$，求：

$$\sum_{i = 0}^{n - 1} \sum_{j = i + 1}^{n - 1} \sum_{k = j + 1}^{n - 1} [\gcd(\min(a_i, a_j, a_k), \max(a_i, a_j, a_k)) = 1]$$

其中 $[P]$ 是[艾佛森括號](https://zh.wikipedia.org/wiki/%E8%89%BE%E4%BD%9B%E6%A3%AE%E6%8B%AC%E5%8F%B7)。

* $3 \leq n \leq 3 \cdot 10^5$
* $1 \leq a_i \leq 3 \cdot 10^5$
* $a_i \neq a_j, i \neq j$

## 觀察

$\mu$ 為[莫比烏斯函數](https://oi-wiki.org/math/number-theory/mobius/)，具有以下的性質：
$$
\sum_{d | n} \mu(d) = [n = 1] =
\begin{cases}
    0 & n \neq 1 \\\\
    1 & n = 1
\end{cases}
$$

我們令 $A = \max(a)$ 為值域。
利用莫比烏斯函數的性質，我們可以把原本的式子轉換為：

$$
\begin{aligned}
&= \sum_{i = 0}^{n - 1} \sum_{j = i + 1}^{n - 1} \sum_{k = j + 1}^{n - 1} \sum_{d | \gcd(\min(a_i, a_j, a_k), \max(a_i, a_j, a_k))} \mu(d) \\\\
&= \sum_{d = 1}^{A} \mu(d) \sum_{i = 0}^{n - 1} \sum_{j = i + 1}^{n - 1} \sum_{k = j + 1}^{n - 1} ([d | \min(a_i, a_j, a_k)][d | \max(a_i, a_j, a_k)])
\end{aligned}
$$

可以發現後面那項其實就是在求有幾組 $i < j < k$ 滿足 $a_i < a_j < a_k$ 且 $a_i$ 和 $a_k$ 是 $d$ 的倍數，枚舉 $d$ 的倍數後可以輕易求出。

莫比烏斯函數可以用[線性篩](https://oi-wiki.org/math/number-theory/sieve/#%E7%BA%BF%E6%80%A7%E7%AD%9B%E6%B3%95)在 $O(A)$ 求出，而所有倍數的個數為 $\sum\limits_{d = 1}^{A} \lfloor \frac{A}{d} \rfloor \approx O(A \log A)$，總時間複雜度為 $O(n + A \log A)$。

[Solution Code](https://codeforces.com/contest/1780/submission/190633405)