[Educational Codeforces Round 130 (Rated for Div. 2)](https://codeforces.com/contest/1697)
![](https://cdn.luogu.com.cn/upload/image_hosting/42ce5gj9.png)
## [A](https://codeforces.com/contest/1697/problem/A)
[洛谷](https://www.luogu.com.cn/problem/CF1697A)
```
#include<bits/stdc++.h>
using namespace std;
int T;
int n,m;
int a[105];
int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
    cin>>T;
    while(T--)
    {
        cin>>n>>m;
        for(int i=1;i<=n;i++)
            cin>>a[i];
        int ans=0;
        for(int i=1;i<=n;i++)
        {
            if(m>=a[i]) m-=a[i];
            else ans+=a[i]-m,m=0;
        }
        cout<<ans<<"\n";
    }
	return 0;
}
```
显然，无论在哪一个长凳恢复能量，效果都是一样的，所以可以在能量不足到下一个长凳时恢复到恰好能到下一个长凳的能量。
## [B](https://codeforces.com/contest/1697/problem/B)
[洛谷](https://www.luogu.com.cn/problem/CF1697B)
```
#include<bits/stdc++.h>
using namespace std;
int n,m;
int a[200005];
long long sum[200005];
int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
        cin>>n>>m;
    for(int i=1;i<=n;i++)
        cin>>a[i];
    sort(a+1,a+1+n);
    for(int i=1;i<=n;i++)
        sum[i]=sum[i-1]+a[i];
    while(m--)
    {
        int x,y;
        cin>>x>>y;
        cout<<sum[n-x+y]-sum[n-x]<<"\n";
    }
	return 0;
}
```
因为要使免费的总价格尽可能高，所以选的一定是价格最高的x个物品，若选更多的物品，会使其中价格最低的y个总价更低，所以只要先排序，再用前缀和求$\sum a[i] ,i\in [n-x+y,n-x]$即可。
## [C](https://codeforces.com/contest/1697/problem/C)
[洛谷](https://www.luogu.com.cn/problem/CF1697C)
```
#include<bits/stdc++.h>
using namespace std;
int T;
int n;
string a,b;
string ac1,ac2;
int suma[100005],sumc[100005];
queue<int> q;
int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
    cin>>T;
    while(T--)
    {
        cin>>n>>a>>b;
        ac1="",ac2="";
        a=" "+a,b=" "+b;
        for(int i=1;i<=n;i++)
        {
            if(a[i]!='b') ac1+=a[i];
            if(b[i]!='b') ac2+=b[i];
        }
        if(ac1!=ac2)
        {
            cout<<"NO\n";
            continue;
        }
        while(!q.empty()) q.pop();
        for(int i=1;i<=n;i++)
        {
            if(b[i]=='b') q.push(i);
            suma[i]=suma[i-1],sumc[i]=sumc[i-1];
            if(a[i]=='a') suma[i]++;
            if(a[i]=='c') sumc[i]++;
        }
        bool flag=false;
        for(int i=1,pre=0x3f3f3f3f;i<=n;i++)
        {
            if(a[i]=='b')
            {
                int j=q.front(); q.pop();
                if(j<i&&sumc[i]-sumc[j-1]!=0||j>i&&suma[j]-suma[i]!=0)
                {
                    cout<<"NO\n";
                    flag=true;
                    break;
                }
            }
        }
        if(flag)
            continue;
        cout<<"YES\n";
    }
	return 0;
}
```
首先，只有s中字符b会移动，且不影响ac顺序，所以s，t中将b删去后的子序列若不相同则一定为NO。若相同，因为b往前交换前面必须是a，往后则必须是c，又因为s，t中b按序匹配一定是不劣于有交叉情况的（具体的懒得写了），所以只需统计a，c数量的前缀和，计算每个对应的b间是否只有a（s中b在t前）或c（s中b在t后）。
## [D](https://codeforces.com/contest/1697/problem/D)
[洛谷](https://www.luogu.com.cn/problem/CF1697D)
```
#include<bits/stdc++.h>
using namespace std;
int n,m;
struct node
{
    char c;
    int pos;
}a[35];
string s;
char t;
bool check(int id,int x)
{
    cout<<"? 2 "<<a[id].pos<<" "<<x<<"\n";
    int tmp;
    cin>>tmp;
    if(tmp==m-id+1) return true;
    return false;
}
bool cmp(node p,node q)
{
    return p.pos<q.pos;
}
int main()
{
	// ios::sync_with_stdio(0);
	// cin.tie(0);
    cin>>n;
    cout<<"? 1 1\n";
    m++;
    cin>>a[m].c;
    a[m].pos=1;
    s+=a[m].c;
    for(int i=2;i<=n;i++)
    {
        if(m<26)
        {
            cout<<"? 2 1 "<<i<<"\n";
            int tmp;
            cin>>tmp;
            if(tmp==m) goto FIND;
            cout<<"? 1 "<<i<<"\n";
            m++;
            cin>>a[m].c;
            a[m].pos=i;
            s+=a[m].c;
            continue;
        }
        FIND:
        int l=1,r=m;
        while(l<r)
        {
            int mid=l+r+1>>1;
            if(check(mid,i))
                l=mid;
            else
                r=mid-1;
        }
        s+=a[l].c;
        a[l].pos=i;
        sort(a+1,a+1+m,cmp);
    }
    cout<<"! "<<s;
	return 0;
}
```
因为第一个问题只能用26次，所以肯定是每个1问题都得找到一个新的字母。所以如果这个字母是一个新的，就直接问它是什么。即先问```? 2 1 i```如果与现有字母数量不一样则再问```? 1 i```；如果一样，则可以一个一个往前推，但这样显然超出了问题二6000个，但可以把每个字母出现的最后一个位置记录下来，按从后向前顺序问，还可以加一个二分查找，看与它相同的字母是否在最近mid个出现的字母中，这样第二个问题的数量就小于$(log_2 26+1)\times n<(5+1)*1000=6000$，符合限制。
## [E](https://codeforces.com/contest/1697/problem/E)
[洛谷](https://www.luogu.com.cn/problem/CF1697E)
```c++
#include<iostream>
#include<cstdlib>
#include<vector>
#define dis(i,j) (abs(x[i]-x[j])+abs(y[i]-y[j]))
#define F return flag[i]=false
#define T return flag[i]=true
using namespace std;
const int mod=998244353;
int n;
int x[105],y[105];
int minn[105];
vector<int> g[105];
bool flag[105];
bool vis[105];
long long c[105][105];
long long fac[105];
int cnt;
vector<int> col[105];
int b[105];
bool solve(int i)
{
	if(vis[i]) return flag[i];
	if(g[i].size()>3||g[i].size()==0) F;
	for(int m=0;m<g[i].size();m++)
	{
		int j=g[i][m];
		if(g[j].size()!=g[i].size()) F;
		for(int k=0;k<g[j].size();k++)
		{
			int u=g[j][k];
//			cout<<j<<" "<<k<<" "<<u<<"\n";
			if(u==i) continue;
			bool is_inI=0;
			for(int t=0;t<g[i].size();t++)
				if(u==g[i][t]) is_inI=1;
//			cout<<i<<" "<<is_inI<<"\n";
			if(!is_inI) F;
		}
	}
	cnt++;
	col[cnt].push_back(i),vis[i]=true;
	for(int m=0;m<g[i].size();m++)
		col[cnt].push_back(g[i][m]),vis[g[i][m]]=true,flag[g[i][m]]=true;
	T;
}
int main()
{
	cin>>n;
	fac[0]=1;
	for(int i=0;i<=n;i++)
	{
		c[0][i]=1;
		for(int j=1;j<=i;j++)
			c[j][i]=(c[j-1][i-1]+c[j][i-1])%mod;
		if(i>0) fac[i]=fac[i-1]*i%mod;
	}
	for(int i=1;i<=n;i++)
		cin>>x[i]>>y[i],minn[i]=0x3f3f3f3f;
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=n;j++)
		{
			if(i==j) continue;
			minn[i]=min(minn[i],dis(i,j));
		}
	}
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=n;j++)
			if(dis(i,j)==minn[i]) g[i].push_back(j);
	}
	for(int i=1;i<=n;i++)
		if(!solve(i)) col[++cnt].push_back(i);
	for(int i=1;i<=cnt;i++)
		b[col[i].size()]++;
	long long ans=0;
	for(int i2=0;i2<=b[2];i2++)
	{
		for(int i3=0;i3<=b[3];i3++)
		{
			for(int i4=0;i4<=b[4];i4++)
			{
				ans=(ans+c[i2][b[2]]%mod*c[i3][b[3]]%mod*c[i4][b[4]]%mod*c[n-i2-2*i3-3*i4][n]%mod*fac[n-i2-2*i3-3*i4]%mod)%mod;
			}
		}
	}
	cout<<ans;
	return 0;
}
```
*比赛时时间不足虽然注意到了几个性质但没写完*

性质：

1. 任何一个点都属于一个点集，使得对于每一种方案，这个点集内的点要么都同色，要么都不同色，且不与其他点集同色（由两条约束比较显然就能得到，2保证它与它同色的点如果有一定包含是那个点集，1保证它一定被那个点集包含）
2. 任何一个点属于的点集为据它最近的点的集合，且集合中每个点都满足这个条件，否则它单独为一个点集。（由两条约束比较显然就能得到）
3. 任何一个点集大小不超过四（若有两个点在一个点集中，以这连个点为圆心画出一个曼哈顿距离圆（一个斜着的正方形），最多有两个交点）

所以只要对一个未被加入进点集中的点，判断它与据它最近的点的集合是否符合题意。最后算出大小为1234的点集各有多少个，对与所有大小为$i$的点集（$b[i]$个）考虑有$j_i$个算作一个整体，则一共有$C^{j_i} _{b[i]} $种选法。最后一共有$n-j_2-2j_3-3j_4$种颜色，即有$A^{n-j_2-2j_3-3j_4}_n=C^{n-j_2-2j_3-3j_4}_n \times (n-j_2-2j_3-3j_4)!$种选法，最终有$\sum (\prod C^{j_i} _{b[i]} \times C^{n-j_2-2j_3-3j_4}_n \times (n-j_2-2j_3-3j_4)!)$方案。