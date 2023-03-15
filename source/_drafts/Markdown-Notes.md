---
title: Markdown 筆記
date: 2023-01-03 11:05:29
categories:
tags:
banner_img:
index_img:
---

## Emojis

:fire: :smiley: :u7533: :u5272:

```markdown
:fire: :smiley: :u7533: :u5272:
```

!!! info "Definition"
	The **product** of formal power series $A(x)$ and $B(x)$, is also defined by expanding it as an arythmetic expression:


	$$
	A(x) B(x) = \left(\sum\limits_{i=0}^\infty a_i x^i \right)\left(\sum\limits_{j=0}^\infty b_j x^j\right) = \sum\limits_{i,j} a_i b_j x^{i+j} = \sum\limits_{k=0}^{\infty} c_k x^k = C(x),
	$$

	where the coefficients $c_0, c_1, \dots$ are define as finite sums

	$$
	c_k = \sum\limits_{i=0}^k a_i b_{k-i}.
	$$

	The sequence $c_0, c_1, \dots$ is also called a **convolution** of $a_0, a_1, \dots$ and $b_0, b_1, \dots$, generalizing the concept to infinite sequences.

Thus, polynomials may be considered formal power series, but with finite number of coefficients.

Formal power series play a crucial role in enumerative combinatorics, where they're studied as [generating functions](https://en.wikipedia.org/wiki/Generating_function) for various sequences. Detailed explanation of generating functions and the intuition behind them will, unfortunately, be out of scope for this article, therefore the curious reader is referenced e.g. [here](https://codeforces.com/blog/entry/103979) for details about their combinatorial meaning.

However, we will very briefly mention that if $A(x)$ and $B(x)$ are generating functions for sequences that enumerate some objects by number of "atoms" in them (e.g. trees by the number of vertices), then the product $A(x) B(x)$ enumerates objects that can be described as pairs of objects of kinds $A$ and $B$, enumerates by the total number of "atoms" in the pair.

!!! Example
	Let $A(x) = \sum\limits_{i=0}^\infty 2^i x^i$ enumerate packs of stones, each stone colored in one of $2$ colors (so, there are $2^i$ such packs of size $i$) and $B(x) = \sum\limits_{j=0}^{\infty} 3^j x^j$ enumerate packs of stones, each stone colored in one of $3$ colors. Then $C(x) = A(x) B(x) = \sum\limits_{k=0}^\infty c_k x^k$ would enumerate objects that may be described as "two packs of stones, first pack only of stones of type $A$, second pack only of stones of type $B$, with total number of stones being $k$" for $c_k$.

In a similar way, there is an intuitive meaning to some other functions over formal power series.

## 插入圖片

<figure>
<img src=/img/moon.jpg width="500" height="200">
<figcaption align="center">Fig.1 - Moon Wallpaper</figcaption>
</figure>

```markdown
<figure>
<img src=/img/moon.jpg width="500" height="200">
<figcaption align="center">Fig.1 - Moon Wallpaper</figcaption>
</figure>
```

## 註腳

喵咪[^1]
永遠不會放棄你[^2]

```markdown
喵咪[^1]
永遠不會放棄你[^2]

[^1]: https://www.youtube.com/watch?v=p7OweGRiv7s
[^2]: https://www.youtube.com/watch?v=dQw4w9WgXcQ
```

[^1]: https://www.youtube.com/watch?v=p7OweGRiv7s
[^2]: https://www.youtube.com/watch?v=dQw4w9WgXcQ