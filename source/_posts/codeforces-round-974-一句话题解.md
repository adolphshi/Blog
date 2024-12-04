---
layout: blog
title: Codeforces Round 974 一句话题解
date: 2024-12-04T18:58:00.000Z
cover: https://adolphshi.netlify.app/images/uploads/snipaste_2024-09-19_19-28-52.png
coverWidth: "812"
coverHeight: "460"
tags:
  - OI
  - VP
categories:
  - CF补全计划
---
CF2014.[Codeforces Round 974 (Div. 3)](https://codeforces.com/contest/2014) solved(7/8)

这篇是一个简要题解,只有题解,不保证能看懂.

<!---more--->

## A. Robin Helps

模拟即可,800分还有什么好说的.

## B. Robin Hood and the Major Oak

我们发现,$i^i$ 是不改变奇偶性的所以直接判断奇数个数即可.

## C. Robin Hood in Town

发现最大值是不会变的,因此我们只需要将第$\left\lfloor\frac{n}{2}\right\rfloor+1$ ,与最大值判断需要加多少次即可.注意需要开`long long` 以及在$n\leq2$ 时判无解即可.

## D. Robert Hood and Mrs Hood

记录下每一个端点,维护两个指针$l,r$ 每一次统计指针间的个数即可.开始节点与结束节点分开统计.

## E. Rendez-vous de Marian et Robin

这个题比较有意思,我们可以将每一个点拆分成两个点,一组点链接未骑马的边,另一组点链接骑马的点,在每一个有马的点建一条链接两个点的双向边.然后对于开始和结束点单独做`dij` 即可.对于每一个节点答案为从两个点到当前点的加和.注意对拆分开的点分别取最小值(共四种).

## F. Sheriff's Defense

动态规划,设$dp_{i,0/1}$ 表示当前点选/不选的最大值,每一次转移都在转移中取$dp_{u,0}=\Sigma\max(dp_{v,0},dp_{v,1})\\dp_{u,1}=\Sigma\max(dp_{v,0},dp_{v,1}-2c)$

其中v为u的孩子,做树形DP即可

## G. Milky Days

维护一个双端链表,我们每一次取队头的时间戳,若小于保质期就弹出,然后从后面弹出牛奶满足要求,当加进新的牛奶时直接`push_back` 就行.

## H. Robin Hood Archery

排序之后莫队,模板秒了.

## code

后面再给

