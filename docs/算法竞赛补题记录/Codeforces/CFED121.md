# CFED121

## D [Martial Arts Tournament](https://codeforces.com/contest/1626/problem/D)

**题意：** 给一堆人的重量，这堆人要被按照阈值x和y分成三批人。但每批人要满足人数是2的幂次。不足时要找人替补。问怎么划分使得替补人数最少，问这个最少人数。

**解法：** 去重，排序，枚举第一段到哪里。第二段和第三段用二分检查和的大小来更新答案。

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <cstdlib>
#include <algorithm>
#include <string>
#include <vector>
#include <map>
#include <unordered_map>
#include <set>
#include <unordered_set>
#include <queue>
using namespace std;
typedef long long ll;
const int N=2e5+10;
int T;

ll n,nb;
ll a[N];
ll b[N];
ll sum[N];

ll cal(ll x)
{
    ll tmpans=1;
    while(tmpans<x) tmpans<<=1;
    tmpans-=x;

    return tmpans;
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    cin>>T;
    for(int _=1;_<=T;_++)
    {
        cin>>n;
        for(int i=1;i<=n;i++)        {
            cin>>a[i];
        }

        sort(a+1,a+1+n);
        for(int i=1;i<=n+5;i++)
        {
            b[i]=a[i];
        }
        nb = unique(b+1,b+1+n) - (b+1);
        sum[0]=sum[1]=0;
        for(int i=1,j=1;i<=n;i++)
        {
            if(a[i]==b[j])
            {
                sum[j] ++;
            }
            else
            {
                j++;
                sum[j]=sum[j-1]+1;
            }
        }

        ll ans=1e18;
        for(int i=0;i<=nb;i++)
        {
            // ll tmpans = 1<<(ll)ceil(log2());
            // ll tmpans=1;
            // while(tmpans<sum[i]) tmpans<<=1;
            // tmpans-=sum[i];
            ll tmpans = cal(sum[i]);

            for(int z=0;z<=18;z++)
            {
                ll zz  = 1ll<<z; //2^z;

                ll L=i;
                ll R=nb;

                while(L<R)
                {
                    ll mid=(L+R+1)>>1;
                    if(sum[mid]-sum[i]<=(zz))
                    {
                        L=mid;
                    }
                    else R=mid-1;
                }

                ll tmpans2 = tmpans;
                tmpans2 += zz - (sum[L]-sum[i]);
                tmpans2 += cal(n - sum[L]);
                ans = min(ans,tmpans2);
            }
        }

        cout<<ans<<endl;

    }
    return 0;
}
```

## E [Black and White Tree](https://codeforces.com/contest/1626/problem/E)

**题意：** 给树，树上有黑白点。对于每个点要回答以下询问：如果芯片放在这个点上能不能移动到任何一个黑点。移动的规则是每次选一个黑点（但两次不能连续选同一个）然后朝它动一步。

**解法：**

相邻的点x能到y且下一步不会强制回来的充要条件是：

- y就是黑点
- 删去x和y的连边后，y所在联通分量中有两个黑点。

根据这个规则建立一个新图，然后在新图上bfs即可。

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <cstdlib>
#include <algorithm>
#include <string>
#include <vector>
#include <map>
#include <unordered_map>
#include <set>
#include <unordered_set>
#include <queue>
using namespace std;
typedef long long ll;
const int N=4e5+10;

int n;
vector<int> g[N], g2[N];
int cnt[N];
int c[N],sum;

void dfs(int x,int fa)
{
    cnt[x] += c[x];
    for(auto &y:g[x])
    {
        if(y==fa) continue;
        dfs(y,x);
        cnt[x] += cnt[y];
    }
}

void dfs2(int x,int fa)
{
    for(auto &y:g[x])
    {
        if(y==fa) continue;
        dfs2(y,x);

        // y -> x
        // x -> y (inverse edge)
        if(c[x] || (sum - cnt[y]) >=2)
        {
            g2[x].emplace_back(y);
        }

        // x -> y
        // y -> x (inverse edge)
        if(c[y] || (cnt[y]) >=2)
        {
            g2[y].emplace_back(x);
        }

    }

    if(fa!=-1)
    {
        // x -> fa
        // fa -> x (inverse edge)
        if(c[fa] || (sum - cnt[x]) >= 2)
        {
            g2[fa].emplace_back(x);
        }

        // fa -> x
        // x -> fa (inverse edge)
        if(c[x] || (cnt[x]) >= 2)
        {
            g2[x].emplace_back(fa);
        }
    }
}

int vis[N];
void bfs()
{
    queue<int> q;

    for(int i=1;i<=n;i++)
    {
        if(c[i])
        {
            q.push(i);
        }
    }

    while(q.size())
    {
        auto x = q.front();
        q.pop();
        if(vis[x]) continue;
        vis[x]=true;
        for(auto &y:g2[x])
        {
            q.push(y);
        }
    }
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    cin>>n;
    for(int i=1;i<=n;i++)
    {
        cin>>c[i];
        sum+=c[i];
    }

    for(int i=1;i<=n-1;i++)
    {
        int x,y;
        cin>>x>>y;
        g[x].emplace_back(y);
        g[y].emplace_back(x);
    }

    dfs(1,-1);
    dfs2(1,-1);
    bfs();

    for(int i=1;i<=n;i++)
    {
        cout<< vis[i]<< " ";
    }

    return 0;
}
```
