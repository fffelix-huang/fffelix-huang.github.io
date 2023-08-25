---
title: 【題解】ABC312 G - Avoid Straight Line
date: 2023-08-05 01:13:07
categories: [競程, 題解, AtCoder]
tags:
index_img:
banner_img:
---

[題目連結](https://atcoder.jp/contests/abc312/tasks/abc312_g)

## 題目大意

給定一棵 $N$ 個節點的樹，問有幾組 $(i, j, k)$ 滿足 $i < j < k$ 且在樹上三個節點不會形成一條路徑？

* $1 \leq N \leq 2 \cdot 10^5$

## 題解

三點不形成一條路徑的方法數可能不太直覺，我們改成用扣除的，也就是計算 $(\text{全部} - \text{三點形成一條路徑})$ 的方法數。全部就是 $\binom{N}{3}$，因此問題化簡為計算三點形成一條路徑的方法數。

我們枚舉三點中的中點 $u$，其他兩個點必定落在 $u$ 的兩個不同子樹。假設 $u$ 有 $k$ 個子樹，子樹的大小分別為 $sz_1, \dots, sz_k$，則 $u$ 為中點的方法數為：

$$\sum\limits_{i = 1}^{k - 1} \sum\limits_{j = i + 1}^k sz_i sz_j = \sum\limits_{i = 1}^{k - 1} sz_i (\sum\limits_{j = i + 1}^k sz_j)$$

可以在 $O(k)$ 求得。

因為每個節點都可能成為三點中的中點，每個節點都要把他當成樹根，因此我們需要用到換根的技巧，可以在 $O(1)$ 重新計算當樹根從相鄰節點 $u$ 移動到 $v$ 時子樹大小的改變，詳細作法請參考程式碼。

[Solution Code](https://atcoder.jp/contests/abc312/submissions/44238184)
