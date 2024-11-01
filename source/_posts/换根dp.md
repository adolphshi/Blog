---
layout: blog
title: 换根DP
date: 2024-07-20T10:18:00.000Z
updated: 2024-07-20T10:18:00.000Z
cover: https://cdn.luogu.com.cn/upload/image_hosting/p5kih24t.png
tags:
  - OI
  - DP
  - 寄巧
categories:
  - 算法学习
---
## 换根DP的原理

对于一个树，如果我们要求以树上所有的节点为根的DP值，我们可以一个一个求，但是这样是 $O(n^2)$ 的，但是如果我们可以先求出以特定节点为根（通常为1） 的DP值，再快速的计算将这个根换为其他根的代价
，这样我们就可以两遍DFS $O(n)$ 地求出答案了。

## 换根DP 的常见模型

一般的换根贡献可能长成这个样子：  
新根=旧根去除掉新根的贡献+新根原来的贡献

在DP过程中可能会遇到三种更新方式：
+ 加
+ 乘
+ 最大值

对于这三种情况我们分别讨论该如何去除旧根中新根的贡献。

### 子树加的换法

这就很简单了，在换根时减去要换子树贡献即可。

eg. [CF1324F Maximum White Subtree](https://www.luogu.com.cn/problem/CF1324F)


### 子树乘的换法

这个也很简单，因为可以除掉新根的贡献，但是在取模时需要用乘法逆元。  
这就会有一个问题：当当前模数没有乘法逆元时就无法计算。

所以我们可以使用前缀积和后缀积的方式来进行求解 
 
$pre_i = a_1 \times a_2 \cdots \times a_i$  

$suf_i = a_n \times a_{n-1} \cdots \times a_i$  

$\frac{a_1\times a_2\cdots a_n}{a_i}=pre_{i-1}\times suf_{i+1}$

eg.[CF543D Road Improvement](https://www.luogu.com.cn/problem/CF543D)

代码还用我贴吗？ ~~已经想到后来回来复习时的我的懵逼的表情了~~


### 子树最大值

这个有点麻烦（但也很简单）,我们发现，当当前子树不对他的父节点作贡献时转移非常简单，但是如果当前子树就是最大值时我们就需要再找一个次大值，所以我们在第一次维护时再同时维护次大值即可。

eg.[CF708C Centroids](https://www.luogu.com.cn/problem/CF708C)

[![Page Views Count](https://badges.toozhao.com/badges/01J3FJT8Q8RK9KE2XW173FM96Y/green.svg)](https://badges.toozhao.com/stats/01J3FJT8Q8RK9KE2XW173FM96Y "Get your own page views count badge on badges.toozhao.com")
