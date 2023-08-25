---
title: 【題解】ABC313 D - Odd or Even
date: 2023-08-05 21:28:56
categories: [競程, 題解, AtCoder]
tags: [互動題]
index_img:
banner_img:
---

[題目連結](https://atcoder.jp/contests/abc313/tasks/abc313_d)

## 題目大意

有一個隱藏的長度為 $N$ 的數列，每一項為 $0$ 或 $1$，你要猜出數列每一項的數值。你可以問至多 $N$ 個問題。給定常數 $K$，每次詢問 $K$ 個 index，judge 會告訴你這些 index 加起來的奇偶性。judge 是 adaptive 的，也就是他的答案不固定，只要不與 judge 的回答自相矛盾的都有可能是答案。

* $1 \leq K < N \leq 1000$
* $K$ 是奇數

## 題解

我們可以透過兩個詢問來得知兩個位置的數值是否相同。假設第一次我們呼叫 $query(a_1, \dots, a_k) = x$，第二次呼叫 $query(a_1, \dots, a_{k - 1}, a_{k + 1}) = y$，如果 $x = y$，則我們知道 $a_k = a_{k + 1}$，否則 $a_k \neq a_{k + 1}$。透過 $N$ 次的詢問我們可以將所有 index 分成兩組，其中一組的數值全部都是 $0$，另外一組全部都是 $1$。最後因為 $K$ 是奇數，也可以判斷出兩組各自的數值。實作細節請參考 code。

[Solution Code](https://atcoder.jp/contests/abc313/submissions/44278230)
