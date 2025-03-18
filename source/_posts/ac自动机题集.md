---
layout: blog
title: AC自动机题集
date: 2025-03-18T19:00:00.000Z
cover: https://cdn.luogu.com.cn/upload/image_hosting/imbnzgmv.png
coverWidth: "812"
coverHeight: "460"
tags:
  - OI
  - 字符串
  - 算法
categories:
  - 算法学习
---
题目不多，大多是一些经典的题目，AC自动机比较重要，但是好像字符串考的越来越少了。但是数据结构很有可能作为某题的一部分出现。我写过的AC自动机题目较简单，后面可能再加。建议（？）搭配[字符串全家桶](https://adolphshi.netlify.app/2023/12/06/%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%85%A8%E5%AE%B6%E6%A1%B6/)食用。

<!-- more -->

## 简介

AC自动机是一个**离线算法**(在没有奇技淫巧的情况下),通常用于匹配（很多种形式，后面会一一讲解）。AC自动机主要是和DP结合做一些统计的问题，在必要时还会上一些其他的数据结构。

## 板子题

这一部分的AC自动机都是AC自动机最为基础的应用，建议在这一阶段将AC自动机的板子拍熟练，后面的AC自动机题目就不需要每一次都重写一遍了。（在这一阶段建议多写）

### 【模板】AC自动机 （[简单版](https://www.luogu.com.cn/problem/P3808)/[简单版II](https://www.luogu.com.cn/problem/P3796)/[加强版](https://www.luogu.com.cn/problem/P5357)）

其实题目都差不多，AC自动机板子都不需要改，只需要在统计答案的地方稍作修改即可。以至于统计时的什么优化，我认为没有优化的统计至少在现在没有什么用处，所以在全家桶中的代码就是直接在fail树上的dfs。

至于为什么需要dfs，我们会发现，当一个匹配到一个点时，其实它fail树上的全部祖先也都被匹配了。因此需要dfs fail树进行统计。

代码见全家桶。

### P3966 [[TJOI2013] 单词](https://www.luogu.com.cn/problem/P3966)

将所有给出的单词一方面作为模式串，建出AC自动机，然后将所有单词连在一起，单词与单词之间用不存在的字符隔开，组成母串。最后就和模板题一样了。

当然，这题还有其他的做法，但需要对AC自动机的fail树有更深层次的理解。这里也讲一下：

因为fail树上一次是匹配一个从根开始的链，一个点的全部可能的匹配次数就是这个点的子树。所以们统计出每一个节点在于多少个字符串中，然后建出fail树，统计每一个字符串结束位置在fail树上的子树和即可。

在实现的过程中我们发现因为fail树深度是递减的，因此不一定要建出fail树，直接倒着扫即可

关键代码

```cpp
void get_ans()
{
	F_(i,cnt,1)
		siz[tr[xl[i]].fail]+=siz[xl[i]];
}
```

### P3121 [[USACO15FEB] Censoring G](https://www.luogu.com.cn/problem/P3121)

正常匹配，在匹配过程中开一个栈记录匹配的节点，然后每一次匹配完成就弹栈到对应的节点，然后继续匹配即可。

关键代码

```cpp
int st[maxn],top;
void AC()
{
	int len=strlen(c+1),now=0;
	_F(i,1,len)
	{
		pos[i]=now=tr[now].nex[c[i]-'a'];
		st[++top]=i;
		if(tr[now].tg)
		{
			top-=tr[now].tg;
			now=pos[st[top]];
		}
	}
}
```

::: success

### 小总结

这一部分中,比较重要的就是掌握**fail树上节点的对应的字符串**上的关系,这一点非常重要,几乎所有的AC自动机的题目也都用了这种关系

:::

## DP题

这一部份是AC自动机与DP的结合题.此时我们可以将AC自动机看成一个图,所以AC自动机上DP和图上DP差不多,但是中间又添加了AC自动机以及fail树所含有的意义.状态中通常含有自动机的节点.最常见的就是统计不包含字符串的字符串.

### P4052 [[JSOI2007] 文本生成器](https://www.luogu.com.cn/problem/P4052)

最基础的AC自动机上DP,但也不是那么好想.发现直接统计不是很好统计,考虑正难则反.我们考虑统计出不含任何一个子串的字符串的个数.

那么我们就应该不难得到DP的状态和转移方程:设 $dp_{i,j}$ 代表生成的长度为 $i$ 的字符串,并且当前在AC自动机的 $j$ 号节点时的非法字符串个数.(因为正难则反)

然后我们发现,当一个字符串不含一个子串时,它不会经过那个子串在AC自动机上的节点以及那个节点在fail树上的子树中的节点.

所以我们只需要在构建AC自动机时将终止标记沿着fail树传递下去即可.(小技巧,因为AC自动机是bfs构建的,这样不需要dfs)

关键代码(AC自动机构建)

``` cpp
_F(i,0,25)
		if(tr[0].nex[i]) q.push(tr[0].nex[i]),tr[tr[0].nex[i]].fail=0;
	while(!q.empty())
	{
		int t=q.front();q.pop();
		_F(i,0,25)
		{
			if(tr[t].nex[i])
				q.push(tr[t].nex[i]),
				tr[tr[t].nex[i]].fail=tr[tr[t].fail].nex[i],
				tr[tr[t].nex[i]].tg|=tr[tr[tr[t].nex[i]].fail].tg;//加上这句
			else
				tr[t].nex[i]=tr[tr[t].fail].nex[i];
		}
	}
```



(DP)

```cpp
	dp[0][0]=1;
	_F(i,0,m-1)
		_F(j,0,cnt)
			_F(k,0,25)
				if(!tr[tr[j].nex[k]].tg)
					dp[i+1][tr[j].nex[k]]=(dp[i+1][tr[j].nex[k]]+dp[i][j])%p;
	ans=ksm(26,m);
	_F(i,0,cnt) ans=(ans-dp[m][i]+p)%p;
```

### P3041 [[USACO12JAN] Video Game G](https://www.luogu.com.cn/problem/P3041)

这一题让我们找出长度一定的字符串包含的子串最多能有多少个.套路地我们设动态转移方程 $dp_{i,j}$ 代表长度为 $i$ 的字符串并且当前在 $j$ 号节点处最多能匹配多少个子串.

我们在构建AC自动机顺便将每个节点是多少个字符串的终止节点记下来.然后我们就不难得到方程$dp_{i,j} \larr\max {\{dp_{i-1,k}\}}+siz_j$ ,其中 $k$ 是能够到达 $j$ 的节点.

关键代码(注意细节清极小值)

```cpp
	dp[0][0]=0;
	_F(i,0,k-1)
	{
		_F(j,0,cnt)
		{
			_F(l,0,2)
			{
				dp[i+1][ch[j][l]]=max(dp[i+1][ch[j][l]],dp[i][j]+tg[ch[j][l]]);
			}
		}
	}
```

### P3311 [[SDOI2014] 数数](https://www.luogu.com.cn/problem/P3311)

本题和上面一道题类似,只不过需要多加一维代表是否贴着上限(和数位DP类似),有上限的需要限制转移.另外,此题需要使用滚动数组.

关键代码:

```cpp
void DP()
{
	_F(i,1,m[1]-'0')
		if(!tr[tr[0].ch[i]].ed)
			dp[0][tr[0].ch[i]][i==m[1]-'0']=(dp[0][tr[0].ch[i]][i==m[1]-'0']+1)%p;
	int pos=0;
	_F(i,2,l)
	{
		pos^=1;
		memset(dp[pos],0,sizeof dp[pos]);
		_F(j,1,9)
			if(!tr[tr[0].ch[j]].ed)
				dp[pos][tr[0].ch[j]][0]=(dp[pos][tr[0].ch[j]][0]+1)%p;
		_F(j,0,cnt)
		{
			if(tr[j].ed) continue;
			if(dp[pos^1][j][0])
			{
				_F(k,0,9)
				{
					if(!tr[tr[j].ch[k]].ed)
					dp[pos][tr[j].ch[k]][0]=(dp[pos][tr[j].ch[k]][0]+dp[pos^1][j][0])%p;
				}
			}
			if(dp[pos^1][j][1])
				_F(k,0,m[i]-'0')
					if(!tr[tr[j].ch[k]].ed)
						dp[pos][tr[j].ch[k]][k==m[i]-'0']=(dp[pos][tr[j].ch[k]][k==m[i]-'0']+dp[pos^1][j][1])%p;
		}
	}
	int ans=0;
	_F(i,0,cnt)
		if(!tr[i].ed)
			ans=((ans+dp[pos][i][0])%p+dp[pos][i][1])%p;
	printf("%d\n",ans);
}
```



### CF86C [Genetic engineering](https://www.luogu.com.cn/problem/CF86C)

看出来这是一道DP题,我们依旧套路地设状态 $dp_{i,j}$ 为到达 $j$ 点,字符串长度为 $i$ 的合法方案数.但是这样会出现一个问题:当我们走到一个结束节点时,我们可以选择跳 fail 边然后继续走,这样走也是合法的(这就相当于缩短要求匹配的前缀),且有可能走出的节点与不跳fail边不同,所以这种状态是不合适的.

我们可以考虑一种新的状态:$dp_{i,j,k}$ 代表到达 $k$ 点,字符串长度为 $i$ 且最后 $j$ 个字符没有匹配的合法方案数.相较于之前新加了一个 $j$ 维度,这就相当于我们可以将未匹配的字符"堆"起来.后面一起处理.

我们维护一个辅助数组,$len_i$ 代表以 $i$ 结尾的字符串最长的长度.

然后就可以DP了.

``` cpp
	dp[0][0][0]=1;
	_F(i,0,n-1)
	{
		_F(j,0,ml)
		{
			_F(k,0,cnt)
			{
				if(dp[i][j][k])
				{
					_F(l,0,3)
					{
						int nex=act[k][l];
						if(tg[nex]>j)
							dp[i+1][0][nex]=(dp[i+1][0][nex]+dp[i][j][k])%p;//堆起来的一次直接匹配完
						else
							dp[i+1][j+1][nex]=(dp[i+1][j+1][nex]+dp[i][j][k])%p;//堆起来
					}
				}
			}
		}
	}
```



