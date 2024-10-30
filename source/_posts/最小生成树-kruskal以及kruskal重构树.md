---
layout: blog
title: 最小生成树(kruskal以及kruskal重构树)
date: 2024-10-30T14:58:00.000Z
cover: https://cdn.luogu.com.cn/upload/image_hosting/imbnzgmv.png
coverWidth: "812"
coverHeight: "460"
tags:
  - OI
  - 算法
  - 图论
  - kruskal
categories:
  - 算法学习
---
**声明:本篇文章不太适合用作最小生成树入门,可能不是很适合刚接触最小生成树的初学者,建议先对kruskal算法有一定了解再来观看**

<!--- more --->

## kruskal最小生成树 
这个最小生成树本质就是一种贪心,每一次取边权最小的边,如果已经联通就不加,否则就让这两个连通块联通,用并查集维护.

但是值得注意的是
