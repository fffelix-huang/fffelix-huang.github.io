---
title: Atcoder Beginner Contest 256 A~H 題解
date: 2022-06-25 02:49:24
categories: [競程, 題解, AtCoder]
tags: [並查集, 線段樹, 矩陣快速冪]
index_img:
---

## 前言

這次在賽後補完了F、G、H，覺得題目不錯，所以來寫個題解吧

## [A - 2^N](https://atcoder.jp/contests/abc256/tasks/abc256_a)

直接乘以 $n$ 次，或是使用 $<<$ 的位元運算。

時間複雜度：$O(n)$ 或 $O(1)$

[Solution Code](https://atcoder.jp/contests/abc256/submissions/32694745)

## [B - Batters](https://atcoder.jp/contests/abc256/tasks/abc256_b)

從索引比較高的往回更新。

時間複雜度：$O(n)$

[Solution Code](https://atcoder.jp/contests/abc256/submissions/32694755)

## [C - Filling 3x3 array](https://atcoder.jp/contests/abc256/tasks/abc256_c)

枚舉左上角四格的所有組合，並判斷剩下的數字是否合理。

時間複雜度：$O(30^4)$

[Solution Code](https://atcoder.jp/contests/abc256/submissions/32694818)

## [D - Union of Interval](https://atcoder.jp/contests/abc256/tasks/abc256_d)

對線段的左端點排序，判斷與下一個線段有無相交。若有重疊則進行合併。

時間複雜度：$O(n \log n)$

[Solution Code](https://atcoder.jp/contests/abc256/submissions/32694847)

## [E - Takahashi's Anguish](https://atcoder.jp/contests/abc256/tasks/abc256_e)

考慮一張無向圖 $G$，節點代表 $n$ 個人，我們連接邊 $i \leftrightarrow X_i$ 代表 $i$ 比 $X_i$ 早拿到禮物。因此如果圖中存在環，代表拿到禮物的先後順序矛盾。因此，題目可化簡為在 $G$ 加入一些邊，使得這些邊不會形成環且權重和越大(代表我們避免掉越多的 frustration)，剩下的邊的權重和(無法避免掉的)就是題目所求。因此，我們可以用並查集維護無向圖中的連通塊，並把邊按照權重由大到小嘗試加入 $G$。

時間複雜度：$O(n \alpha(n))$

[Solution Code](https://atcoder.jp/contests/abc256/submissions/37711907)

## [F - Cumulative Cumulative Cumulative Sum](https://atcoder.jp/contests/abc256/tasks/abc256_f)

先來將 $A, B, C$ 數列展開觀察
|     | i = 1 | i = 2               | i = 3                             | i = 4                                           |
| --- | ----- | ------------------- | --------------------------------- | ----------------------------------------------- |
| $A$ | $A_1$ | $A_2$               | $A_3$                             | $A_4$                                           |
| $B$ | $A_1$ | $A_1 + A_2$         | $A_1 + A_2 + A_3$                 | $A_1 + A_2 + A_3 + A_4$                         |
| $C$ | $A_1$ | $2 \cdot A_1 + A_2$ | $3 \cdot A_1 + 2 \cdot A_2 + A_3$ | $4 \cdot A_1 + 3 \cdot A_2 + 2 \cdot A_3 + A_4$ |

所要求的 $D_x$ 就是 $C$ 的前綴和 $\sum_{i = 1}^x C_i$
而 $C$ 數列有個特性，當我們將 $A_i$ 增加 $K$，$A_{i + 1}$ 要增加 $2K$，$A_{i + 2}$ 增加 $3K$，以此類推。因此我們需要可以區間更新和區間加總的資料結構，可以用線段樹 + 懶人標記。簡化到這裡可以發現就跟[這題](https://cses.fi/problemset/task/1736)一模一樣。

時間複雜度：$O(n \log n)$

[Solution Code](https://atcoder.jp/contests/abc256/submissions/37712082)

## [G - Black and White Stones](https://atcoder.jp/contests/abc256/tasks/abc256_g)

每個邊有 $d + 1$ 顆石頭。把每個邊的狀態列出來，可以發現一共有四種狀態：白頭白尾、白頭黑尾、黑頭白尾、黑頭黑尾。如果把狀態寫成矩陣：

\begin{bmatrix}
  C_{WW} & C_{WB} \\\\
  C_{BW} & C_{BB} 
\end{bmatrix}

把 $n$ 條邊串在一起就是將矩陣相乘 $n$ 次，可以使用[矩陣快速冪](https://zh.wikipedia.org/wiki/%E5%B9%B3%E6%96%B9%E6%B1%82%E5%B9%82)在 $O(\log n)$ 完成運算。由於第一邊個的頭和第 $n$ 個邊的尾必須相同(圖形為 $n$ 邊形)，對答案的貢獻為 $C_{WW} + C_{BB}$
利用上述的技巧，枚舉白石的數量 $W$，對應的矩陣為：

\begin{bmatrix}
  \binom{d - 1}{W - 2} & \binom{d - 1}{W - 1} \\\\
  \binom{d - 1}{W - 1} & \binom{d - 1}{W}
\end{bmatrix}

時間複雜度：$O(d \log n)$

[Solution Code](https://atcoder.jp/contests/abc256/submissions/38152274)

## [Ex - I like Query Problem](https://atcoder.jp/contests/abc256/tasks/abc256_h)

第二種操作：區間改值，可以運用線段樹 + 懶人標記 $O(\log n)$ 更新。

第一種操作：這是本題最難的地方，由於更新後的加總無法直接計算出來，我們只能分段更新。把 $[L, R + 1)$ 拆成很多小區間 $[L, s_1), [s_1, s_2), [s_2, s_3), \dots [s_k, R + 1)$ 使得每一個小區間的數值都相同。這樣就能套用區間改值，把每個區間更新成 $\lfloor \frac{A_{s_i}}{x}\rfloor$。

接下來我們要分析第一種操作的時間複雜度。在切成小區間時，只有包含最左邊和最右邊數值的兩個區間有可能被切開變成新的小區間。因此，我們最多有 $O(n + q)$ 個小區間。而對於一個小區間，能夠被除的次數不會超過 $O(\log \max(A))$ 次，所以總操作次數的上限為 $O((n + q) \log \max(A))$。而因為單一操作 (區間改值) 的時間複雜度為 $O(\log n)$，整體的時間複雜度為 $O((n + q) \log n \log \max (A))$

第一種操作還能優化，改成分成很多個區間 $P_1, P_2, \dots P_K$ 使得 $\lfloor \frac{\min(P_i)}{x} \rfloor = \lfloor \frac{\max(P_i)}{x} \rfloor$，這樣區間的總數會更少，常數會比較小。

[Solution Code](https://atcoder.jp/contests/abc256/submissions/37712592)