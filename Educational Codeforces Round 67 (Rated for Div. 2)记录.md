[Educational Codeforces Round 67 (Rated for Div. 2)](https://codeforces.com/contest/1187)
![](https://cdn.luogu.com.cn/upload/image_hosting/1ii8odz8.png)
## [C](https://codeforces.com/contest/1187/problem/C)
[洛谷](https://www.luogu.com.cn/problem/CF1187C)
```c++
#include<iostream>
#include<cstring>
#include<vector>
#include<cmath>
using namespace std;
int n,m;
int dif[1005];
int lf[1005],rt[1005],cnt;
int main()
{
	cin>>n>>m;
	for(int i=1,t,l,r;i<=m;i++)
	{
		cin>>t>>l>>r;
		if(t==1)
			for(int j=l+1;j<=r;j++) dif[j]=1;
		else
			lf[++cnt]=l,rt[cnt]=r;
	}
	for(int i=1;i<=cnt;i++)
	{
		bool flag=true;
		for(int j=lf[i]+1;j<=rt[i];j++) 
		{
			if(dif[j]!=1)
			{
				flag=false;
				dif[j]=-1;
				break;
			}
		}
		if(flag)
		{
			cout<<"NO";
			return 0;
		}
	}
	cout<<"YES\n";
	for(int i=1,a=1;i<=n;i++)
	{
		a+=dif[i];
		cout<<n+10+a<<" ";
	}
	return 0;
}
```
我们可以考虑这个数组的差分数组，若不 $[l,r]$ 不降则差分数组 $dif[i]\ge 0,i \in (l,r]$ ，若不是一个不降序列，则存在一个 $i \in (l,r]$ 使得 $dif[i]<0$ 。所以可以先将差分数组全置为0，然后将所有不降区间的差分数组置为1，再考虑每一个不是一个不降序列的区间，从中找到一个位置使得其差分数组小于等于0，置为-1，若不存在，则输出 `NO` 。最后给差分数组求前缀和（注意要加上 $n+1$ 防止原数组小于0） 算出原数组即可。

## [D](https://codeforces.com/contest/1187/problem/D)
[洛谷](https://www.luogu.com.cn/problem/CF1187D)
```c++
#include<iostream>
#include<cstring>
#include<vector>
#include<cmath>
#include<algorithm>
using namespace std;
int T,n;
int a[300005];
int b[300005];
int pos[300005];
int cnt[300005];
int cnt1[300005];
int t[300005];
int minn[1200005];
void add(int p,int l,int r,int x,int w)
{
	if(l==r)
	{
		minn[p]=w;
		return ;
	}
	int mid=l+r>>1;
	if(x<=mid) add(p*2,l,mid,x,w);
	else add(p*2+1,mid+1,r,x,w);
	minn[p]=min(minn[2*p],minn[2*p+1]);
}
int query(int p,int l,int r,int x,int y)
{
	if(x<=l&&r<=y) return minn[p];
	int mid=l+r>>1,res=0x3f3f3f3f;
	// cout<<l<<" "<<r<<" "<<minn[p]<<".\n";
	if(x<=mid) res=min(res,query(2*p,l,mid,x,y));
	if(y>mid) res=min(res,query(2*p+1,mid+1,r,x,y));
	return res;
}
int main()
{
	ios::sync_with_stdio(0); cin.tie(0);
	cin>>T;
	while(T--)
	{
		cin>>n;
		for(int i=1;i<=n;i++)
			cnt[i]=cnt1[i]=0;
		for(int i=1;i<=n;i++)
			cin>>a[i],t[i]=a[i];
		for(int i=1;i<=n;i++)
			cin>>b[i];
		sort(t+1,t+1+n);
		for(int i=1;i<=n;i++)
		{
			a[i]=lower_bound(t+1,t+1+n,a[i])-t+(cnt[a[i]]++);
			add(1,1,n,i,a[i]);
			pos[a[i]]=i;
			b[i]=lower_bound(t+1,t+1+n,b[i])-t+(cnt1[b[i]]++);
		}
		bool flag=true;
		for(int i=1;i<=n;i++) 
		{
			if(cnt[i]!=cnt1[i]) 
			{
				cout<<"NO\n";
				flag=false;
				break;
			}
		}
		if(!flag) continue;
		for(int i=1;i<=n;i++)
		{
			// cout<<i<<" "<<pos[b[i]]<<"\n";
			if(b[i]!=query(1,1,n,1,pos[b[i]]))
			{
				cout<<"NO\n";
				flag=false;
				break;
			}
			add(1,1,n,pos[b[i]],0x3f3f3f3f);
		}
		if(!flag) continue;
		cout<<"YES\n";
	}
	return 0;
}
```

~~看了题解才做出来的~~

1. 对于一个区间排序和对于相邻两数排序是等价的（同冒泡排序的正确性证明）。
2. 对于再b中第一个数能在a中一道第一个位置的充要条件是在a中它前面的数都比它小。
3. 当第一个数排好后，剩下数相对位置没有改变，相当于把第一个数删掉再求第二个数。同理，就可以求出后面所有数是否可行。

所以可以用线段树维护区间最小值，每次查询从1（不是i，前面的数也向后移了）到pos[b[i]]的最小值是否是a[i]，若是，则将这个位置的值置为无穷大，若不是，则输出 ```NO``` 。用线段树优化，复杂度 $O(nlogn)$

## [E](https://codeforces.com/contest/1187/problem/E)
[洛谷](https://www.luogu.com.cn/problem/CF1187E)
```c++
#include<iostream>
#include<cstring>
#include<vector>
#include<cmath>
#include<algorithm>
using namespace std;
int n;
vector<int> g[200005];
long long f[200005];
long long siz[200005];
long long ans;
void dfs(int u,int fa)
{
	siz[u]=1;
	for(int v:g[u])
	{
		if(v==fa) continue;
		dfs(v,u);
		siz[u]+=siz[v];
	}
	f[1]+=siz[u];
}
void solve(int u,int fa)
{
	for(int v:g[u])
	{
		if(v==fa) continue;
		f[v]=f[u]-siz[v]+n-siz[v];
		//n+siz[v]+... ->
		solve(v,u);
	}
}
int main()
{
	// ios::sync_with_stdio(0); cin.tie(0);
	cin>>n;
	for(int i=1,u,v;i<n;i++)
	{
		cin>>u>>v;
		g[u].push_back(v);
		g[v].push_back(u);
	}
	dfs(1,0);
	solve(1,0);
	for(int i=1;i<=n;i++)
		ans=max(ans,f[i]);
	cout<<ans;
	return 0;
}
```
显然，答案至于选择的第一个点相关，后面的点的顺序不影响答案，若将第一次选择的点作为根，然后进行换根dp即可。

记 $f[i]$ 为第一次选点 $i$ 的答案。先用dfs求出 $f[1]$ 。若 $v$ 为 $u$ 的儿子，若以这两个点中一个为根，则其他点的贡献不变，变的是 $u$ ， $v$ 两点的贡献，所以 

$f[u]=n+siz[v]+...$  

$f[v]=n+n-siz[v]+...$

$\therefore f[v]=f[u]-siz[u]+n-siz[u]$

再进行一次dfs算出每个点的答案，求最小值即可。