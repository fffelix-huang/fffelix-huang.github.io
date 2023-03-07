---
title: 網路流建圖技巧與應用
categories: [競程, 演算法]
tags: [網路流]
date: 2023/03/03
index_img:
banner_img:
---

## 前言

網路流的定理和建圖的技巧太多了，因此特別整理一些常用的定理與技巧。

## 先備知識

知道 flow 在做什麼~~和會抄模板~~、判斷二分圖
Max Flow (Dinic) [模板](https://github.com/fffelix-huang/CP-stuff/blob/master/codebook/graph/Dinic.h)
Min-Cost Max-Flow (MCMF) [模板](https://github.com/fffelix-huang/CP-stuff/blob/master/codebook/graph/MCMF.h)

## 基本介紹

### 割 (Cut)

切斷邊後使得 flow graph 中的 $S$ 與 $T$ 不連通的邊權總和。

max flow $=$ min cut ([Max-flow min-cut theorem](https://en.wikipedia.org/wiki/Max-flow_min-cut_theorem))

### 點覆蓋 (Vertex Cover)

從圖中選擇一些頂點，使得每條邊的兩端點都至少有一邊的頂點有被選到。

| <img src=vertex-cover-valid.jpg width=350 height=325> | <img src=vertex-cover-invalid.jpg width=350 height=325> |
| :---: | :---: |
| vertex cover | 不是 vertex cover，因為紅色的邊兩端<br />的頂點都沒有被選到 |

二分圖中，minimum vertex cover $=$ maximum matching ([Kőnig's theorem](https://en.wikipedia.org/wiki/K%C5%91nig%27s_theorem_(graph_theory)))

二分圖的 minimum weighted vertex cover 可以轉換為 max flow 問題，建圖的方式如下：
1. 左邊的點連向 $S$，capacity 為點權
2. 右邊的點連向 $T$，capacity 為點權
3. 連接中間原本的邊，capacity 為 $\infty$

<details><summary>證明</summary>
對於每條中間的邊，因為 capacity 為 $\infty$，cut 只會發生在邊的左端或右端，滿足 vertex cover 的性質。因此這張圖的 min cut 就會是 minimum weighted vertex cover。
</details>

### 獨立集 (Independent Set)

從圖中選擇一些頂點，使得選擇的任兩個頂點之間沒有邊。

| <img src=independent-set-valid.jpg width=400 height=200> | <img src=independent-set-invalid.jpg width=400 height=200> |
| :---: | :---: |
| independent set | 不是 independent set，因為紅色的邊連接<br />兩個選擇的頂點 |

二分圖中，maximum independent set $= n -$ minimum vertex cover

<details><summary>證明</summary>
根據定義，vertex cover 中的每一條邊至少被選了一個頂點。因此，我們考慮 vertex cover 的點集合 $S$ 的補集 $S^\prime$，由於每條邊至多被選了一個頂點，沒有邊連接兩個 $S^\prime$ 中的點，因此 $S^\prime$ 為 independent set。綜上所述我們可以得到：

\begin{aligned}
|S^\prime| &= n - |S| \\\\
\iff \max |S^\prime| &= \max (n - |S|) \\\\
\iff \max |S^\prime| &= n - \min |S| \\\\
\end{aligned}

</details>

## 常見技巧

### 分裂節點

對於在節點上限制 capacity 的情況，我們可以透過分裂節點的技巧，把節點上的 capacity 轉移到邊上。

原本：

<img src=vertex-capacity.jpg style="display:block; margin: 0 auto;"/>

分裂節點：

<img src=edge-capacity.jpg style="display:block; margin: 0 auto;"/>

### Edge-disjoint paths

### Node-disjoint paths

### Node-disjoint path cover

### General path cover

## 例題

> [Codeforces - Petya and Graph](https://codeforces.com/contest/1082/problem/G)
> 給定一張 simple graph (無環、無重邊)，頂點權重 $a_i$，邊 $(u_i, v_i)$ 權重 $w_i$。定義一個圖 $G = (V, E)$ 的分數為 $\sum_{w \in E} w_i - \sum_{a \in V} a_i$，請求出分數最大的子圖的分數。

<details><summary>Solution</summary>

我們先選擇全部的邊，再把最終沒有選擇的邊「還回去」，並且我們希望最小化還回去的費用。

對於每條邊，我們有兩種選擇：保留或不保留。因此，我們可以設計一張二分圖，讓左邊的頂點作為保留，右邊的頂點作為不保留。要讓每條邊都能正確的做出選擇，我們可以利用 vertex cover 的性質。考慮兩種情況：

* 保留這條邊，那麼這條邊所連接的兩個頂點也都要選擇，要還 $a_{u_i}$ 和 $a_{v_i}$ 回去。所以我們把左邊頂點 $i$ 的點權設為 $a_i$。
* 不保留這條邊，那麼我們要還 $w_i$ 回去。所以我們把右邊頂點 $i$ 的點權設為 $w_i$。

有了二分圖的頂點之後，我們要加入邊。對於每條邊 $(u_i, v_i)$，我們從左邊的 $u_i$ 和 $v_i$ 連接到右邊的 $i$。在這樣的情況下，vertex cover 就等同於對於每條邊，我們都要在保留與不保留中做出選擇，而選擇的花費即為二分圖中的點權。因此這張二分圖的 minimum weighted vertex cover 即為還回去的最少費用。因此答案為 $\sum w_i -$ minimum weighted vertex cover。

我們用以下這張圖舉例，紅色的是點權，綠色的是邊權。

<img src=simple-graph-00.jpg style="display:block; margin: 0 auto;"/>

根據這張圖建的二分圖長這樣：

<img src=bipartite-00.jpg style="display:block; margin: 0 auto;" width=420 height=450/>

把二分圖的 minimum weighted vertex cover 轉換成 max flow：

<img src=flow-graph-00.jpg style="display:block; margin: 0 auto;"/>

</details>

> [2022 清大 NCPC 校內賽 Spaceship Mission](https://drive.google.com/file/d/1UqtLTLxI16I2fuTF4bv4pdcLh0ixmlnd/view)
> 在平面上有許多敵人，你位於 $(0, 0)$。你可以進行以下兩種操作：
>
> 1. 發射一道雷射，摧毀直線上所有敵人
> 2. 發動衝擊波，摧毀最近的敵人 (若有多個距離相同則一併摧毀)
>
> 每道雷射花費 $L$ 元，每個衝擊波花費 $S$ 元，問你摧毀所有敵人的最小花費？

<details><summary>Solution</summary>

建立一張二分圖，左邊為雷射，點權為 $L$，右邊為衝擊波，點權為 $S$。對於每個敵人，我們把能夠打到他的雷射和衝擊波連邊。現在問題就變成從二分圖選擇一些點，使得每條邊的兩個端點都至少有一邊被選到，而且選擇的點權和最小。答案就是這張二分圖的 minimum weighted vertex cover。

</details>

## 題目

## Reference

[[Tutorial, Flows] Project Selection Problem](https://codeforces.com/blog/entry/101354)