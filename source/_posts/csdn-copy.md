---
title: CSDN 博客登入後複製解決方法
date: 2024-04-06
categories: [CSDN]
tags: [CSDN]
index_img:
banner_img:
---

CSDN 博客每次都會要求登入後複製，非常麻煩。只需要在按下 `F12` 進入 console 後貼上以下的指令即可。

```bash
javascript:window.oncontextmenu=document.oncontextmenu=document.oncopy=null; [...document.querySelectorAll('body')].forEach(dom => dom.outerHTML = dom.outerHTML); [...document.querySelectorAll('body, body *')].forEach(dom => {['onselect', 'onselectstart', 'onselectend', 'ondragstart', 'ondragend', 'oncontextmenu', 'oncopy'].forEach(ev => dom.removeAttribute(ev)); dom.style['user-select']='auto';});
```