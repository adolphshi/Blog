---
layout: blog
title: 最小生成树(kruskal以及kruskal重构树)
date: 2024-10-30T14:58:00.000Z
cover: https://cdn.luogu.com.cn/upload/image_hosting/kl5wx8q2.png
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
**声明:本篇文章不太适合用作最小生成树入门,可能不是很适合刚接触最小生成树的初学者,建议先对kruskal算法有一定了解再来观看~~然后就发现没必要看了~~**

<!-- more -->


## kruskal最小生成树


这个最小生成树本质就是一种贪心,每一次取边权最小的边,如果已经联通就不加,否则就让这两个连通块联通,用并查集维护.


但是值得注意的是,因为它是个贪心,所以在处理的过程中也会有很多有用的性质,例如将当前两个连通块合并的边,一定是最小生成树上使两个连通块联通的最长的边.(其实这就很接近kruskal重构树的思想了)


## kruskal重构树


~~其实kruskal重构树才是本文的重点,但是又没有多少题~~


kruskal重构树的建造就是在将原图进行kruskal的时候将合并的两个点(或连通块)之间的边新建一个点,将两个点作为它的儿子.


![](source/images/uploads/重构树.gif)


其中红边为最小生成树的边,旁边的树就是kruskal重构树,方点为原先边上的点,圆点为原图的点.


### kruskal重构树的性质


我们从上图中可以看到,kruskal重构树有以下几个性质:


1. 有$n$ 个叶子节点,每个叶子节点都是原图的点,有$n-1$ 个非叶子节点,都是原图中的边.
2. 是一个二叉树
3. 深度越小的非叶子节点,权值越大
4. 原最小生成树上的两点间最大权值就是重构树上两点间的LCA


因为性质很少,所以它用来解决什么问题也是十分清晰的.


## 例题


又是喜闻乐见的例题环节!( •̀ ω •́ )✧


### \[ABC282E] Choose Two and Eat One


[链接](https://www.luogu.com.cn/problem/AT_abc282_e)


#### 题意


有 $n$ 个数 $a_i$,你每次可以选出两个数 $a_i$ 和 $a_j$,获得$(a_i^{a_j}+a_j^{a_i})\bmod M$分,并选择这两个数中的一个数删掉，求最大得分。


$1\leq n \leq 500$


#### 思路


每次删一个点，每个点最多只能被删一次，如果我们将每一次删一个点中被删的点看作儿子，那么我们会发现最终的答案是一个树形结构．而儿子与父亲节点的连边为$(a_i^{a_j}+a_j^{a_i})\bmod M$因此我们想到两两连边，然后跑最大生成树即可．


#### code

```cpp
#include <bits/stdc++.h>
#define _F(x,y,z) for(int x=y;x<=z;x++)
#define F_(x,z,y) for(int x=z;x>=y;x--)
#define TF(x,y,z) for(int x=head[y],z;x;x=nex[x])


using namespace std;


typedef long long ll;
typedef double dou;
typedef const int ci;
typedef pair<int,int> pii;


ci maxn=2e6+10;


struct ss 
{
	ll v,x,y;
}e[maxn];
ll n,m,fa[maxn],ans,cnt,a[maxn];
int find(int x)
{
	if(fa[x]==x)
		return x;
	return fa[x]=find(fa[x]);
}
void kruskal()
{
	int tot=0;
	_F(i,1,cnt)
	{
		ll x=e[i].x,y=e[i].y,v=e[i].v;
		int fx=find(x),fy=find(y);
		if(fx!=fy)
		{
			fa[fy]=fx;
			ans+=v;tot++;
		}
		if(tot==n-1)
			return ;
	}
}
ll ksm(ll a,ll b,ll p)
{
	ll ans=1ll;
	while(b)
	{
		if(b&1)
			ans=(ans*a)%p;
		a=a*a%p;
		b>>=1;
	}
	return ans;
}
int main()
{
	scanf("%lld%lld",&n,&m);
	_F(i,1,n)
		scanf("%lld",&a[i]),fa[i]=i;
	_F(i,1,n)
		_F(j,i+1,n)
			e[++cnt]={(ksm(a[i],a[j],m)+ksm(a[j],a[i],m))%m,i,j};
	sort(e+1,e+1+cnt,[](ss a,ss b){
		return a.v>b.v;
	});
	kruskal();
	printf("%lld",ans);
	return 0;
}
```



### CF1857G Counting Graphs


[link](https://www.luogu.com.cn/problem/CF1857G)


#### 题意


给定一个有$n$个顶点的树，每个边有一个权值$w_i$。


计算同时满足以下所有条件的带权图的数量：


* 该图无重边与自环。
* 所有的边的权值不大于给定的数 S 且为正整数。
* 该图有唯一的最小生成树。
* 该图的最小生成树是给定的树。


打印其模 $998244353$的值。


我们说两个图是不同的，当且仅当其边集不同。


$t$ 组数据，$1≤t≤10000,2≤∑n≤2×10^5,1≤S≤10^9,1≤w_i≤S$。


#### 思路


考虑每一条边的贡献,不难发现,对于一条边,它若是要想不被选进最小生成树中,那么它必须要**严格大于**它所连接的两点间的路径上的最大值.


但是我们会发现,如果我们直接枚举两个端点进行统计的话很显然是超时的,考虑将贡献拆开,我们可以按边考虑.模拟kruskal的过程,当它链接两个连通块的时候这一条边是左右两个连通块上的点的路径上的最大值,答案就呼之欲出了,因此我们只需要在并查集合并的时候维护一下连通块大小即可.


::: success


#### extend


按边考虑贡献**十分常见**


:::


#### code


```cpp
#include <bits/stdc++.h>
#define _F(x,y,z) for(int x=y;x<=z;x++)
#define F_(x,z,y) for(int x=z;x>=y;x--)
#define TF(x,y,z) for(int x=head[y],z;x;x=nex[x])


using namespace std;


typedef long long ll;
typedef double dou;
typedef const int ci;
typedef pair<int,int> pii;


ci maxn=2e6+10,p=998244353;


struct ss 
{
	ll f,t,val;
}e[maxn];
ll n,t,ans,s,fa[maxn],siz[maxn];
int find(int x)
{
	if(fa[x]==x)
		return x;
	return fa[x]=find(fa[x]);
}
ll ksm(ll x,ll y)
{
	ll res=1;
	while(y)
	{
		if(y&1) res=res*x%p;
		x=x*x%p;
		y>>=1;
	}
	return res;
}
int main()
{
	scanf("%lld",&t);
	while(t--)	
	{
		ans=1;
		scanf("%lld%lld",&n,&s);
		_F(i,1,n)
			fa[i]=i,siz[i]=1;
		_F(i,1,n-1)
		{
			ll x,y,z;
			scanf("%lld%lld%lld",&x,&y,&z);
			e[i]={x,y,z};
		}
		sort(e+1,e+n,[](ss a,ss b){
			return a.val<b.val;
		});
		_F(i,1,n-1)
		{
			int x=e[i].f,y=e[i].t,z=e[i].val;
			int fx=find(x),fy=find(y);
// 			printf("%d %d\n",fx,fy);
			fa[fx]=fy;
			ans=(ans*ksm(s-z+1,siz[fx]*siz[fy]-1))%p;
			siz[fy]+=siz[fx];
		}
		printf("%lld\n",ans);
	}
	return 0;
}
```

### CF609E Minimum spanning tree for each edge


[link](https://www.luogu.com.cn/problem/CF609E)


#### 题意


给你 $n$ 个点，$m$ 条边，如果对于一个最小生成树中要求必须包括第 $i(1≤i≤m)$ 条边，那么最小生成树的权值总和最小是多少。


$1≤n≤2×10^5,n−1≤m≤2×10^5$


$1≤u_i,v_i≤n,u_i≠v_i,1≤w_i≤10^9$


#### 思路


很简单,先求出最小生成树,然后会发现,在树上的边不变,对于每一个不在树上的边用树链剖分,求出它所连接的两点上路径的最大值,将它换成这条边即可.


#### code


这道题我用LCT过的,代码就不给了,我相信你们一定能做出来的( ‵▽′)ψ


### 遺産相続


[link](https://www.luogu.com.cn/problem/AT_joisc2015_k)


#### 题意


给定一个$N$个点、$M$条边的无向图，每条边有权值，任意两条边的权值不同。图上保证没有自环，但是可能有重边。每条边有一个标记值，初始每条边的标记值均为$0$。


接下来对这个图进行$K$次操作，每一次操作取走一个最大生成森林(图可能不连通),问每一条边什么时候被取走


$1≤N≤1000$


$1≤M≤300000$


$1≤K≤10000$


$1≤A_i,B_i≤N,A_i≠B_i$


$1≤C_i≤10^9,∀i≠j,C_i≠C_j$


#### 思路


~~论并查集的神秘用法~~


很显然,每一次暴力删是过不了的,会被卡.我们会有一个神奇的做法,维护$K$个并查集,对于一条边,如果我们在kruskal的过程中已经将这个边所连的连通块合并了,那么我们就见这条边插入到第一个没有被合并的并查集中,每一条边的答案就是它所在的并查集.


显然,上面查找并查集的操作是满足可二分性的,因此我们可以用二分找到第一个没有被合并的并查集.


#### code


如果没有看懂的话就可以看代码:



```cpp
#include <bits/stdc++.h>
#define _F(x,y,z) for(int x=y;x<=z;x++)
#define F_(x,z,y) for(int x=z;x>=y;x--)
#define TF(x,y,z) for(int x=head[y],z;x;x=nex[x])


using namespace std;


typedef long long ll;
typedef double dou;
typedef const int ci;
typedef pair<int,int> pii;


ci maxn=1e6+10;


struct ss 
{
	int f,t,v,id;
}e[maxn];
int n,m,k,fa[10010][1010],ans[maxn];
int find(int x,int t)
{
	if(fa[t][x]==x)
		return x;
	return fa[t][x]=find(fa[t][x],t);
}
int get(int x,int y)
{
	int l=1,r=k+1,res=0;
	while(l<=r)
	{
		int mid=(l+r)>>1;
		if(find(x,mid)!=find(y,mid))
			r=mid-1,res=mid;
		else
			l=mid+1;
	}
	return res;
}
int main()
{
	scanf("%d%d%d",&n,&m,&k);
	_F(i,1,k)
		_F(j,1,n)
			fa[i][j]=j;
	_F(i,1,m)
	{
		int x,y,z;
		scanf("%d%d%d",&x,&y,&z);
		e[i]={x,y,z,i};
	}
	sort(e+1,e+1+m,[](ss a,ss b){
		return a.v>b.v;
	});
	_F(i,1,m)
	{
		int x=e[i].f,y=e[i].t,t=get(x,y);
		int fx=find(x,t),fy=find(y,t);
		if(fx!=fy)
			fa[t][fx]=fa[t][fy];
		ans[e[i].id]=t;
	}
	_F(i,1,m)
	{
		if(ans[i]>k)
			puts("0");
		else 
			printf("%d\n",ans[i]);
	}
	return 0;
}
```

### P4768 \[NOI2018] 归程


[link](https://www.luogu.com.cn/problem/P4768)


#### 题意


魔力之都可以抽象成一个 $n$ 个节点、$m$ 条边的无向连通图（节点的编号从 $1$ 至 $n$）。我们依次用 $l,a$ 描述一条边的**长度、海拔**。


作为季风气候的代表城市，魔力之都时常有雨水相伴，因此道路积水总是不可避免的。由于整个城市的排水系统连通，因此**有积水的边一定是海拔相对最低的一些边**。我们用**水位线**来描述降雨的程度，它的意义是：所有海拔**不超过**水位线的边都是**有积水**的。


Yazid 是一名来自魔力之都的 OIer，刚参加完 ION2018 的他将踏上归程，回到他温暖的家。Yazid 的家恰好在魔力之都的 $1$ 号节点。对于接下来 $Q$ 天，每一天 Yazid 都会告诉你他的出发点 $v$ ，以及当天的水位线 $p$。


每一天，Yazid 在出发点都拥有一辆车。这辆车由于一些故障不能经过有积水的边。Yazid 可以在任意节点下车，这样接下来他就可以步行经过有积水的边。但车会被留在他下车的节点并不会再被使用。 需要特殊说明的是，第二天车会被重置，这意味着：


* 车会在新的出发点被准备好。
* Yazid 不能利用之前在某处停放的车。


Yazid 非常讨厌在雨天步行，因此他希望在完成回家这一目标的同时，最小化他**步行经过的边**的总长度。请你帮助 Yazid 进行计算。


强制在线.


#### 思路


噔噔咚.~~臭名昭著~~


不过不难,因为每一条路的长度是不会变的,因此我们可以先考虑给每一个点计算出到$1$点的的最短路(一定要用dij,~~别怪我没警告过你~~).


后面的好像也就挺自然了,考虑对海拔做kruskal重构树(降序),那么深度越浅,海拔越低,我们考虑预处理出重构树上每一个代表边的点的权值为它的子树中的点到$1$ 号点的最短距离.那么对于每一次询问,倍增的向上跳到最靠上的海拔大于询问的点,那个点的权值就是答案.


#### code

```cpp
#include <bits/stdc++.h>
#define _F(x,y,z) for(int x=y;x<=z;x++)
#define F_(x,z,y) for(int x=z;x>=y;x--)
#define TF(x,y,z) for(int x=head[y],z;x;x=nex[x])
#define TF1(x,y,z) for(int x=head1[y],z;x;x=nex1[x])


using namespace std;


typedef long long ll;
typedef double dou;
typedef const int ci;
typedef pair<ll,ll> pll;


ci maxn=2e6+10;


ll n,m,to1[maxn],nex1[maxn],head1[maxn],val1[maxn],tot1;
ll to[maxn<<1],nex[maxn<<1],head[maxn],val[maxn],tot,ff[maxn];
ll fa[20][maxn],f[maxn],vis[maxn],t,q,k,s,lans,dep[maxn];
ll mx[maxn];
struct ss 
{
	ll f,t,v,h;
}e[maxn];
priority_queue<pll,vector<pll>,greater<pll> >pq;
void add1(int x,int y,int v)
{
	to1[++tot1]=y;
	nex1[tot1]=head1[x];
	head1[x]=tot1;
	val1[tot1]=v;
}
void dij()
{
	f[1]=0;
	pq.push({0,1});
	while(!pq.empty())
	{
		ll x=pq.top().second;pq.pop();
		if(vis[x]) continue;
		vis[x]=1;
		for(int i=head1[x],y;i;i=nex1[i])
		{
			y=to1[i];
			if(vis[y]) continue;
			if(f[y]>f[x]+val1[i])
			{
				f[y]=f[x]+val1[i];
				pq.push({f[y],y});
			}
		}
	}
	_F(i,1,n)
		mx[i]=f[i];
//	_F(i,1,n)
//	{
//		printf("%lld ",f[i]);
//	}
//	puts("");
}
void add(int x,int y)
{
	to[++tot]=y;
	nex[tot]=head[x];
	head[x]=tot;
}
int find(int x)
{
	if(ff[x]==x)
		return x;
	return ff[x]=find(ff[x]);
}
void dfs(int x,int f)
{
	fa[0][x]=f;dep[x]=dep[f]+1;
	_F(i,1,19)
		fa[i][x]=fa[i-1][fa[i-1][x]];
//	printf("%d ",x);
	for(int i=head[x],y;i;i=nex[i])
	{
		y=to[i];
		if(y!=f)
		{
			dfs(y,x);
			mx[x]=min(mx[x],mx[y]);
		}
	}
}
ll ask(int x,int y)
{
	F_(i,19,0)
		if(dep[x]-(1<<i)>0&&val[fa[i][x]]>y) x=fa[i][x];
	return mx[x];
}
void kru()
{
	_F(i,1,(n<<1))
		ff[i]=i,val[i]=0;
	sort(e+1,e+1+m,[](ss a,ss b){
		return a.h>b.h;
	});
	ll res=0,cnt=n;
	_F(i,1,m)
	{
		ll x=e[i].f,y=e[i].t;
		ll fx=find(x),fy=find(y);
		if(fx!=fy)
		{
			add(++cnt,fx),add(cnt,fy);
			add(fx,cnt),add(fy,cnt);
//			printf("%d %d\n%d %d\n",fx,cnt,fy,cnt);
			ff[fy]=ff[fx]=cnt;	
			val[cnt]=e[i].h;
			res++;
		}
		if(res>=n-1) break;
	}
	dfs(cnt,0);
//	_F(i,1,cnt)	
//	{
//		printf("%lld %lld\n",mx[i],val[i]);
//	}
	scanf("%lld%lld%lld",&q,&k,&s);
	while(q--)
	{
		ll x,y;
		scanf("%lld%lld",&x,&y);
//		_F(i,0,19)
//			printf("%lld ",fa[i][x]);
		x=(x+k*lans-1)%(n)+1;
		y=(y+k*lans)%(s+1);
		lans=ask(x,y);
		printf("%lld\n",lans);
	}
}
int main()
{
	scanf("%lld",&t);
	while(t--)
	{
		lans=tot1=tot=0;
		memset(head,0,sizeof head);
		memset(head1,0,sizeof head1);
		memset(e,0,sizeof e);
		memset(fa,0,sizeof fa);
		memset(vis,0,sizeof vis);
		memset(f,0x3f,sizeof f);
		memset(mx,0x3f,sizeof mx);
		scanf("%lld%lld",&n,&m);
		_F(i,1,m)
		{
			ll x,y,l,a;
			scanf("%lld%lld%lld%lld",&x,&y,&l,&a);
			add1(x,y,l),add1(y,x,l);
			e[i]={x,y,l,a};
		}
		dij();
		kru();
	}
	return 0;
}
```



### CF888G Xor-MST


[link](https://www.luogu.com.cn/problem/CF888G)


#### 题意


给定 $n$ 个结点的无向完全图。每个点有一个点权为 $a_i$。连接 $i$ 号结点和 $j$ 号结点的边的边权为 $a_i\oplus a_j$。


求这个图的 MST 的权值。


$1≤n≤2×10^5，0≤ai<2^{30}$


#### 思路


首先看见异或,可以考虑trie树,我们可以考虑将所有的数插入01trie中,然后进行dfs,类似kruskal,我们可以贪心的发现,两个数字在01trie上分开的深度越深,异或值最小,所以遇见同时有左右儿子的节点,我们可以考虑他的左右儿子,我们向下搜索使它异或值最小的数字(尽可能同向)即可.


#### code



```cpp
#include <bits/stdc++.h>
#define _F(x,y,z) for(int x=y;x<=z;x++)
#define F_(x,z,y) for(int x=z;x>=y;x--)
#define TF(x,y,z) for(int x=head[y],z;x;x=nex[x])


using namespace std;


typedef long long ll;
typedef double dou;
typedef const int ci;
typedef pair<int,int> pii;


ci maxn=5e6+10;


struct tire 
{
	int nex[3],dep;
}nd[maxn];
int n,a[maxn],cnt;
ll ans=0,num;
void insert(int x)
{
	int now=0;
	nd[0].dep=31;
	F_(i,30,0)
	{
		int t=nd[now].dep;
		if(!nd[now].nex[(x>>i)&1])
			nd[now].nex[((x>>i)&1)]=++cnt;
//		printf("%d %d %d %d\n",now,nd[now].nex[0],nd[now].nex[1],nd[now].dep);
		now=nd[now].nex[((x>>i)&1)];
		nd[now].dep=t-1;
	}
}
void get(int x,int y,ll res)
{
	if(res>num)
		return ;
	if(!nd[x].dep)
	{
		num=min(num,res);
		return ;
	}
	_F(i,0,1)
	{
		if(nd[x].nex[i]&&nd[y].nex[i])
			get(nd[x].nex[i],nd[y].nex[i],res);
		else if(nd[x].nex[i^1]&&nd[y].nex[i])
			get(nd[x].nex[i^1],nd[y].nex[i],res+(1<<nd[x].dep));
	}
}
void dfs(int x)
{
//	printf("%d %d %d %d\n",x,nd[x].nex[0],nd[x].nex[1],nd[x].dep);
	if(!nd[x].dep)
		return ;
	if(!nd[x].nex[0])
	{
		dfs(nd[x].nex[1]);
		return ;
	}
	if(!nd[x].nex[1])
	{
		dfs(nd[x].nex[0]);
		return ;
	}
	dfs(nd[x].nex[0]);
	dfs(nd[x].nex[1]);
	num=INT_MAX;
	get(nd[x].nex[0],nd[x].nex[1],0);
	ans+=num;
	ans+=(1<<nd[x].dep);
}
int main()
{
	scanf("%d",&n);
	_F(i,1,n)
		scanf("%d",&a[i]),insert(a[i]);
	dfs(0);
	printf("%lld",ans/2);
	return 0;
}
```



### CF1706E Qpwoeirut and Vertices


[link](https://www.luogu.com.cn/problem/CF1706E)


#### 题意


给出 $n$ 个点， $m$ 条边的不带权连通无向图， $q$ 次询问至少要加完编号前多少的边，才能使得 $[l,r]$ 中的所有点两两连通。


#### 思路


~~感觉这是我写过最恶心的一道蓝题了~~


考虑以边的编号为权值进行kruskal重构树,那么相邻两个点之间的LCA就是连通这两个点的最小编号.所以我们只需要预处理出相邻两个点之间的LCA,让后对于一个区间,直接使用数据结构去维护区间最大值即可.


#### code


思路不难,代码恶心.

```cpp#include <bits/stdc++.h>
#define _F(x,y,z) for(int x=y;x<=z;x++)
#define F_(x,z,y) for(int x=z;x>=y;x--)
#define TF(x,y,z) for(int x=head[y],z;x;x=nex[x])

using namespace std;

typedef long long ll;
typedef double dou;
typedef const int ci;
typedef pair<int,int> pii;

ci maxn=4e5+10;

int n,m,q,t,rt,tot,head[maxn],to[maxn],nex[maxn],lg[maxn],fa[maxn],f[25][maxn],st[25][maxn>>1];
int dep[maxn];
void add(int x,int y)
{
	to[++tot]=y;
	nex[tot]=head[x];
	head[x]=tot;
}
int find(int x)
{
	if(fa[x]==x)
		return x;
	return fa[x]=find(fa[x]);
}
void dfs(int x,int fa)
{
	f[0][x]=fa;dep[x]=dep[fa]+1;
	_F(i,1,20) f[i][x]=f[i-1][f[i-1][x]];
	TF(i,x,y)
	{
		y=to[i];
		if(y!=fa)
			dfs(y,x);
	}
}
int lca(int x,int y)
{
	if(dep[x]<dep[y])
		swap(x,y);
	F_(i,20,0)
		if(dep[f[i][x]]>=dep[y])
			x=f[i][x];
	if(x==y)
		return x;
	F_(i,20,0)
		if(f[i][x]!=f[i][y])
			x=f[i][x],y=f[i][y];
	return f[0][x];
}
int main()
{
	scanf("%d",&t);
	lg[0]=-1;
	_F(i,1,114514)
		lg[i]=lg[i>>1]+1;
	while(t--)
	{
		tot=0;
		memset(head,0,sizeof head);
		scanf("%d%d%d",&n,&m,&q);
		_F(i,1,n+m)
			fa[i]=i;
		_F(i,1,m)
		{
			int x,y;
			scanf("%d%d",&x,&y);
			int fx=find(x),fy=find(y);
			if(fx==fy)continue;
			add(fx,n+i),add(i+n,fx);
			add(fy,n+i),add(i+n,fy);
			fa[fx]=i+n,fa[fy]=i+n;rt=i+n;
		}
		dfs(rt,0);
//		printf("%d",rt);
		_F(i,1,n-1)
			st[0][i]=lca(i,i+1);
		for(int i=1;(1<<i)<=n-1;i++)
		{
			for(int j=1;j+(1<<i)-1<=n-1;j++)
				st[i][j]=max(st[i-1][j],st[i-1][j+(1<<(i-1))]);
//			puts("");
		}
		_F(i,1,q)
		{
			int l,r;
			scanf("%d%d",&l,&r);
			if(l==r)
			{
				printf("0 ");
				continue;
			}
			r--;
			int len=lg[r-l+1];
			printf("%d ",max(st[len][l],st[len][r-(1<<len)+1])-n);
		}
		puts("");
	}
	return 0;
}
```



### Flip Digits 2


[link](https://atcoder.jp/contests/typical90/tasks/typical90_aw)


#### 题意


您有一个长度为 $N$ 的01序列 $S$ 。最初， $S$ 中的所有字符都是 "0"。


商店出售 $M$ 件商品，每件商品的编号从 $1$ 到 $M$ 。商品 $i$ 的价格为 $C_i$ ，这可以使 $S$ 中的 $L_i$ 至 $R_i$ 数字取反。

你想从 $M$ 中选择一些物品购买，以满足可以通过你买的物品构造出长度为$N$所有01串.


求满足条件所需买的物品的最低总价。


但是，如果他即使买下所有物品也无法满足条件，请输出$-1$。


#### 思路


神奇题目,老师不讲我肯定做不出来.


看见区间操作,考虑差分,每一个操作相当于将$L_i$和$R_i+1$的位置异或一,考虑倒序操作,就是给你一个01串,最后就是要让差分数组全成零.类似差分约束,我们考虑$R_i+1$向$L_i$连边,如果图联通,那么有解,跑最小生成树即可.


#### code



```cpp
#include <bits/stdc++.h>
#define _F(x,y,z) for(int x=y;x<=z;x++)
#define F_(x,z,y) for(int x=z;x>=y;x--)
#define TF(x,y,z) for(int x=head[y],z;x;x=nex[x])


using namespace std;


typedef long long ll;
typedef double dou;
typedef const int ci;
typedef pair<int,int> pii;


ci maxn=2e6+10;


struct ss 
{
	int x,y,val;
}e[maxn];
int n,m,cnt,fa[maxn];
int find(int x)
{
	if(fa[x]==x)
		return x;
	return fa[x]=find(fa[x]);
}
ll kruskal()
{
	ll ans=0;
	_F(i,1,m)
	{
		auto [x,y,v]=e[i];
		int fx=find(x),fy=find(y);
		if(fx!=fy)
		{
			cnt++;
			ans+=v;
			fa[fx]=fy;
		}
		if(cnt==n)
			return ans;
	}
	return -1;
}
int main()
{
	scanf("%d%d",&n,&m);
	_F(i,1,n+1)
		fa[i]=i;
	_F(i,1,m)
	{
		int l,r,v;
		scanf("%d%d%d",&v,&l,&r);
		e[i]={l,r+1,v};
	}
	sort(e+1,e+1+m,[](ss a,ss b){
		return a.val<b.val;
	});
	printf("%lld",kruskal());
	return 0;
}
```



### CF1120D Power Tree


[link](https://www.luogu.com.cn/problem/CF1120D)


#### 题意


给定一棵 $n$ 个点的有根树，$1$ 为根。定义 $u$ 为叶子当且仅当它**不是根**且度数为 $1$。


你可以选择花费 $w_i$ 的代价控制点 $i$。当一个点 $i$ 被控制时你可以选择给它的子树内的叶子的点权都加上一个自己选定的值 $v_i$ 。你需要控制若干个点，使得花费的代价尽量少，且无论怎样规定所有叶子的初始点权，都可以通过调整你选择的点的值 $v_i$ 来让所有叶子的点权变为 $0$。


你需要输出最小代价和并构造**所有**最优选取方案的**并集**。


$n≤2×10^5$

#### 思路


大致与上题类似,只不过需要先用dfs序将树上问题,转为区间问题,然后就跟上题一样了.

::: success 

#### extend

树上问题可以与序列问题相互转换,将树上问题转成序列问题用dfs序(或欧拉序),将序列问题转为树上问题使用笛卡尔树.

:::


#### code

```cpp
#include <bits/stdc++.h>
#define _F(x,y,z) for(int x=y;x<=z;x++)
#define F_(x,z,y) for(int x=z;x>=y;x--)
#define TF(x,y,z) for(int x=head[y],z;x;x=nex[x])


using namespace std;


typedef long long ll;
typedef double dou;
typedef const int ci;
typedef pair<int,int> pii;


ci maxn=2e6+10;


struct ss 
{
	ll l,r,w,id;
}e[maxn];
ll n,head[maxn],to[maxn],nex[maxn],tot,val[maxn],v[maxn],dfnl[maxn],dfnr[maxn],siz[maxn],cnt;
ll m,fa[maxn],ans,s;
void add(int x,int y)
{
	to[++tot]=y;
	nex[tot]=head[x];
	head[x]=tot;
}
int find(int x)
{
	if(fa[x]==x)
		return x;
	return fa[x]=find(fa[x]);
}
void dfs(int x,int f)
{
	dfnl[x]=1145141919810;
	int fl=0;
	TF(i,x,y)
	{
		y=to[i];
		if(y!=f)
		{
			dfs(y,x);
			dfnl[x]=min(dfnl[y],dfnl[x]);
			dfnr[x]=max(dfnr[x],dfnr[y]);
			fl=1;
		}
	}
	if(!fl)
		dfnl[x]=dfnr[x]=++cnt;
	e[++m]={dfnl[x],dfnr[x]+1,val[x],x};
}
void kruskal()
{
	sort(e+1,e+1+m,[](ss x,ss y){
		return x.w<y.w;
	});
//	_F(i,1,m)
//		printf("%d %d %d %d\n",e[i].l,e[i].r,e[i].w,e[i].id);
	int l=1,r=1;
	while(l<=n)
	{
		r=l;
		while(r+1<=n&&e[r].w==e[r+1].w) r++;
		_F(i,l,r)
		{
			int x=e[i].l,y=e[i].r;
			if(find(x)!=find(y))
				v[e[i].id]=1,s++;
		}
		_F(i,l,r)
		{
			int x=e[i].l,y=e[i].r;
			int fx=find(x),fy=find(y);
			if(fx!=fy)
			{
				fa[fx]=fy;
				ans+=e[i].w;
			}
		}
		l=r+1;
	}
}
int main()
{
	scanf("%lld",&n);
	_F(i,1,n)
		scanf("%lld",&val[i]);
	_F(i,1,n-1)
	{
		int x,y;
		scanf("%d%d",&x,&y);
		add(x,y),add(y,x);
	}
	dfs(1,0);
	_F(i,1,n+1)
		fa[i]=i;
	kruskal();
	printf("%lld %lld\n",ans,s);
	_F(i,1,n)
		if(v[i])
			printf("%d ",i);
	return 0;
}
```
