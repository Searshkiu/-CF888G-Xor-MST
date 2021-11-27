---
[[CF888G]Xor-MST](https://codeforces.com/problemset/problem/888/G) 解题报告
#### 目录

#### 题意
- [洛谷传送门](https://www.luogu.com.cn/problem/CF888G)

给定有$n$个节点的无向完全图，每个点有点权$a_i$, 节点$i$与节点$j$之间的边权为 $ a_i \oplus a_j $. 计算该图最小生成树的权值.


#### 思路  
处理异或：Trie  
最小生成树：$Kruskal, Prim, Bor\dot{u}vka(Sollin)$

已知：  
我们会求n个数中任取两数的最大、最小异或和，由此算是处理了$\oplus$有关的事；  
对三种我们已知的MST算法进行选择：
- Kruskal  
遍历每一条边，合并两棵树
- Prim
- Boruvka

#### 代码实现
一点叭叭：花了两天晚上。第一天晚上特意找最小生成树的模板题打了Boruvka，第二天晚上花了两个半小时看题解和写代码和看题解。实际耗时远不及此。

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

#### 参考资料

$\color{Teal}{BONA (●◡●)}$
