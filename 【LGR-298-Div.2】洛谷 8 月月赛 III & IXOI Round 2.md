[【LGR-298-Div.2】洛谷 8 月月赛 III & IXOI Round 2](https://www.luogu.com.cn/contest/327094)


![](https://cdn.luogu.com.cn/upload/image_hosting/3y9onqq8.png)
## [A](https://www.luogu.com.cn/problem/P17281)
```c++
#include<iostream>
#include<cstring>
#include<vector>
#include<cmath>
#include<map>
#include<algorithm>
using namespace std;
long long n;
int main()
{
	ios::sync_with_stdio(0); cin.tie(0);
	cin>>n;
	cout<<n/2;
	return 0;
}
```
显然， $\forall a<\lfloor n \rfloor 即 2a<n ,a=gcd(a,2a),所以可以产生$， 
$\forall a>\lfloor n \rfloor,在1∼n中只有一个它的倍数,所以无法产生$。

最终数量即为 $\lfloor n \rfloor$ 。

## [B](https://www.luogu.com.cn/problem/P17282)
```c++
#include<iostream>
#include<cstring>
#include<vector>
#include<cmath>
#include<map>
#include<algorithm>
using namespace std;
int n;
long long ansa,ansb;
long long a[1000005];
struct node{long long x,y;} f[1000005];
long long gcd(long long a,long long b)
{
	if(b==0) return a;
	return gcd(b,a%b);
}
int main()
{
	ios::sync_with_stdio(0); cin.tie(0);
	cin>>n;
	for(int i=1;i<=n;i++)
		cin>>a[i];
	f[1]=(node){1ll*a[1],1ll};
	for(int i=2;i<=n;i++)
	{
		if(1ll*a[i]*(f[i-1].y+1)<f[i-1].x+a[i])
			f[i]=(node){a[i],1};
		else
			f[i]=(node){f[i-1].x+a[i],f[i-1].y+1};
	}
	for(int i=2;i<=n;i++)
	{
		if(f[i].x==0)
			f[i]=(node){f[i-1].x+a[i],f[i-1].y+1};
	}
	ansa=1;
	for(int i=1;i<=n;i++)
	{
		if(f[i].x!=0&&f[i].x*ansb<ansa*f[i].y)
			ansa=f[i].x,ansb=f[i].y;
		// cout<<ansa<<" "<<ansb<<"\n";
	}
	long long tmp=gcd(ansa,ansb);
	cout<<ansa/tmp<<" "<<ansb/tmp;
	return 0;
}
```
正经做法：
注意到这个区间只包含一个非零数，所以只要对每一个数向两边拓展即可。

不正经做法：
定义f[i]为以i为末尾信息密度最低区间，
    g[i]为以i为末尾信息密度不为零的最低区间。

$f[i]=min(a[i],\frac{f[i-1]+a[i]}{len[i-1]+1})$

$g[i]=min(a[i](a[i] \not=0),\frac{f[i-1]+a[i]}{len[i-1]+1})$

最后只需要统计g中非0最小值即可。

## [C](https://www.luogu.com.cn/problem/P17283)
```c++
#include<iostream>
#include<cstring>
#include<vector>
#include<cmath>
#include<map>
#include<algorithm>
using namespace std;
int n,q,r;
vector<int> g[1000005];
int siz[1000005];
int f[1000005];
int maxs[1000005];
void dfs(int u,int fa)
{
	siz[u]=1;
	for(int v:g[u])
	{
		if(v==fa) continue;
		dfs(v,u);
		siz[u]+=siz[v];
		maxs[u]=max(maxs[u],siz[v]);
	}
}
int main()
{
	ios::sync_with_stdio(0); cin.tie(0);
	cin>>n>>q>>r;
	for(int i=1,u,v;i<n;i++)
	{
		cin>>u>>v;
		g[u].push_back(v);
		g[v].push_back(u);
	}
	dfs(r,-1);
	for(int i=0;i<n;i++)
		f[i]=maxs[i]+n-siz[i];
	for(int i=1;i<n;i++)
		f[i]=max(f[i-1],f[i]);
	f[n]=n;
	while(q--)
	{
		int x;
		cin>>x;
		cout<<lower_bound(f,f+n,x)-f<<"\n";
	}
	return 0;
}
```
考虑若使数i不在数组T里，H最多能容纳多少个点。

显然，i不能选，不在它子树中的点的公共祖先不会是i，如果在它子树中，必定得在同一子树中，否则存在两点公共祖先为i。

所以最多容纳 $siz[i]+siz[son[i]]$ 个点。（ $son$ 为重儿子）

把它记录到数组中，在做前缀max，然后对每次询问二分查找即可。

## [D](https://www.luogu.com.cn/problem/P17284)
```c++
#include<iostream>
#include<cstring>
#include<vector>
#include<cmath>
#include<map>
#include<algorithm>
using namespace std;
int n;
int ans;
const int mod=1000000007;
int a[8008];
int f[2][8005][3];
int main()
{
	ios::sync_with_stdio(0); cin.tie(0);
	cin>>n;
	for(int i=1;i<=n;i++)
		cin>>a[i];
	f[0][0][0]=1;
	int nw=1;
	for(int i=1;i<=n;i++,nw=1-nw)
	{
		for(int j=0;j<=n;j++)
		{
			f[nw][j][0]=(0ll+f[1-nw][j][0]+f[1-nw][j][1]+f[1-nw][j][2])%mod;
			if(j>0) f[nw][j][1]=(0ll+f[1-nw][j-1][0]+(i==j)*f[1-nw][j-1][1])%mod*1ll*a[i]%mod;
			// for(int k=2,x=1ll*a[i]*a[i]%mod;k<=j;k++,x=1ll*x*a[i]%mod)
			// {
			// 	f[i][j][2]=(0ll+f[i][j][2]+1ll*f[i-1][j-k][0]*x%mod)%mod;
			// }
			if(j>=2) f[nw][j][2]=(1ll*f[nw][j-1][2]*a[i]%mod+1ll*f[1-nw][j-2][0]*a[i]%mod*a[i]%mod)%mod;
		}
	}
	nw=1-nw;
	ans=(0ll+f[nw][n][0]+f[nw][n][1]+f[nw][n][2])%mod;
	cout<<ans;
	return 0;
}
```

