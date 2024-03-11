---
title: Ubuntu 22.04 音量控制按鈕消失解決方法
date: 2024-03-11
categories: [Ubuntu]
tags: [Ubuntu]
index_img: /img/banner/ubuntu-volume-button-disappear.png
banner_img:
---

今天不知道不小心動了什麼東西導致音量控制的按鈕消失，畫面如下圖

![Ubuntu 音量控制按鈕消失](ubuntu-volume-button-disappear.png)

此情況甚至連鍵盤快捷鍵 `Fn + 右鍵` 都沒辦法控制音量。

爬了很多文章甚至重新灌了 kernel 都沒有解決問題，後來發現了一個簡單的指令就能讓它顯示。

打開 terminal 打上以下指令

```bash
pulseaudio --start
```

大功告成！

![Ubuntu 音量控制按鈕出現](ubuntu-volume-button-appear.png)
