---
[[CF888G]Xor-MST](https://codeforces.com/problemset/problem/888/G) 解题报告
### 目录

### 题意
- [洛谷传送门](https://www.luogu.com.cn/problem/CF888G)

给定有$n$个节点的无向完全图，每个点有点权$a_i$, 节点$i$与节点$j$之间的边权为 $ a_i \oplus a_j $. 计算该图最小生成树的权值.


### 思路  
处理异或：Trie  
最小生成树：$Kruskal, Prim, Borůvka(Sollin)$

已知：  
我们会求n个数中任取两数的最大、最小异或和，由此算是处理了$\oplus$有关的事；  
对三种我们已知的MST算法进行选择：
- Kruskal  
贪心，从小到大将**边加入**当前选出的、用于构成MST的边的**集合**。需要维护若干集合，会用到*并查集*。
- Prim  
从**点**出发，选择与当前节点距离最小的（并且不在集合里的）点**加入集合**，并连上那条边。
- Borůvka (Sollin)  
遍历没用过的边，寻找每个**连通块**的最小出边，将这条边两个端点所在的两个连通块**合并**。也需要用到*并查集*。通俗言之，相当于将连通块当成点来跑Kruskal，或者说，用“点”带“连通块”的形式跑Prim。 由于只需要$ \logN $次合并，在处理边权与点权有关/一些（？？？）的问题时会有优势。  
此题就是用Borůvka的典例。  
LG [@Tweetuzki](https://www.luogu.com.cn/blog/Tweetuzki/)dalao的[这篇题解](https://www.luogu.com.cn/blog/Tweetuzki/solution-p3366)写得很清楚，在此感谢这位神犇。  

整理一下，我们找到了思路：Borůvka + 01 Trie！o(*￣▽￣*)ブ

但是在代码实现中，一个~~小~~问题出现了。

### 代码实现
怎么找一个连通块的最小出边呢？
由常识可知，当两个数在01 Trie里的LCA最深时，这两个数的异或值最小。
> 证明：$2^a>2^{a-1}+2^{a-2}+\cdots+2^1+2^0$，显然左式=右式+1.  

所以权值在01 Trie上LCA最深的两个点必然先连通。因此，采用分治思想，字典树上当前节点的0儿子和1儿子联通的代价为 在0儿子里的点的MST权值+1儿子里的点的MST权值+0块和1块之间最小边的边权。最小边的边权即为在0儿子和1儿子里分别找一个点，将这两个点的权异或得到的最小值。  

***AC Code***
```
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int N=2e5+5;
const ll INF=2147483647;
vector<int> a;
int trie[N<<5][2],cnt;
int n;

void ins(int x){
	int u=0;
	for(int i=31;i>=0;i--){
		if(!trie[u][x>>i &1]) trie[u][x>>i &1]=++cnt;
		u=trie[u][x>>i &1];
	}
} 

ll xr(int t,int f,int r){
	if(t==-1) return 0;
	int u=r;
	ll ret=0;
	for(int i=t;i>=0;i--){
		if(trie[u][f>>i &1]){
			u=trie[u][f>>i &1];
		} else {
			u=trie[u][!(f>>i &1)];
			ret+=1<<i;
		}
	}
	return ret;

}

ll b(const vector<int> &now,int t,int r){//boruvka,t为当前递归到的层数
	if(t<0 || !r) return 0;
	int l=now.size();
	vector<int>ch[2];// ch向量用于存当前分治树的0儿子和1儿子下的点的权值
	for(int i=0;i<l;i++){
		ch[now[i]>>t &1].push_back(now[i]);
	}
	
	if(!ch[0].size()) return b(ch[1],t-1,trie[r][1]);
	if(!ch[1].size()) return b(ch[0],t-1,trie[r][0]);
    
	ll minn=INF;
	int siz=ch[0].size();
	for(int i=0;i<siz;i++){
		minn=min(minn,(1<<t)*1LL+xr(t-1,ch[0][i],trie[r][1]));
        //取1儿子里的点与0儿子里的点分别异或，取最小值作为当前两个连通块联通的代价
	}
	return minn+b(ch[0],t-1,trie[r][0])+b(ch[1],t-1,trie[r][1]);
}

int main(){
	ins(0);
	scanf("%d",&n);
	for(int i=0,j;i<n;i++){
		scanf("%d",&j);
		a.push_back(j);
		ins(j);
	}
	printf("%lld",b(a,30,1));
	return 0;
} 
```
一点叭叭：花了两天晚上。第一天晚上特意找最小生成树的模板题打了Borůvka，第二天晚上花了两个半小时看题解和写代码和看题解。实际耗时远不及此。

#### 参考资料

$\color{Teal}{BONA (●◡●)}$
