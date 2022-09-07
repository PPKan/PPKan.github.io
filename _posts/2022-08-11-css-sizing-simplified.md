---
layout: post
title: "CSS sizing: rem % vw em px 超簡易使用指南"
subtitle: "超簡單幾行字教你如何選css的尺寸"
date: 2022-08-11
author: "Peter"
header-img: "img/post-bg-2022.jpg"
tags: [web dev, css]
---

### rem

每個 element 的 font-size, width, height 如果需要指定就用 rem，不需要指定就用%，或是 auto。

### %

比例縮放的時候用

### vw

1. reset css 有的會有，請 specify 你的 body 為 100vw，避免網頁超出去。
2. 1200px 以上的 media query 內，把 rem 換成 vw。這樣在電腦可以有完美尺寸的畫面。不過這有點看人用，用 rem 換 size 的網頁也大有人在。。。

### em

在 font-sizing 指定的情況下，像是 letter-spacing 等等的間距的時候用，可以根據你的 font-size 調整。

### px

做 border、box-shadow 等小東西的時候用

---

大概 4john，請記得基本尺寸一定用 rem，除了可以依據 user preference 自動更動以外，在手機上面也顯示得出來，如果你在 1200px 下用 vw 的話，字會超級小。。。
