---
title: ABC311 F - Yet Another Grid Task 題解
date: 2023-08-05 13:43:49
categories: [競程, 題解, AtCoder]
tags: [DP]
index_img:
banner_img:
---

[題目連結](https://atcoder.jp/contests/abc311/tasks/abc311_f)

## 題目大意

給定 $N \times M$ 個格子，每個格子為黑或白。如果 $(i, j)$ 為黑，則下方 $(i + 1, j)$ 和右下方 $(i + 1, j + 1)$ 也會被染成黑色。一開始已經有一些格子是黑色，現在你可以將某些白色的格子塗成黑色，問最後可以產生幾種相異的版面 $\bmod{998244353}$。

* $1 \leq N, M \leq 2000$

## 題解

官方的[題解](https://atcoder.jp/contests/abc311/editorial/6828)寫了個不直觀的斜線 $dp$，因此這邊提供一個比較簡單的作法。

因為黑色會向下染色，因此每一列都會是上面白色下面黑色。根據這個觀察，我們可以定義 $dp[i][j]$ 為只考慮前 $i$ 列且 $(i, j)$ 為第 $i$ 列最上方的黑色格子，或是 $dp[i][n]$ 為該列沒有黑色格子。因為黑色會往右下方染色，在 $(i - 1, j - 1)$ 上方的格子都不能是黑色，否則 $(i, j)$ 就不會是該列最上方黑色的格子。因此我們可以列出 $dp$ 的轉移式：

$$dp[i][j] = \sum\limits_{k = \max(0, j - 1)}^n dp[i - 1][k]$$

這個轉移可以透過預處理後用後綴和 $O(1)$ 得到。時間複雜度為 $O(NM)$。

[Solution Code](https://atcoder.jp/contests/abc311/submissions/44246658)
