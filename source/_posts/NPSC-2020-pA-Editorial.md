---
title: NPSC 2020 pA 邊緣人 題解
categories: [競程, 題解, 其他]
tags: [數論分塊, 差分]
date: 2022-08-02 10:45:11
---

[題目連結](https://contest.cc.ntu.edu.tw/npsc2020/teamclient/semi_senior.pdf)

## 小故事
NPSC 是我參加的第一場比賽，理所當然的被打爆，只解出最水的pB。當時跟隊友花了很多時間在解這題，但是由於數學知識不足，都在原地打轉。最近剛好翻到這題，想說花點時間想一下，馬上就有了突破，也順利 $\color{green}{\text{AC}}$，算是彌補了當時的遺憾。

## 題目敘述
有 $n$ 個人要分組，一組 $x$ 個人，編號 $1$ 到 $x$ 的人會分到一組，$(x + 1)$ 到 $2x$ 會分到一組，依此類推。最後可能會有一些人組員人數不足 $x$，我們稱這些人為邊緣人。我們定義編號 $i$ 的人的邊緣值 $f(i)$ 為：在 $x = 1, x = 2, \dots , x = n$ 這 $n$ 個情況中，編號 $i$ 的人成為邊緣人的情況總數。
給定 $L, R$，請求出 $f(L), f(L + 1), \dots , f(R)$。

* $1 \leq n \leq 2^{40}$
* $L \leq R \leq n$
* $R − L \leq 3 \cdot 10^5$

## 觀察
$f(i)$ 可以改寫為：

$$f(i) = \sum_{g = 1}^n [\lfloor \frac{n}{g} \rfloor \cdot g < i]$$

其中 $[P]$ 是[艾佛森括號](https://zh.wikipedia.org/wiki/%E8%89%BE%E4%BD%9B%E6%A3%AE%E6%8B%AC%E5%8F%B7)。

可以發現我們要計算不同的 $f(i)$ 差別都只在於最右邊的 $\text{< }i$。此外，假設有兩個人編號 $x < y$，如果分成 $g$ 人一組時 $x$ 會變成邊緣人，那麼 $y$ 也會在分成 $g$ 人一組時變成邊緣人。因此對於所有的分組，假設 $g$ 人一組，我們都要找到最後一組 (第 $\lfloor \frac{n}{g} \rfloor$ 組) 的最後一個人 (編號為 $\lfloor \frac{n}{g} \rfloor \cdot g$ )，將他後面的所有人的邊緣值都 $+1$，可以運用差分的技巧 $O(1)$ 做區間加值。

另外一個關鍵是左邊的 $d = \lfloor \frac{n}{g} \rfloor$ 只有 $O(\sqrt{n})$ 種不同的值。如果有辦法快速的掃過所有的 $d$，那對本題有很大的幫助。我們可以運用[數論分塊](https://oi-wiki.org/math/number-theory/sqrt-decomposition/)的技巧，其中最小的 $b$ 使得 $\lfloor \frac{n}{a}\rfloor < \lfloor \frac{n}{b} \rfloor$ 就是 $\lfloor \frac{n}{\lfloor \frac{n}{a} \rfloor} \rfloor + 1$。因此，我們可以從 $a = 1$ 在經過 $O(\sqrt{n})$ 個值後到達 $n$。
同時，$x \in [L', R']$ 且 $\lfloor \frac{n}{L'} \rfloor = \lfloor \frac{n}{R'} \rfloor$ 的所有 $\lfloor \frac{n}{x} \rfloor \cdot x$ 會形成等差數列 (因為 $\lfloor \frac{n}{x} \rfloor$ 都相同，相鄰的 $x$ 只相差 $1$)。利用這個特性我們只需要紀錄每個區間的首項 $\lfloor \frac{n}{L'} \rfloor \cdot L'$，末項 $\lfloor \frac{n}{R'} \rfloor \cdot R'$，公差 $\lfloor \frac{n}{L'} \rfloor$。

## 實作

先找出所有 $[L', R']$，在題目要求的區間 $[L, R]$ 加上值
$[L', R']$ 只有 $O(\sqrt{n})$ 個，且公差 $d$ 增加的很快 (其實就是[調和級數](https://zh.wikipedia.org/zh-tw/%E8%B0%83%E5%92%8C%E7%BA%A7%E6%95%B0))

時間複雜度為：$O(\sqrt{n} + (R - L) \log \sqrt{n})$

<details><summary>Solution Code</summary>
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
	ios::sync_with_stdio(false);
	cin.tie(0);
	long long N, L, R;
	cin >> N >> L >> R;
	vector<tuple<long long, long long, long long>> seg;
	for(long long i = 1; i <= N;) {
		long long p = N / i;
		long long j = N / p + 1;
		seg.emplace_back(i * p + 1, (j - 1) * p + 1, p);
		i = j;
	}
	const long long OFFSET = L;
	vector<long long> ans(R - L + 1);
	for(auto& [s, e, p] : seg) {
		long long step = (s < L ? (L - s + p - 1) / p : 0);
		ans[L - OFFSET] += step;
		s += step * p;
		while(s <= e && s <= R) {
			ans[s - OFFSET] += 1;
			s += p;
		}
	}
	for(int i = 1; i < R - L + 1; ++i) {
		ans[i] += ans[i - 1];
	}
	for(int i = 0; i < R - L + 1; ++i) {
		cout << ans[i] << " \n"[i == R - L];
	}
	return 0;
}
```
</details>