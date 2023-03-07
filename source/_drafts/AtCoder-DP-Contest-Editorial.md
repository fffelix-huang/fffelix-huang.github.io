---
title: AtCoder DP Contest 題解
categories: [競程, 題解, AtCoder]
tags: [DP, 動態凸包, 線段樹, 矩陣快速冪]
index_img:
banner_img:
---

## [A - Frog 1](https://atcoder.jp/contests/dp/tasks/dp_a)

定義 $dp[i]$ 為走到 $i$ 的最小花費，我們可以列出：

$$
dp[i] =
\begin{cases}
0 & i = 0 \\\\
|h_1 - h_0| & i = 1 \\\\
\min(dp[i - 2] + |h_i - h_{i - 2}|, dp[i - 1] + |h_i - h_{i - 1}|) & 2 \leq i < n
\end{cases}
$$

* 1, 2：base case
* 3：從 $i - 2$ 或是 $i - 1$ 跳到 $i$

答案就是 $dp[n - 1]$。

時間複雜度：$O(n)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38078914)

## [B - Frog 2](https://atcoder.jp/contests/dp/tasks/dp_b)

與前一題相同，定義 $dp[i]$ 為走到 $i$ 的最小花費，我們可以列出：

$$
dp[i] =
\begin{cases}
0 & i = 0 \\\\
\min\limits_{\max(i - k, 0) \leq j < i}\\{dp[j] + |h_i - h_j|\\} & 1 \leq i < n
\end{cases}
$$

* 1：base case
* 2：從 $\max(i - k, 0) \leq j < i$ 跳到 $i$

答案就是 $dp[n - 1]$。

時間複雜度：$O(nk)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38079227)

## [C - Vacation](https://atcoder.jp/contests/dp/tasks/dp_c)

定義 $h_{i, j}$ 為第 $i$ 天從事活動 $j$ 的開心值 $(h_{i, 0} = a_i, h_{i, 1} = b_i, h_{i, 2} = c_i)$
定義 $dp[i][j]$ 為前 $i$ 天且最後一天從事活動 $j$ 的最大開心值，我們可以列出：

$$
dp[i][j] =
\begin{cases}
h_{0, j} & i = 0 \\\\
\max\limits_{j \neq k}\\{dp[i - 1][k] + h_{i, j}\\} & 1 \leq i < n
\end{cases}
$$

* 1：base case
* 2：連續兩天的活動不能相同 $(j \neq k)$

答案就是 $\max\limits_{j \in \\{0, 1, 2\\}}\\{dp[n - 1][j]\\}$

時間複雜度：$O(n)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38079363)

## [D - Knapsack 1](https://atcoder.jp/contests/dp/tasks/dp_d)

經典的背包問題，定義 $dp[i][j]$ 為使用前 $i$ 個物品湊出重量為 $j$ 的最大價值，我們可以列出：

$$
dp[i][j] =
\begin{cases}
0 & i = 0, j < w_0 \\\\
v_0 & i = 0, j \geq w_0 \\\\
dp[i - 1][j] & 1 \leq i < n, j < w_i \\\\
\max(dp[i - 1][j], dp[i - 1][j - w_i] + v_i) & 1 \leq i < n, j \geq w_i
\end{cases}
$$

* 1, 2：base case
* 3：放不下第 $i$ 個物品，所以答案與前 $i - 1$ 個物品湊出的最大價值相同
* 4：放得下第 $i$ 個物品，可以選擇放或不放：
    * 不放：與 case 3 相同
    * 放：則我們由前 $i - 1$ 個湊出重量 $j - w_i$ 的最大價值，再加上 $v_i$
    * 上述兩個 case 取 $\max$

答案就是 $dp[n - 1][w]$

時間複雜度：$O(nw)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38079543)

## [E - Knapsack 2](https://atcoder.jp/contests/dp/tasks/dp_e)

定義 $dp[i][j]$ 為使用前 $i$ 個物品湊出價值為 $j$ 的最小重量，我們可以列出：

$$
dp[i][j] =
\begin{cases}
0 & i = 0, j = 0 \\\\
w_0 & i = 0, j = v_0 \\\\
dp[i - 1][j] & 1 \leq i < n, j < v_i \\\\
\min(dp[i - 1][j], dp[i - 1][j - v_i] + w_i) & 1 \leq i < n, j \geq v_i \\\\
\infty & 其他
\end{cases}
$$

* 1, 2, 5：base case
* 3：$j < v_i$，沒有拿第 $i$ 個物品，答案與前 $i$ 個物品湊出價值為 $j$ 相同
* 4：$j \geq v_i$，可以選擇拿或不拿
    * 不拿：與 case 3 相同
    * 拿：前 $i - 1$ 個物品湊出價格 $j - v_i$ 的最小重量，再加上 $w_i$
    * 上述兩個 case 取 $\min$

答案就是 $\max\limits_{dp[n - 1][j] \leq w} j$

時間複雜度：$O(n \sum v)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38152172)

## [F - LCS](https://atcoder.jp/contests/dp/tasks/dp_f)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38080236)

## [G - Longest Path](https://atcoder.jp/contests/dp/tasks/dp_g)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38080448)

## [H - Grid 1](https://atcoder.jp/contests/dp/tasks/dp_h)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38080581)

## [I - Coins](https://atcoder.jp/contests/dp/tasks/dp_i)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38080795)

## [J - Sushi](https://atcoder.jp/contests/dp/tasks/dp_j)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38123358)

## [K - Stones](https://atcoder.jp/contests/dp/tasks/dp_k)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38081006)

## [L - Deque](https://atcoder.jp/contests/dp/tasks/dp_l)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38081845)

## [M - Candies](https://atcoder.jp/contests/dp/tasks/dp_m)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38082029)

## [N - Slimes](https://atcoder.jp/contests/dp/tasks/dp_n)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38082224)

## [O - Matching](https://atcoder.jp/contests/dp/tasks/dp_o)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38120484)

## [P - Independent Set](https://atcoder.jp/contests/dp/tasks/dp_p)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38120580)

## [Q - Flowers](https://atcoder.jp/contests/dp/tasks/dp_q)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38120726)
// TODO

## [R - Walk](https://atcoder.jp/contests/dp/submissions/38152222)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38120853)

## [S - Digit Sum](https://atcoder.jp/contests/dp/tasks/dp_s)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38121108)

## [T - Permutation](https://atcoder.jp/contests/dp/tasks/dp_t)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38122296)

## [U - Grouping](https://atcoder.jp/contests/dp/tasks/dp_u)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38121440)

## [V - Subtree](https://atcoder.jp/contests/dp/tasks/dp_v)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38152095)

## [W - Intervals](https://atcoder.jp/contests/dp/tasks/dp_w)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38150749)

## [X - Tower](https://atcoder.jp/contests/dp/tasks/dp_x)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38149091)

## [Y - Grid 2](https://atcoder.jp/contests/dp/tasks/dp_y)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38149399)

## [Z - Frog 3](https://atcoder.jp/contests/dp/tasks/dp_z)

$DP[i] = QQ$

時間複雜度：$O(N)$ 或 $O(1)$

[程式碼](https://atcoder.jp/contests/dp/submissions/38122627)