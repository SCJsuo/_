[Educational Codeforces Round 80 (Rated for Div. 2)](https://codeforces.com/contest/1288)
![](https://cdn.luogu.com.cn/upload/image_hosting/5p7h3oku.png)
## [A](https://codeforces.com/contest/1288/problem/A)
[洛谷](https://www.luogu.com.cn/problem/CF1288A)
```c++
#include<iostream>
#include<cstdlib>
#include<vector>
#include<cmath>
using namespace std;
int T;
int n,d;
int solve(int a)
{
	return a+(d+a)/(a+1);
}
int main()
{
	cin>>T;
	while(T--)
	{
		cin>>n>>d;
		int tmp=sqrt(d);
		bool flag=true;
		for(int i=max(0,tmp-100);i<=tmp+100;i++)
		{
			if(solve(i)<=n)
			{
				cout<<"YES\n";
				flag=false;
				break;
			}
		}
		if(flag) cout<<"NO\n";
	}
	
	return 0;
}
```
根据基本不等式 $x+ \lceil \frac{d}{x+1} \rceil=x+1+ \lceil \frac{d}{x+1} \rceil -1 \geq x+1+\frac{d}{x+1} -1\geq 2 \sqrt d-1$ ，不考虑向上取整， $x$ 在 $x+1=\frac{d}{x+1}$ 即 $x=\sqrt d -1$ 时取得最小值，考虑到向上取整等的影响，只需要计算这个值周围一些数（由于 $x=\sqrt d -1$ ，只需要周围两三个）判断是否存在在 $n$ 以内的即可。

## [B](https://codeforces.com/contest/1288/problem/B)
[洛谷](https://www.luogu.com.cn/problem/CF1288B)
```c++
#include<iostream>
#include<cstdlib>
#include<vector>
#include<cmath>
using namespace std;
int T;
long long a,b;
int main()
{
	cin>>T;
	while(T--)
	{
		cin>>a>>b;
		int cnt=0;
		b++;
		while(b)
		{
			b/=10;
			cnt++;
		}
		cout<<(cnt-1)*a<<"\n";
	}
	
	return 0;
}
```

$conc(a,b)=10^{\lfloor log_{10}b \rfloor+1} \times a+b$

$a\times b+a+b=(b+1)\times a+b$

 $\because a\geq1 \therefore$ 当且仅当 $b+1=10^{\lfloor log_{10}b \rfloor+1}$时，满足条件。

显然， $\exists a\in \mathbb{N},b=\sum \limits_{i=0}^a 9\times 10^i \iff b+1=10^{\lfloor log_{10}b \rfloor+1}$

所以只需要统计所有在 $B$ 以内形如 $99...99$ 的数（即 $(log_{10} B+1) -1$ ）有多少个再乘上   $A$ 即可。

## [C](https://codeforces.com/contest/1288/problem/C)
[洛谷](https://www.luogu.com.cn/problem/CF1288C)
```c++
#include<iostream>
#include<cstdlib>
#include<vector>
#include<cmath>
using namespace std;
int n,m;
int f[15][1005];
const int mod=1e9+7;
int main()
{
	cin>>n>>m;
	for(int i=1;i<=n;i++)
		f[1][i]=1;
	for(int i=2;i<=m;i++)
	{
		for(int j=1;j<=n;j++)
			f[i][j]=(f[i-1][j]+f[i][j-1])%mod;
	}
	int ans=0;
	for(int i=1;i<=n;i++)
	{
		for(int j=i;j<=n;j++)
			ans=(ans+1ll*f[m][i]*f[m][n+1-j]%mod)%mod;
	}
	cout<<ans;
	return 0;
}
```
由于 $a$ 是单调不降， $b$ 是单调不增，所以只要 $a[n]\leq b[n]$ ，则对于任意下标 $i$， $a[i]\le b[i]$

所以只需要一个dp考虑 $f[i][j]$ 表示长度为 $i$ 的单调不降的序列最后一个数为 $j$ 的方案数。分别考虑前一个数是否等于 $j$ ，则有转移方程 $f[i][j]=f[i-1][j]+f[i][j-1]$ 。

由于 $a$ ， $b$ 是对称的，所以 $b$ 也可以用 $f$ 计算，最后枚举 $a$ ， $b$ 最后一个数，再将 $f$ 相乘即可。

## [D](https://codeforces.com/contest/1288/problem/D)
[洛谷](https://www.luogu.com.cn/problem/CF1288D)
```c++
#include<iostream>
#include<cstdlib>
#include<vector>
#include<cmath>
#include<bitset>
using namespace std;
int n,m;
int a[300005][10];
bitset<300005> f[10],x,y,tmp;
inline bool dfs(int u)
{
	if(u>m)
		return true;
	bitset<300005> x1=x,y1=y;
	tmp=x1&f[u];
	if(tmp.any())
	{
		x=tmp;
		if(dfs(u+1)) return true;
		x=x1;
	}
	tmp=y1&f[u];
	if(tmp.any())
	{
		y=tmp;
		if(dfs(u+1)) return true;
		y=y1;
	}
	return false;
}
inline bool check(int tmp)
{
	for(int j=1;j<=m;j++) f[j].reset();
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=m;j++)
			if(a[i][j]>=tmp) f[j][i]=true;
	}
	x.set(),y.set();
	return dfs(1);
}
int main()
{
	cin>>n>>m;
	int l=0,r=0;
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=m;j++)
			cin>>a[i][j],r=max(r,a[i][j]);
	}
	while(l<r)
	{
		int mid=l+r+1>>1;
		if(check(mid))
			l=mid;
		else
			r=mid-1;
	}
	check(l);
	for(int i=1;i<=n;i++)
	{
		if(x[i]) 
		{
			cout<<i<<" ";
			break;
		}
	}
	for(int i=1;i<=n;i++)
	{
		if(y[i]) 
		{
			cout<<i<<" ";
			break;
		}
	}
	return 0;
}
```
可以发现在 $n\le3\times 10^5$ 范围内直接找出最优的一组解是困难的，但如果已知 $\min\limits_{k=1}^{m} b_k$ ，可以先考虑对于每一列可以选哪几行（哪几个数组），使得 $a_{i,j}\ge\min\limits_{k=1}^{m} b_k$ ，再找到一种方法把所有列分成两部分，使得每部分可选的行的集合的交集不为空，则任取两部分交集中各一个数组，即可使得对于每一列，至少有一个数组的值比给定的要大。

同理，也可以判定给定的 $\min\limits_{k=1}^{m} b_k$ 是否可行，这样就可以用二分来求解 $\min\limits_{k=1}^{m} b_k$ 最大值，复杂度 $O(nmlog_2 n)$ （可以用bitset存储可行数组以优化常数），然后再根据上文求解即可。

## [E](https://codeforces.com/contest/1288/problem/E)
[洛谷](https://www.luogu.com.cn/problem/CF1288E)
```c++
#include<iostream>
#include<cstdlib>
#include<vector>
#include<cmath>
#define lb(x) (x&-x)
using namespace std;
int n,m;
int a[300005];
int l[300005],r[300005],pos[300005];
int c[600005];//1~m m+1~m+n
void add(int x,int v)
{
	while(x<=m+n)
	{
		c[x]+=v;
		x+=lb(x);
	}
}
int query(int x)
{
	int sum=0;
	while(x)
	{
		sum+=c[x];
		x-=lb(x);
	}
	return sum;
}
int main()
{
	cin>>n>>m;
	for(int i=m+1;i<=m+n;i++)
		add(i,1),pos[i-m]=i;
	for(int i=1;i<=m;i++)
	{
		cin>>a[i];
		l[a[i]]=1,r[a[i]]=max(r[a[i]],query(pos[a[i]]));
		add(pos[a[i]],-1);
		pos[a[i]]=m-i+1;
		add(pos[a[i]],1);
	}
	for(int i=1;i<=n;i++)
		r[i]=max(r[i],query(pos[i]));
	for(int i=1;i<=n;i++)
		cout<<(l[i]==0?i:l[i])<<" "<<r[i]<<"\n";	
	return 0;
}
```
首先，如果一个人发过消息，则最小位置为1，否则为最开始的位置。

然后要求最大位置要模拟整个过程，直接在数组中移动是非常慢的，用链表无法快速查询。但并不用保证两个数一定相邻，只需要前后关系就可以了。所以可以开一个大小为 $n+m$ 的数组其中 $m+1~m+n$ 为最开始的位置，若第 $i$ 个加进。则删除原本位置上的数，移到 $m-i+1$ 处，一个人的最大位置显然出现在他发消息的前一时刻或最后一刻，只需要统计他前面有多少个数，可以用树状数组优化至 $O(log_2 (n+m))$ ，最后总时间复杂度为 $O((n+m)log_2 (n+m))$ 。
