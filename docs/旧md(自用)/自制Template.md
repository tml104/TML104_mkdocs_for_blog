# 自制Template

# 规范

- 数据结构：使用namespace+struct封装。
- 散装函数：使用namespace封装
- 方法、函数前使用注释描述功能、形参说明和返回值说明

- namespace 最开头是常量定义（比如MAXsize之类的），然后使用空行和注释区分local变量和public变量
- 仅struct或namespace内使用的函数使用双下划线开头
- namespace中的变量名也请使用注释简要说明变量用途



## 待讨论

- 图论：邻接表格式？

### [算法名]

算法描述：输入接口、运行算法需要的操作、输出接口、时间复杂度

| Funs \|\| Methods | Reliance | Description | Time Complexity | Inputs | Outputs or Returns |
| ----------------- | -------- | ----------- | --------------- | ------ | ------------------ |
|                   |          |             |                 |        |                    |
|                   |          |             |                 |        |                    |
|                   |          |             |                 |        |                    |



# 字符串

## KMP(OLD)

```c++
#include <iostream>
#include <cstring>
using namespace std;
const int N=1e6+10;
char a[N],b[N];
int inext[N],f[N];

void prenext(int n) //n: length of A （模式串）
{
    inext[1]=0;
    for(int i=2,j=0;i<=n;i++)
    {
        while(j>0 && a[i]!=a[j+1]) j=inext[j];
        if(a[i]==a[j+1]) j++; //extendable 
        inext[i]=j;
    }
}

void match(int n,int m) //n: length of A（模式串）; m: length of B （主串）
{
    for(int i=1,j=0;i<=m;i++)
    {
        while(j>0 && (j==n || b[i]!=a[j+1])) j=inext[j];
        if(b[i]==a[j+1]) j++;
        f[i]=j;
        
        if(f[i]==n) //Match
        {
            cout<<i-n+1<<endl;
        }
    }
}

int main()
{
    ios::sync_with_stdio(false);
    a[0]=b[0]='?';
    //cin>>b+1>>a+1;

    cin>>(a+1);
    int la=strlen(a)-1,lb=strlen(b)-1;
    prenext(la);
    //match(la,lb);
    for(int i=1;i<=la;i++)
    {
        cout<<inext[i]<<" ";
    }
    return 0;
}

```

## EXKMP

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

const int Mod=10007;

namespace EXKMP
{
    const int N=2e5+10;

    int z[N],extend[N];
    char a[N],b[N];
    int a_len,b_len;
    //z,ext:b串关于自己的每个位置的lcp（最长公共前缀）长度，a串关于b串的最长公共前缀长度
    //a，b：串（b是模式串）
    //a_len, b_len： 串长

    //求出b的z函数（1号位置不是良定义的）
    void get_z(char b[], int n=b_len)
    {
        int l=1,r=1;
        z[1]=n; //undefined
        for(int i=2;i<=n;i++)
        {
            z[i]=0;
            if(i<=r)
                z[i]=min(z[i-l+1],r-i+1);//init        
            while (i+z[i]<=n && b[z[i]+1]==b[i+z[i]]) //i+z[i] is next position, so accessible pos is i+z[i]-1
                z[i]++;
            if(i+z[i]-1>r)
                l=i,r=i+z[i]-1;
        }
    }

    //求出a关于b的ext数组
    void exkmp(char a[],char b[],int m=a_len,int n=b_len)
    {
        extend[1]=0;
        while(1+extend[1]<=m && a[1+extend[1]]==b[1+extend[1]]) extend[1]++;
        int l=1,r=1;
        for(int i=2;i<=m;i++)
        {
            extend[i]=0;
            if(i<=r)
                extend[i]=min(z[i-l+1],r-i+1);
            while (i+extend[i]<=m && b[extend[i]+1]==a[i+extend[i]]) 
                extend[i]++;
            if(i+extend[i]-1>r)
                l=i,r=i+extend[i]-1;
        }
    }

}
using namespace EXKMP;

int T;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    cin>>T;
    for(int _=1;_<=T;_++)
    {
        cin>>b_len;
        cin>>b+1;
        get_z(b,b_len);
        //exkmp(a,b,a_len,b_len)
        ll ans=0;
        for(int i=1;i<=b_len;i++)
        {
            // cout<<z[i]<<"-\n";
            ans=(ans+z[i])%Mod;
        }
        cout<<ans<<endl;
    }
    return 0;
}
```

## 最小表示法

```c++

#include <iostream>
using namespace std;
const int N=6e5+7;
int n,a[N];

int minimal_expression()
{
    int i=1,j=2;
    while(i<=n && j<=n)
    {
        int k=0;
        for(;k<n && a[i+k]==a[j+k];k++);
        if(k==n) break;
        if(a[i+k]>a[j+k])
        {
            i=i+k+1;
            i+=(i==j);
        }
        else
        {
            j=j+k+1;
            j+=(i==j);
        }
    }
    return min(i,j);
}

int main()
{
    ios::sync_with_stdio(false);
    cin>>n;
    for(int i=1;i<=n;i++)
    {
        cin>>a[i];
        a[n+i]=a[i];
    }
    for(int i=minimal_expression(),j=1;j<=n;i++,j++)
    {
        cout<<a[i]<<" ";
    }
    return 0;
}
```

## Manacher（分开求解法）

```c++
//luogu3805 马拉车 分开求解法
#include <iostream>
#include <cstring>
#include <memory.h>
using namespace std;

namespace Manacher
{
    const int N = 1.1e7 + 100;

    //public
    char s[N];
    int len;
    int d1[N], d2[N];
    //s:原字符串
    //len:s的串长
    //d1,d2：奇数位置，偶数位置（两个字符中间）的回文半径。
    //对于d2，下标i表示的是夹在i~i+1之间的位置为中心的回文半径

    void get_d1()
    {
        d1[1] = 1;
        int l = 1, r = 1;
        for (int i = 2; i <= len; i++)
        {
            int k = (i > r) ? 1 : min(d1[l + r - i], r - i + 1);
            while (1 <= i - k && i + k <= len && s[i - k] == s[i + k])
                k++;
            d1[i] = k;
            if (i + k - 1 > r)
            {
                r = i + k - 1;
                l = i - k + 1;
            }
        }
    }

    void get_d2()
    {
        d2[0] = 0;
        int l = 0, r = -1;
        for (int i = 1; i <= len; i++)
        {
            int k = (i >= r) ? 0 : min(d2[l + r - i - 1], r - i);
            while (1 <= i - k && i + k + 1 <= len && s[i - k] == s[i + k + 1])
                k++;
            d2[i] = k;
            if (i + k > r)
            {
                r = i + k;
                l = i - k + 1;
            }
        }
    }
}
using namespace Manacher;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    cin >> s + 1;
    len = strlen(s + 1);
    get_d1();
    get_d2();
    int ans = 1;
    for (int i = 1; i <= len; i++)
    {
        ans = max(ans, d1[i] * 2 - 1);
        ans = max(ans, d2[i] * 2);
    }
    cout << ans << endl;
    return 0;
}
```


## 马拉车（插入井号法）

```c++

//luogu3805 马拉车 插入井号法
#include <iostream>
#include <cstring>
#include <memory.h>
using namespace std;


const int N=1.1e7+100;
char s[N],s2[N*2];
int n,nn;
int d1[N*2];

void get_d1()
{
    nn=1;
    s2[0]='$';
    s2[1]='#';
    for(int i=1;i<=n;i++)
    {
        s2[++nn]=s[i];
        s2[++nn]='#';
    }

    d1[1]=1;
    int l=1,r=1;
    for(int i=2;i<=nn;i++)
    {
        int k=(i>r)?1:min(d1[l+r-i],r-i+1);
        while(1<= i-k && i+k<=nn && s2[i-k]==s2[i+k]) k++;
        d1[i]=k;
        if(i+k-1>r)
        {
            r=i+k-1;
            l=i-k+1;
        }
    }

}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);cout.tie(0);
    cin>>s+1;
    n=strlen(s+1);
    get_d1();
    int ans=0;
    for(int i=1;i<=nn;i++)
    {
        ans=max(ans,d1[i]-1);
    }
    cout<<ans<<endl;
    return 0;
}

```


## 后缀数组（SA）

sa是字符串s的每一个后缀的排名到字符串s中的下标的映射数组。rk是每一个后缀的下标到排名的映射数组。ht是第i名和第i-1名(注意是排名)的最长公共前缀长度。

输入字符串s和长度len，调用get_sa和get_ht，得到的sa、rk、ht数组。

| Funs \|\| Methods | Reliance | Time Complexity | Inputs                       | Outputs                  | Description        |
| ----------------- | -------- | --------------- | ---------------------------- | ------------------------ | ------------------ |
| get_sa            |          | nlogn           | 输入s的长度len；字符串s。    | 修改sa、rk数组作为输出。 | 得到后缀数组sa、rk |
| get_ht            | get_sa   | n               | 输入s的长度len；数组sa和ht。 | 修改数组ht作为输出。     | 得到后缀数组ht     |

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <memory.h>
using namespace std;

//后缀数组：输入字符串s和其长度len，执行get_sa、get_ht，返回字符串的sa、rk、ht数组。复杂度nlogn。
namespace SA
{
    const int N = 1e6 + 5 , M=300; //N：长度上限 M：字符集大小
    //public
    char s[N]; //s:字符串
    int len;  //len:的长度
    int sa[N],rk[N],ht[N]; //sa、rk、ht数组
    //sa: 排名 -> 下标
    //rk: 下标 -> 排名

    //local
    int oldrk[N * 2], id[N], px[N], cnt[N];
    //id: (按第二/第一关键字排序后的)排名->下标(临时数组)
    //px: rk[id[i]], 在按第一关键字排序时,等价于第二关键字排名->下标-> w-1时该下标处的排名
    //ht: height数组

    //判断旧rk数组中对应的两个位置x、y以及x+w、y+w是否相等
    bool __is_equal(int x, int y, int w)
    {
        return oldrk[x] == oldrk[y] && oldrk[x + w] == oldrk[y + w];
    }

    //输入s的长度len，修改sa数组作为输出
    void get_sa(int len = len)
    {
        //计数排序
        //第一次排序（w=0）
        int m = M, p = 0;
        for (int i = 1; i <= len; i++)
            cnt[rk[i] = s[i]]++;
        for (int i = 1; i <= m; i++)
            cnt[i] += cnt[i - 1]; //前缀和
        for (int i = len; i >= 1; i--)
            sa[cnt[rk[i]]--] = i;

        for (int w = 1; w < len; w <<= 1, m = p)
        {
            p = 0;
            for (int i = len; i > len - w; i--) //第二关键字无穷小的先放
            {
                id[++p] = i;
            }
            for (int i = 1; i <= len; i++)
                if (sa[i] > w)
                    id[++p] = sa[i] - w; // i:排名, sa[i]:下标, sa[i]-w 下标偏移
            //这里sa是w-1时的,因此sa看作是w时的第二关键字的排名->下标映射,则sa[i]-w是此时第一关键字的下标
            //id不再返存回sa数组中
            memset(cnt, 0, sizeof(cnt));
            for (int i = 1; i <= len; i++)
                cnt[px[i] = rk[id[i]]]++;
            for (int i = 1; i <= m; i++)
                cnt[i] += cnt[i - 1];
            for (int i = len; i >= 1; i--)
                sa[cnt[px[i]]--] = id[i];
            memcpy(oldrk, rk, sizeof(rk));
            p = 0;
            for (int i = 1; i <= len; i++)
            {
                rk[sa[i]] = __is_equal(sa[i], sa[i - 1], w) ? p : ++p;
            }
        }
    }

    //(get_sa)得到后缀数组ht：输入s的长度len、数组sa和ht，修改数组ht为结果
    void get_ht(int len=len)
    {
        int k = 0; //上一ht值
        for (int i = 1; i <= len; i++)
        {
            if (k)
                k--;                                 // k>=0
            while (s[i + k] == s[sa[rk[i] - 1] + k]) //根据定义暴力求
            {
                k++;
            }
            ht[rk[i]] = k;
        }
    }
} // namespace SA

using namespace SA;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0), cout.tie(0);
    cin >> s + 1;
    len = strlen(s + 1);
    get_sa();
    get_ht();
    for (int i = 1; i <= len; i++)
        cout << sa[i] << " ";
    return 0;
}
```

## 後綴自動機

给定一个只包含小写字母的字符串S,请你求出 S 的所有出现次数不为 1 的子串的出现次数乘上该子串长度的最大值。

```c++
//luogu 3804
#include <iostream>
#include <cstdio>
#include <memory.h>
#include <cstring>
#include <queue>
using namespace std;
const int N=2e6+5;
struct SAM_NODE
{
    int inext[26],len,link,siz;
}sam[N];
int total,last;
char s[N];
int len;

void sam_init()
{
    sam[0].len=sam[0].siz=0;
    sam[0].link=-1;
    memset(sam[0].inext,0,sizeof(sam[0].inext));
    total=last=0;
}

void sam_extend(char c)
{
    int cur=++total,cc=c-'a';
    sam[cur].len=sam[last].len+1;
    int p=last;
    while (p!=-1 && !sam[p].inext[cc])
    {
        sam[p].inext[cc]=cur;//还没有到字符c的转移，我们就添加一个到状态c的转移
        p=sam[p].link;
    }
    if(p==-1)//未找到现有转移
        sam[cur].link=0;
    else//已经找到现有转移
    {
        int q=sam[p].inext[cc];
        if(sam[p].len+1==sam[q].len)//连续转移
            sam[cur].link=q;
        else//不连续转移
        {
            int clone=++total;
            sam[clone].len=sam[p].len+1;
            memcpy(sam[clone].inext,sam[q].inext,sizeof(sam[q].inext));
            sam[clone].link=sam[q].link;
            while (p!=-1 && sam[p].inext[cc]==q)
            {
                sam[p].inext[cc]=clone;
                p=sam[p].link;
            }
            sam[q].link=sam[cur].link=clone;
        }
    }
    last=cur;
    sam[cur].siz=1; // 标记终点节点
}

void sam_marksuf()
{
    int p=last;
    while (p!=-1)
    {
        //...向标记数组写入
        p=sam[p].link;
    }
}

void sam_traverse(int x=0)//调试用，输出sam结构
{
    cout<<x<<":";
    for(int i=0;i<26;i++)
    {//转移字符 标号
        if(sam[x].inext[i]) cout<<(char)('a'+i)<<sam[x].inext[i]<<" ";
    }
    cout<<endl;
    for(int i=0;i<26;i++)
        if(sam[x].inext[i]) sam_traverse(sam[x].inext[i]);
}

void sam_tr_suftree()//调试用，输出后缀链接树结构
{
    //从叶子到根
    for(int i=1;i<=total;i++)
    {
        if(sam[i].siz)
        {
            int p=i;
            while (p!=-1)
            {
                cout<<p<<" ";
                p=sam[p].link;
            }
            cout<<endl;
        }
    }
}

long long ans=0;
int deg[N];
void get_size()//从叶子节点开始bfs，累加siz，非叶子节点的siz值就是这个节点的终点集合大小，也即这个节点对应最长串出现的次数
{
    queue<int> q;
    for(int i=1;i<=total;i++)
    {
        deg[sam[i].link]++;
        /*bool flag=true;
        for(int j=0;j<26;j++)
            if(sam[i].inext[j]) {flag=false;break;}
        if(flag) q.push(i);*/ //兄啊这么写根本和link没关系啊！
    }
    for(int i=1;i<=total;i++)
        if(deg[i]==0) q.push(i);
    while (q.size())
    {
        int x=q.front();
        q.pop();
        sam[sam[x].link].siz+=sam[x].siz;
        deg[sam[x].link]--;
        if(deg[sam[x].link]==0) q.push(sam[x].link);
    }
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    cin>>s+1;
    len=strlen(s+1);
    sam_init();
    for(int i=1;i<=len;i++)
        sam_extend(s[i]);
    //sam_traverse(0);
    //sam_tr_suftree();
    get_size();
    for(int i=1;i<=total;i++)
        if(sam[i].siz>=2) ans=max(ans,(long long)sam[i].siz*sam[i].len);
    cout<<ans<<endl;
    return 0;
}

```


## 字符串周期？

### 2017-2018 ACM-ICPC, Asia Daejeon Regional Contest

题目描述：给出一个长度为\(n\)的序列\(a_i\)，这个序列第\(k+1\)起到最后是若干个循环，周期为\(p\)，最后一个循环可以不完整。求\(k+p\)最小值对应的\(k, p\)。

solution
将序列\(a_i\)翻转，做一次\(KMP\)(\(fail[i]\))，枚举\(k+1\)的位置\(i\)，则周期\(p=i-fail[i], k=n-i\)。更新答案。

```c++
#include<bits/stdc++.h>
using namespace std;

int n;
int a[1000010];
int nnext[1000010];
int k=10000000,p=10000000;
void cal_next()
{
	nnext[1]=0;
	for(int i=2, j=0; i<=n; i++)
	{
		while(j>0 && a[i]!=a[j+1]) j=nnext[j];

		if(a[j+1]==a[i])
			j++;
		nnext[i]=j;

	}
}


int main()
{
	cin>>n;
	for(int i=n; i>=1; i--)
		scanf("%d",&a[i]);

	cal_next();
	
	for(int i=1;i<=n;i++)
	{
		int tempk=n-i,tempp=i-nnext[i]+1;
		if(tempk+tempp<k+p)
		{
			k=tempk;
			p=tempp;
		}
	}
	
	cout<<k<<" "<<p-1;
	return 0;
}

```

### 2021 CCPC 女生赛 F

不难发现行与列是两个独立的问题，因此只需要求出行的最短循环节的长度，再求出列的
最短循环节的长度，相乘就是答案。
以行为例，首先通过 Hash 将问题转化为一维问题。一维问题则是经典问题，对于一个长
度为 n 的字符串，长度为 d 的前缀是循环节当且仅当长度为 n − d 的前后缀相等，因此需要找到这个字符串最长的前缀，满足该前缀也是该字符串的后缀。可以枚举所有可能的 d 然后使用Hash O(1) 判断；也可以使用 KMP 算法求出 nxt 数组，答案即为 n − nxt[n]。
时间复杂度 O(n^2 + qn)。

```c++
#include<bits/stdc++.h>
using namespace std;

string a[2010];

const int P=131;
long long p[2010];
long long hashrow[2010][2010],hashcol[2010][2010];

long long drow[2010],dcol[2010];
int n,q;

int nex[2010];
void getnex(long long s[],long long len)
{
    int i=0;
    int j=-1;
    nex[0]=-1;
    while(i<len)
    {
        if(j==-1||s[i]==s[j])
        {
            i++;
            j++;
            nex[i]=j;
        }
        else
            j=nex[j];
    }
}


int main()
{
	cin>>n>>q;
	for(int i=1; i<=n; i++)
	{
		cin>>a[i];
		a[i]=" "+a[i];
	}

	p[0]=1;
	for(int i=1; i<=n; i++) p[i]=p[i-1]*P;

	for(int i=1; i<=n; i++)
	{
		for(int j=1; j<=n; j++)
		{
			hashrow[i][j]=hashrow[i-1][j]*P+a[i][j]-'a';
			hashcol[i][j]=hashcol[i][j-1]*P+a[i][j]-'a';
		}
	}

	for(int t=1; t<=q; t++)
	{
		long long x1,y1,x2,y2;
		cin>>x1>>y1>>x2>>y2;
		long long ans1=0,ans2=0;
		
		for(int j=y1; j<=y2; j++)
			drow[j-y1]=hashrow[x2][j]-hashrow[x1-1][j]*p[x2-x1+1],nex[j-y1]=0;
		getnex(drow,y2-y1+1);
		ans1=y2-y1+1-nex[y2-y1+1];
		for(int i=x1; i<=x2; i++)
			dcol[i-x1]=hashcol[i][y2]-hashcol[i][y1-1]*p[y2-y1+1],nex[i-x1]=0;
		getnex(dcol,x2-x1+1);
		ans2=x2-x1+1-nex[x2-x1+1];
		
		cout<<ans1*ans2<<endl;	
	}

	return 0;
}
```


## AC 自动机

### luogu 3796 AC自动机（加强版）

有N个由小写字母组成的模式串以及一个文本串 T。每个模式串可能会在文本串中出现多次。你需要找出哪些模式串在文本串 T 中出现的次数最多。

```c++
#include <iostream>
#include <cstdio>
#include <memory.h>
#include <queue>
using namespace std;
const int N=1e6+10;
int n;
char s[151][100],t[N];
int trie[N][26],total,id[N],fail[N],last[N];
int cnt[N];

void insert(char *s,int num)
{
    int u=0;
    for(int i=1;s[i];i++)
    {
        int x=s[i]-'a';
        if(!trie[u][x]) trie[u][x]=++total;
        u=trie[u][x];
    }
    id[u]=num;
}

void build()
{
    queue<int> q;
    for(int i=0;i<26;i++)
    {
        if(trie[0][i]) q.push(trie[0][i]);
    }
    while (q.size())
    {
        int u=q.front();
        q.pop();
        for(int i=0;i<26;i++)
        {
            if(trie[u][i])
            {
                fail[trie[u][i]]=trie[fail[u]][i];
                last[trie[u][i]]=(id[fail[trie[u][i]]]==0)?(last[fail[trie[u][i]]]):(fail[trie[u][i]]); //有效字符串结尾
                q.push(trie[u][i]);
            }
            else trie[u][i]=trie[fail[u]][i];
        }
    }
}

void query(char *s)
{
    int u=0;
    for(int i=1;s[i];i++)
    {
        int x=s[i]-'a';
        u=trie[u][x];
        for(int j=u;j;j=last[j])
        {
            cnt[id[j]]++;
        }
    }
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    while (cin>>n && n)
    {
        memset(trie,0,sizeof(trie));
        memset(fail,0,sizeof(fail));
        memset(last,0,sizeof(last));
        memset(cnt,0,sizeof(cnt));
        memset(id,0,sizeof(id));
        for(int i=1;i<=n;i++)
        {
            cin>>s[i]+1;
            insert(s[i],i);
        }
        build();
        cin>>t+1;
        query(t);
        int ans=0;
        for(int i=1;i<=n;i++)
        {
            ans=max(ans,cnt[i]);
        }
        cout<<ans<<endl;
        for(int i=1;i<=n;i++)
            if(ans==cnt[i]) cout<<s[i]+1<<endl;
    }
    
    return 0;
}
```

### 二次加强版

给你一个文本串 S 和 n 个模式串 T_{1..n}，请你分别求出每个模式串 T_i在 S 中出现的次数。

详细说明见OI杂笔记

```c++
//二次加强版
#include <iostream>
#include <cstdio>
#include <queue>
using namespace std;
const int N=2e5+1,M=2e6+10;
int n;
char s[N],t[M];
int trie[M/2][26],total,fail[M/2];
int id[M/2],node_[N]; //结点和字符串编号的映射
int delta[M/2],sum[M/2];//树上差分数组，差分求和

struct E
{
    int to,inext;
}edge[M/2];
int head[M/2],e_total;

void adde(int x,int y)
{
    edge[++e_total].to=y;
    edge[e_total].inext=head[x];
    head[x]=e_total;
}

void insert(char *s,int num)
{
    int u=0;
    for(int i=1;s[i];i++)
    {
        int x=s[i]-'a';
        if(!trie[u][x]) trie[u][x]=++total; //!!
        u=trie[u][x];
    }
    if(!id[u]) id[u]=num;
    node_[num]=u;
}

void build()
{
    queue<int> q;
    for(int i=0;i<26;i++)
    {
        if(trie[0][i]) q.push(trie[0][i]);
    }
    while (q.size())
    {
        int u=q.front();
        q.pop();
        for(int i=0;i<26;i++)
        {
            if(trie[u][i])
            {
                fail[trie[u][i]]=trie[fail[u]][i];
                q.push(trie[u][i]);
            }
            else
            {
                trie[u][i]=trie[fail[u]][i];
            }
        }
    }
    for(int i=1;i<=total;i++)
    {
        adde(fail[i],i);
    }
}

void traverse(char *s)
{
    int u=0;
    for(int i=1;s[i];i++)
    {
        int x=s[i]-'a';
        u=trie[u][x];
        delta[u]++;
    }
}

void dfs(int u=0)
{
    sum[u]=delta[u];
    for(int e=head[u];e;e=edge[e].inext)
    {
        int y=edge[e].to;
        dfs(y);
        sum[u]+=sum[y];
    }
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    cin>>n;
    for(int i=1;i<=n;i++)
    {
        cin>>s+1;
        insert(s,i);
    }
    build();
    cin>>t+1;
    traverse(t);
    dfs(0);
    for(int i=1;i<=n;i++)
    {
        cout<<sum[node_[i]]<<endl;
    }
    return 0;
}

```


# 动态规划

## ⭐几种形态的数位DP

### 形態1：記憶化搜索

```c++
    //CFED50 C classy_numbers
//題意：求解L~R內，十進制表示下不超過3個數是非0數的個數
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
const int N = 2e5 + 10;

//from left to right, the pos-th digit (start from 1)
int s[30], n;
void getdigit(ll R)
{
    if (R == 0)
    {
        n = 1;
        s[1] = 0;
        return;
    }
    n = log10(R) + 1;
    for (int i = n; i >= 1; i--)
    {
        s[i] = R % 10;
        R /= 10;
    }
}

//1~n
ll dp[21][4][2]; //pos,cnt,limit
ll dfs(int pos, int cnt, int limit)
{
    if (pos == n + 1)
    {
        return dp[pos][cnt][limit] = 1;
    }
    if (cnt == 3)
    {
        return dp[pos][cnt][limit] = 1;
    }

    if (dp[pos][cnt][limit] != -1)
        return dp[pos][cnt][limit];

    ll ans = 0;
    if (limit)
    {
        for (int d = 0; d <= s[pos]; d++)
        {
            ans += dfs(pos + 1, cnt + (d > 0), (d == s[pos]));
        }
    }
    else
    {
        for (int d = 0; d <= 9; d++)
        {
            ans += dfs(pos + 1, cnt + (d > 0), 0);
        }
    }
    return dp[pos][cnt][limit] = ans;
}

int T;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0), cout.tie(0);
    cin >> T;
    for (int _ = 1; _ <= T; _++)
    {
        ll L, R;
        cin >> L >> R;
        getdigit(R);
        memset(dp, -1, sizeof(dp));
        ll Rans = dfs(1, 0, 1);

        getdigit(L - 1);
        memset(dp, -1, sizeof(dp));
        ll Lans = dfs(1, 0, 1);
        cout << Rans - Lans << endl;
    }
    return 0;
}
```

### 形态2 试填法

不要62：区间内有多少个数，问当中有多少个数是带有连续62的
```c++
#include <iostream>
#include <memory.h>
#include <cmath>
using namespace std;

int f[10][10];

void init()
{
    memset(f,0,sizeof(f));
    for(int k=0;k<=9;k++)
    {
        if(k!=4) f[1][k]=1;
    }
    for(int i=2;i<=7;i++)
    {
        for(int j=0;j<=9;j++)
        {
            if(j!=4)
            {
                for(int k=0;k<=9;k++)
                {
                    if(j!=6 ||(j==6 && k!=2))
                    {
                        f[i][j]+=f[i-1][k];
                    }
                }
            }
        }
    }
}

int take_bit(int x,int i)
{
    if(log10(x)+1<i) return -10;//no exist
    for(int j=1;j<=i-1;j++) x/=10;
    return x%=10;
}

int fill(int x)
{
    if(x<=0) return 0;
    int ans=0;
    for(int i=log10(x);i>=1;i--)
    {
        for(int j=1;j<=9;j++) if(j!=4)ans+=f[i][j];
    }

    for(int i=log10(x)+1;i>=1;i--)
    {
        for(int j=((i==(int)(log10(x)+1))?1:0);(j<take_bit(x,i) ||(i==1 && j<=take_bit(x,i)));j++)
        {
            if(j==4) continue;
            if(take_bit(x,i+1)==6 && j==2) continue;
            if(i>=2)
            {
                for(int k=0;k<=9;k++)
                {
                    if(k==4) continue;
                    if(j==6 && k==2) continue;
                    ans+=f[i-1][k];
                }
            }
            else
            {
                ans++;
            }
            
        }
        if(take_bit(x,i)==4) break;
        if(take_bit(x,i+1)==6 && take_bit(x,i)==2) break;
    }
    return ans;
}

int main()
{
    int n,m;
    init();
    while(1)
    {
        cin>>n>>m;
        if(n==0 && m==0) break;
        cout<<fill(m)-fill(n-1)<<endl;
    }
    return 0;
}
```

魔鬼数
```c++
#include <iostream>
using namespace std;
long long f[30][4];//f[i][0|1|2]:not beastly number; f[i][3];is beastly number

void init()
{
    f[0][0]=1;
    for(int i=1;i<=20;i++)
    {
        f[i][0]=9*(f[i-1][0]+f[i-1][1]+f[i-1][2]);
        f[i][1]=f[i-1][0];
        f[i][2]=f[i-1][1];
        f[i][3]=10*f[i-1][3]+f[i-1][2];
    }
}

int main()
{
    init();
    int t;
    cin>>t;
    while(t--)
    {
        long long x,cnt=0,bit=1;
        cin>>x;
        while(f[bit][3]<x) bit++;//find out how many bit it have
        for(int i=bit,k=0;i>=1;i--)
        {
            for(int j=0;j<=9;j++)
            {
                int tempcnt=0;
                tempcnt+=f[i-1][3];
                if(j==6 && k>=0) tempcnt+=f[i-1][2];
                if(j==6 && k>=1) tempcnt+=f[i-1][1];
                if(j==6 && k>=2) tempcnt+=f[i-1][0];
                if(j!=6 && k>=3) tempcnt+=(f[i-1][2]+f[i-1][1]+f[i-1][0]);

                if(cnt+tempcnt<x) cnt+=tempcnt;
                else
                {
                    cout<<j;
                    if(k<3)
                    {
                        if(j==6) k++;
                        else k=0;
                    }
                    break;
                }
                
            }
        }
        cout<<endl;
    }
    return 0;
}

```

月之谜（同类分布）
```c++
#include <cstdio>
#include <cstring>
#include <cstdlib>
using namespace std;

// f[模][剩余数字数目][剩余数字的和][剩余位的模] = 合法方案数 
// f[S][i][j][k] = ∑(f[S][i - 1][j - R][(k - pwr[i - 1] * R) mod S], 0≤R≤9) 
// 边界条件 F[S][0][0][0] = 1 

int f[82][10][82][82], pwr[82][10];
inline int modabs (int a, int mod) { return ((a % mod) + mod) % mod; }
int num[10];
int count (int p, int sum, int mod, int s, bool e)
{
    if (s - sum < 0) return 0;
    else if (!e) return f[s][p + 1][s - sum][(s - mod) % s];
    else if (p == -1)
    {
        if (sum == s && mod == 0) return 1;
        else return 0;
    }
    else
    {
        int res = 0;
        for (int d = 0; d <= num[p]; d++)
            res += count(p - 1, sum + d, (mod + pwr[s][p] * d) % s, s, d == num[p]);
        return res;
    }
}
int fcount (int tt)
{
    if (tt == 0) return 0;
    int maxp = 0;
    while (tt) num[maxp++] = tt % 10, tt /= 10;
    int res = 0;
    for (int i = 1; i <= 81; i++) res += count(maxp - 1, 0, 0, i, true);
    return res;
}
int main ()
{
    freopen("mystery.in", "r", stdin);
    freopen("mystery.out", "w", stdout);
    for (int s = 1; s <= 81; s++)
    {
        memset(f[s], 0, sizeof f[s]);
        f[s][0][0][0] = 1;
        pwr[s][0] = 1 % s;
        for (int i = 1; i <= 9; i++) pwr[s][i] = (pwr[s][i - 1] * 10) % s;
        for (int i = 1; i <= 9; i++)
        {
            for (int j = 0; j <= i * 9; j++)
            {
                for (int k = 0; k <= s; k++)
                {
                    for (int d = 0; d <= 9 && j - d >= 0; d++)
                        f[s][i][j][k] += f[s][i - 1][j - d][modabs(k - pwr[s][i - 1] * d, s)];
                }
            }
        }
    }
    int l, r; while (scanf("%d %d", &l, &r) == 2) printf("%d\n", fcount(r) - fcount(l - 1));
    fclose(stdin);
    fclose(stdout);
    return 0;
}

```

## 悬线法

### K.[Largest Common Submatrix](https://nanti.jisuanke.com/t/42391)

(2019银川区域赛K题 by hqh)

题意:给两个nm(1000以内)的矩阵,问这两个矩阵的最大子矩阵大小,单个矩阵内元素两两不同,且都在1~nm内.

解法:先建立两个矩阵元素之间的位置映射(用个数组之类的保存). 然后使用悬线法(https://oi-wiki.org/misc/hoverline/),对于每个位置(i,j)我们假设这个位置有个竖直悬线(有高度和左右扩展最大距离等信息),我们最终的目标是让悬线高度等于这一列能扩展的最大高度的情况下,让悬线向左或者向右扩展的距离尽可能大.能扩展当且仅当a矩阵中对应的某个数x在b矩阵中对应的相对位置也符合要求.

例如矩阵

a=

5 6 1

7 9 3

2 4 8

b=

1 2 3

4 5 6

8 7 9

a中的7对应b中(3,2)位置,假设7这个位置的悬线初始高\左\右扩展最大位置分别是h=1\ l=1 \r=1;先更新高度,a中5在7的上面,b中也是如此,因此高度为2.此时不能再扩展了.左右最大距离的更新也类似,悬线的高度和左\右最大可达距离是可被线性更新的.

求出所有悬线后,直接用悬线的信息(r-l)*h作为矩形的最大面积更新即可


```c++
#include <iostream>
#include <cstdio>
#include <unordered_map>
#include <map>
using namespace std;

int n,m;
const int N=2010;
struct HOVER
{
    int h,l,r;
}hl[N][N];

//map<int,pair<int,int>> mp;
pair<int,int> mp[N*N];

int a[N][N],b[N][N];

void do_hoverline()
{
    for(int i=1;i<=n;i++)
    {
        //L
        for(int j=1;j<=m;j++)
        {
            //Height
            //pair<int,int> iijj=mp[b[i][j]];
            pair<int,int> iijj=mp[b[i][j]]; //b! （不是a

            if(b[i-1][j]==a[iijj.first-1][iijj.second])
            {
                hl[i][j].h=hl[i-1][j].h+1;
            }
            while (hl[i][j].l>1
             && iijj.second-(j-(hl[i][j].l-1))>=1
             && b[i][hl[i][j].l-1]==a[iijj.first][iijj.second-(j-(hl[i][j].l-1))] //注意悬线扩展时b也要匹配
             && hl[i][hl[i][j].l-1].h>=hl[i][j].h)
            {
                hl[i][j].l=hl[i][hl[i][j].l-1].l;
            }
        }
        //R
        for(int j=m;j>=1;j--)
        {
            pair<int,int> iijj=mp[b[i][j]]; //b!
            while (hl[i][j].r<m
             && iijj.second+((hl[i][j].r+1)-j)<=m
             && b[i][hl[i][j].r+1]==a[iijj.first][iijj.second+((hl[i][j].r+1)-j)] //注意悬线扩展时b也要匹配
             && hl[i][hl[i][j].r+1].h>=hl[i][j].h)
            {
                hl[i][j].r=hl[i][hl[i][j].r+1].r;
            }
        }
    }
}

int main()
{
    //ios::sync_with_stdio(false);
    //cin.tie(0),cout.tie(0);
    //cin>>n>>m;
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;i++)
    {
        for(int j=1;j<=m;j++)
        {
            //cin>>a[i][j];
            scanf("%d",&a[i][j]);

            mp[a[i][j]]={i,j};

            hl[i][j]={1,j,j};
        }
    }
    for(int i=1;i<=n;i++)
    {
        for(int j=1;j<=m;j++)
        {
            scanf("%d",&b[i][j]);
        }
    }
    do_hoverline();
    int ans=0;
    for(int i=1;i<=n;i++)
    {
        for(int j=1;j<=m;j++)
        {
            ans=max(ans,(hl[i][j].r-hl[i][j].l+1)*hl[i][j].h);
        }
    }
    //cout<<ans<<endl;
    printf("%d\n",ans);
    return 0;
}
```

### 最大子方阵

题意：
给定一个由0,1组成的n*n方阵（n在运行时提醒用户输入），判断其中由全1组成的最大子方阵的左上角位置和阶数。编写方法实现上述功能，返回值是一个包含3个元素的数组，依次表示行下标，列下标，阶数。

解法：
直接遍历每个方格并查找以这个方格为左上角顶点的最大子方阵，需要的时间复杂度为O\left(n^3\right)。事实上此问题可用悬线法动态规划将时间复杂度优化到O\left(n^2\right)。


假设每个方格对应一条从该方格开始到该方格能向上扩展到的最高位置为止的悬线，并将这个位置的悬线高度记下。悬线能向上扩展，当且仅当悬线顶部的上方方格是1。特别地，如果这个方格对应位置的值就是0，那么这个悬线的高度为0。


接下来考虑这个悬线向左或者向右平移，并考虑它扩展到的最远位置。悬线能向左或者向右扩展，当且仅当悬线目前能向左或向右能扩展到的最远位置的左边（或者右边）将被悬线覆盖时覆盖的所有方格都是1。例如，考虑下面的数据的最下面一行的方格对应的悬线：
$$
0110\\
1111\\
1111\\
$$
第3行第4列的方格对应的悬线高度为2（因为从这个位置开始向上能到达的最远非零格是第2行第4列的方格），能向左扩展到的最远位置是第1列。而第3行第3列的方格对应的悬线高度是3，向左能扩展到的最远位置是第2列（不能再向第一列扩展了，因为第3行第1列对应方格的高度只有2，比现在的悬线高度3要小）。如果能如此求出这些方格的悬线高度以及他们能向左或向右扩展的最大距离，就能求出以该悬线为高的最大矩形面积，取这些矩形的长宽中的最小值并计算出对应的正方形的面积以及左上角位置，就可以找到最大子方阵。

考虑如何按行更新每一列方格的悬线高度：如果上一行对应位置的悬线高度是h，若当前行对应方格是1则当前行的悬线高度就是h+1，否则将悬线高度清零。

考虑更新悬线的向左或者向右扩展的最远距离：以向左扩展为例，初始值就是当前列。若当前悬线能扩展到的最远距离的左侧一列的同一行的悬线高度比当前悬线高度更高，说明那个悬线向左能扩展到的地方，当前悬线也一定能扩展到，因此直接使用公式
$$l\left[i\right]=l\left[l\left[i\right]-1\right]$$
转移，其中l[i] 表示这一行第i列的悬线能向左扩展到的最远位置。如此反复迭代直到遇到悬线高度比当前高度更矮或到达矩阵尽头为止。向右能扩展到的最远距离同理。

程序在实现时，为每一行的所有列对应的悬线实现了一个Hoverline类，包含三个成员：h,l,r，分别表示悬线高度、悬线能向左和向右扩展到的最远位置。同时包含init、update_l、update_r以及get_ans方法，分别用于初始化悬线的高度和能扩展到的最远位置、更新向左和向右能扩展到的最远位置，以及计算悬线对应方阵的大小和左上角坐标。

```java
1.	import java.util.Scanner;
2.	import java.util.Arrays;
3.	
4.	class Hoverline{
5.	    public int[] h,l,r;
6.	    public int n;
7.	    Hoverline(int n){
8.	        this.n=n;
9.	        h=new int[n];
10.	        l=new int[n];
11.	        r=new int[n];
12.	    }
13.	    //用于初始化悬线的高度和能扩展到的最远位置
14.	    public void init(int[][] m,int line){
15.	        for(int i=0;i<n;i++){
16.	            h[i]=(m[line][i]==0)?0:(h[i]+1);
17.	            l[i]=r[i]=i;
18.	        }
19.	    }
20.	    //更新向左能扩展到的最远位置
21.	    public void update_l(int i){
22.	        while (l[i]-1>=0 && h[l[i]-1]>=h[i]){
23.	            l[i]=l[l[i]-1];
24.	        }
25.	    }
26.	    //更新向左能扩展到的最远位置
27.	    public void update_r(int i){
28.	        while (r[i]+1<n && h[r[i]+1]>=h[i]){
29.	            r[i]=r[r[i]+1];
30.	        }
31.	    }
32.	    //计算悬线对应方阵的大小和左上角坐标,同时更新答案
33.	    public void get_ans(int[] ans,int line){
34.	
35.	        for(int i=0;i<n;i++){
36.	            int minval=Math.min(r[i]-l[i]+1,h[i]);
37.	            int tmp_area=minval*minval;
38.	            if(tmp_area>ans[2]){
39.	                ans[0]=line-h[i]+1;
40.	                ans[1]=l[i];
41.	                ans[2]=tmp_area;
42.	            }
43.	        }
44.	    }
45.	}
46.	
47.	public class a {
48.	
49.	    public static int[] findLargestBlock(int[][] m){
50.	        int[] ans=new int[3];
51.	
52.	        int n=m.length;
53.	        Hoverline hoverline=new Hoverline(n);
54.	        //更新悬线
55.	        for(int i=0;i<n;i++){
56.	            hoverline.init(m,i);
57.	            for(int j=0;j<n;j++) {
58.	                hoverline.update_l(j);
59.	            }
60.	            for(int j=n-1;j>=0;j--){
61.	                hoverline.update_r(j);
62.	            }
63.	            hoverline.get_ans(ans,i);
64.	
65.	        }
66.	
67.	        return ans;
68.	    }
69.	
70.	//    输入矩阵
71.	    static int[][] read_matrix() {
72.	        int n;
73.	        Scanner sc=new Scanner(System.in);
74.	        System.out.println("Enter matrix size:");
75.	        n=sc.nextInt();
76.	        int[][] m=new int[n][n];
77.	        for(int i=0;i<n;i++){
78.	            for(int j=0;j<n;j++)
79.	                m[i][j]=sc.nextInt();
80.	        }
81.	        return m;
82.	    }
83.	
84.	    public static void main(String[] args) {
85.	
86.	        var m=read_matrix();
87.	        var ans=findLargestBlock(m);
88.	        System.out.println("row_index, column_index, size: "+Arrays.toString(ans)); //输出答案
89.	    }
90.	}

```

## 四边形不等式决策优化

### 小米赛背包题

题意：
n个物品有重量wi和价值vi，最大化价值。（wi在100以内，物品个数2e5，背包大小2e5，价值1e9）


![QQ图片20211114094959.png](https://i.loli.net/2021/11/14/Anl296y4oHZq1T3.png)

```c++

#include <iostream>
#include <cstdio>
#include <algorithm>
#include <memory.h>
using namespace std;
const int N=2e5+10; //!2
long long w[110][N],pre[N];//价值
long long dp[N],last[N];
int n,m;

struct P
{
    int k,l,r;
}queue[N];
int hd,rr;

long long s(int l)
{
    return pre[l];
}

bool is_better(int x,int k,int u,int i,int p) //p is better than k for x(p<=x)
{
    return last[u+p*i]+s(x-p) >= last[u+k*i] + s(x-k);
}

int bs(int u,int i,int p)
{
    int bs_l=queue[rr].l,bs_r=queue[rr].r;
    while (bs_l<bs_r)
    {
        int mid=(bs_l+bs_r)>>1;
        if(is_better(mid,queue[rr].k,u,i,p))
        {
            bs_r=mid;
        }
        else bs_l=mid+1;
    }
    if(bs_l==queue[rr].r && !is_better(bs_l,queue[rr].k,u,i,p)) bs_l++;
    return bs_l;
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    while (cin>>n>>m)
    {
        //memset(w,0,sizeof(w));
        for(int i=1;i<=100;i++) w[i][0]=0;
        for(int i=1;i<=n;i++)
        {
            int ww;
            cin>>ww;
            cin>>w[ww][++w[ww][0]];
        }
        for(int i=1;i<=m;i++)
            dp[i]=last[i]=0;

        for(int i=1;i<=100;i++)
        {
            if(w[i][0]==0)
            {
                for(int j=0;j<=m;j++)
                    last[j]=dp[j];
                continue;
            }
            sort(w[i]+1,w[i]+w[i][0]+1,greater<long long>());
            for(int j=1;j<=w[i][0];j++)
                pre[j]=pre[j-1]+w[i][j];
            for(int u=0;u<i;u++)
            {
                hd=1,rr=0; //reset queue
                int pmax=(m-u)/i;
                queue[++rr]={0,1,min(pmax,(int)(0+w[i][0]))}; //k=0 ,l=1, r=k+w[i][0]
                for(int p=1;p<=pmax;p++)
                {
                    while (hd<=rr && (queue[hd].r<p)) hd++; //time out
                    if(queue[hd].l<p) queue[hd].l=p;
                    //can transfer
                    if(hd<=rr && queue[hd].l<=p && p<=queue[hd].r)
                        dp[u+p*i]=max(dp[u+p*i],last[u+queue[hd].k*i]+s(p-queue[hd].k));
                    //try to insert new policy (from last[u+p*i]) (p<=queue[rr].l<=queue[rr].r)
                    while(hd<=rr && is_better(queue[rr].l,queue[rr].k,u,i,p)) rr--;
                    int pos;
                    if(hd<=rr) {pos=bs(u,i,p);queue[rr].r=pos-1;} //!1
                    else pos=1;
                    
                    queue[++rr]={p,pos,min(pmax,(int)(p+w[i][0]))};
                }
            }
            for(int j=0;j<=m;j++)
                last[j]=dp[j];
        }
        long long ans=0;
        for(int i=0;i<=m;i++)
            ans=max(ans,dp[i]);
        cout<<ans<<endl;
    }
    return 0;
}

```


# 数学

## 高斯消元

### luogu 4035 ball_space_generator

```c++
#include <iostream>
#include <iomanip>
#include <cmath>
using namespace std;
int n;
double c[101][102],a[101][102];

void gaussian_elimination()
{
    double sum1,sum2;
    sum1=sum2=0.0;
    for(int i=1;i<=n;i++)
    {
        sum1=sum2=0.0;
        for(int j=1;j<=n;j++)
        {
            sum1+=a[i][j]*a[i][j];
            sum2+=a[i+1][j]*a[i+1][j];
            c[i][j]=2*(a[i+1][j]-a[i][j]);
        }
        c[i][n+1]=sum2-sum1;
    }

    int pos=1;
    for(int i=1;i<=n;i++) //for per line
    {
        bool flag_of_find=false;
        for(int j=pos;j<=n;j++)
        {
            if(fabs(c[i][j])>1e-8) //c[j][i] ????????
            {
                flag_of_find=true;
                for(int k=1;k<=n+1;k++)
                {
                    swap(c[pos][k],c[i][k]);
                }
            }
        }
        if(!flag_of_find) continue;
        for(int j=1;j<=n;j++)
        {
            if(j==pos) continue;
            double rate=c[j][i]/c[pos][i];
            for(int k=1;k<=n+1;k++)
            {
                c[j][k]-=rate*c[pos][k];
            }
        }
        pos++;
    }
}

int main()
{
    ios::sync_with_stdio(false);
    cin>>n;
    for(int i=1;i<=n+1;i++)
    {
        for(int j=1;j<=n;j++)
            cin>>a[i][j];
    }
    gaussian_elimination();
    cout<<fixed;
    for(int i=1;i<=n;i++)
    {
        cout<<setprecision(3)<<c[i][n+1]/c[i][i]<<" ";
    }
    return 0;
}

```

### gaussian elimination

```c++
#include <iostream>
#include <cstdio>
#include <cmath>
using namespace std;
const int N=888;
double c[N][N];
int n;
int main()
{
    cin>>n;
    for(int i=1;i<=n;i++)
    {
        for(int j=1;j<=n+1;j++)
        {
            cin>>c[i][j];
        }
    }

    //int i=1;
    //ps:将pos改成i后，得到的矩阵将是对角线上全是1的矩阵
    for(int i=1;i<=n;i++)
    {
        //find a equation that the coefficient of the x_i is not 0
        bool flag_of_find=false;
        for(int j=i;j<=n;j++)
        {
            if(fabs(c[j][i])>1e-8)
            {
                flag_of_find=true;
                for(int k=1;k<=n+1;k++)
                {
                    swap(c[i][k],c[j][k]);
                }
            }
        }
        if(!flag_of_find) continue;
        for(int j=1;j<=n;j++)
        {
            if(i==j) continue;
            double rate=c[j][i]/c[i][i];
            for(int k=1;k<=n+1;k++)
            {
                c[j][k]-=c[i][k]*rate;
            }
        }
        //i++;
    }
    for(int i=1;i<=n;i++)
    {
        for(int j=1;j<=n+1;j++)
        {
            printf("%.8lf ",c[i][j]);
        }
        cout<<endl;
    }
    return 0;
}

```

### 2020 icpc shenyang A

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
const int N=310, Mod=998244353;

int n;
int a[N][N];
int b[N][N];
int c[N][N],d[N];
ll pow2[N];

void predo()
{
    pow2[0]=1;
    for(int i=1;i<=305;i++)
    {
        pow2[i]=pow2[i-1]*2;
        pow2[i]%=Mod;
    }
}

void gauss_elimination(int c[][N], int b[],int n)
{
    int pos=1;
    for(int i=1;i<=n;i++)// for every x
    {
        bool flag_of_find=false;
        for(int j=pos;j<=n;j++)
        {
            if(c[j][i])
            {
                flag_of_find=true;
                for(int k=1;k<=n;k++)
                    swap(c[pos][k],c[j][k]);
                swap(b[pos],b[j]);
            }
        }
        if(!flag_of_find) continue;
        for(int j=1;j<=n;j++)  //for every equation, execute elimination
        {
            if(j==pos) continue;
            if(c[j][i]==0) continue; //1->0: No need to eliminate
            for(int k=1;k<=n;k++)
            {
                c[j][k]^=c[pos][k];
            }
            b[j]^=b[pos];
        }
        pos++;
    }
}


void work()
{
    ll ans=1;
    for(int j=1;j<=n;j++)
    {
        for(int i=1;i<=n;i++)
        {
            for(int k=1;k<=n;k++)
            {
                c[i][k]=0;
                if(a[i][k])
                {
                    c[i][k]=1;
                }
            }

            if(b[i][j])
            {
                c[i][i]^=1;
            }
        }

        fill(d,d+n+1,0);
        gauss_elimination(c,d,n);

        int pos=1;
        int ans2=0;
        for(int i=1;i<=n;i++)
        {
            while(pos<=n && c[i][pos]==0)
            {
                pos++;
            }

            if(pos<=n) 
            {
                ans2++;
            }
        }

        ans2=n-ans2;
        ans=(ans*pow2[ans2])%Mod;
    }


    cout<<ans<<endl;
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    freopen("a_in.txt","r",stdin);
    cin>>n;
    predo();
    for(int i=1;i<=n;i++)
    {
        for(int j=1;j<=n;j++)
        {
            cin>>a[i][j];
        }
    }
    for(int i=1;i<=n;i++)
    {
        for(int j=1;j<=n;j++)
        {
            cin>>b[i][j];
        }
    }

    work();



    return 0;
}
```


## 线性基

可在对数时间内完成插入、查询集合最大值和最小异或和的操作。

完成重构操作后（花费对数平方的时间）可转换为形似于最简行阶梯型矩阵的形式，之后可在对数时间内查询集合内第k大异或和。

**最大值**

- （restructure前）从大到小枚举每个数异或进来会不会使得答案更大，如果会就异或进来。
- 如果需要在异或初值不为零的情况下取得最大值，只需要更改ans的初始值即可。

**最小值**：

- 直接贪心拿线性基中最小的数（当然，需要特判线性基是否可获得0）

| Funs \|\| Methods | Reliance    | Time Complexity | Inputs             | Outputs                                        | Description                                                              |
| ----------------- | ----------- | --------------- | ------------------ | ---------------------------------------------- | ------------------------------------------------------------------------ |
| LB                |             | 1               |                    |                                                | 构造函数                                                                 |
| insert            |             | log             | x                  | 返回是否插入成功，不成功则表明其可以被线性表出 | 将x插入线性基                                                            |
| get_max           |             | log             | ans=0              | 最大值                                         | 取得线性基能线性表出的最大值（重构前），若初始异或值不为0则向ans传递参数 |
| get_min           |             | log             |                    | 最小值                                         | 取得线性基中能表出的最小的值（重构前）                                   |
| restructure       |             | log^2           |                    | 修改p数组                                      | 对线性基做行变换使其成为类似于高斯消元的形式，以便能求出排名             |
| kth               | restructure | log             | k:第k小数的排名k   | 第k小数                                        | 求出第k小的能被表出的数（重构后）                                        |
| operator\|        |             | log^2           | n2：待合并的线性基 | ret：合并后的线性基                            | 合并线性基（重构前）                                                     |

```c++
//HDOJ 3949
#include <iostream>
#include <cstdio>
#include <cstring>
#include <memory.h>
#include <algorithm>
#include <map>
#include <vector>
#include <queue>
#include <cmath>
using namespace std;
typedef long long ll;
const int N=2e5+10;

namespace Linear_Basis
{
    struct LB //linear basis
    {
        static const int SZ=63;

        //local
        ll p[SZ]; //p：第i个p保存对应最高位为1的数
        bool flag; //flag:是否可异或得到0 (whether can do xor to obtain 0)
        int cnt; //cnt：重构后（高斯消元）的基大小
    
        //初始化
        LB()
        {
            //for(int i=0;i<SZ;i++)
            //p[i]=0;
            memset(p,0,sizeof(p)); //没什么太大性能开销
            cnt=flag=0;
        }

        //插入x到线性基中
        bool insert(ll x)
        {
            for(int j=SZ-1;j>=0;j--)
            {
                if(x>>j == 0 ) continue;
                if(p[j]==0)
                {
                    p[j]=x;
                    return true;
                }
                x^=p[j];
            }
            flag=true;
            return false;
        }

        //取得线性基能线性表出的最大值（重构前）
        ll get_max(ll ans=0) //ans:异或初值
        {
            for(int i=SZ-1;i>=0;i--)
            {
                if((ans^p[i])>ans) ans ^= p[i];
            }
            return ans;
        }

        //取得线性基中能表出的最小的值（重构前）
        ll get_min()
        {
            if(flag) return 0;
            for(int i=0;i<SZ;i++)
            {
                if(p[i]) return p[i];
            }
            return 0;
        }

        //对线性基做行变换使其成为类似于高斯消元的形式
        void restructure() 
        {
            for(int i=SZ-1;i>=0;i--)
            {
                if(p[i])
                {
                    for(int j=i-1;j>=0;j--)
                    {
                        if(p[i] & (1ll<<j))
                        {
                            p[i]^=p[j];
                        }
                    }
                }
            }

            for(int i=0;i<SZ;i++)
            {
                if(p[i])
                {
                    p[cnt++]=p[i];
                }
            }
        }

        //求出第k小的能被表出的数（重构后）
        ll kth(ll k) //before calling this func, must run restructure func once
        {
            //对k或k-1做二进制分解
            if(flag) k--;
            if(k==0) return 0;
            if(k>=(1ll << cnt)) return -1;
            ll ans=0;
            for(int i=0;i<cnt;i++)
            {
                if(k & (1ll<<i))
                {
                    ans^=p[i];
                }
            }
            return ans;
        }

        //合并线性基（重构前）
        LB operator|(const LB &n2) //合并线性基
        {
            LB ret= *this;
            for(int i=0;i<SZ;i++)
            {
                if(n2.p[i])
                {
                    ret.insert(n2.p[i]);
                }
            }

            ret.flag|=n2.flag; //?
            return ret;
        }
    };
}
using namespace Linear_Basis;




int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int T;
    cin>>T;
    for(int _=1;_<=T;_++)
    {
        int n;
        cin>>n;
        LB lb;
        for(int i=1;i<=n;i++)
        {
            ll x;
            cin>>x;
            lb.insert(x);
        }
        lb.restructure();
        cout<<"Case #"<<_<<":"<<endl;
        int q;
        cin>>q;
        for(int i=1;i<=q;i++)
        {
            ll k;
            cin>>k;
            cout<<lb.kth(k)<<endl;
        }
    }

    return 0;
}
```

## 线性求逆元

这个懒得整namespace了……

```c++
#include <iostream>
#include <cstdio>
#include <cmath>
using namespace std;

const int N=114514;
long long inv[N],p;

void linear_inv(int n)//in mod p
{
    inv[1]=1;
    //p=ki+j==0 (mod p) -> -kj^-1=i^-1
    //k=p/i, j=(p%i)^-1
    for(int i=2;i<=n;i++)
    {
        inv[i]=(long long)(p-p/i)*inv[p%i]%p; //(p-p/i)==(-p/i)
    }
    //if inv[i]=0, inverse not exists.
    //如果没有相应的逆元的时候，inv[i] 的值是未定义的
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    return 0;
}
```

## 线性求任意给定n个数的逆元

### luogu 5431 multi_inverse2

题意：给定n个数ai，求在mod p意义下的逆元。给定常数k，输出$\sum_{i=1}^n \frac{k^i}{a_i} \% p$

解法：

首先计算 $n$ 个数的前缀积，记为 $s_i$，然后使用快速幂或扩展欧几里得法计算 $s_n$ 的逆元，记为 $sv_n$。

因为 $sv_n$ 是 $n$ 个数的积的逆元，所以当我们把它乘上 $a_n$ 时，就会和 $a_n$ 的逆元抵消，于是就得到了 $a_1$ 到 $a_{n-1}$ 的积逆元，记为 $sv_{n-1}$。

同理我们可以依次计算出所有的 $sv_i$，于是 $a_i^{-1}$ 就可以用 $s_{i-1} \times sv_i$ 求得。

所以我们就在 $O(n + \log p)$ 的时间内计算出了 $n$ 个数的逆元。

```c++
#include <iostream>
#include <cstdio>
using namespace std;
const int N=5e6+10;
int n,p,k,kk=1,ans=0;
int a[N],s[N],sv[N],inv[N];
typedef long long ll;

namespace Fast_IO {
    
template <typename T> inline void read(T &x) {
  char c;
  int sgn = 1;
  x = 0;
  c = getchar();
  while (c > '9' || c < '0') {
    if (c == '-')
      sgn = -1;
    c = getchar();
  }
  while (c <= '9' && c >= '0') {
    x = (x << 3) + (x << 1) + (c - '0');
    c = getchar();
  }
  x *= sgn;
}

// template <typename T> inline void read(T &x){
// 	x=0;
//     char ch;
// 	for(ch=getchar();ch<'0'||ch>'9';ch=getchar());
// 	for(;ch>='0'&&ch<='9';ch=getchar())
// 		x=(x<<3)+(x<<1)+(ch&15); 
// }

template <typename T> inline void print(T x) {
  if (x < 0) {
    putchar('-');
    x = -x;
  }
  if (x > 9) {
    print(x / 10);
  }
  putchar(x % 10 + '0');
}
} // namespace Fast_IO
using namespace Fast_IO;

// //扩展欧几里得：ax+by=1
// ll exgcd(ll a,ll b,ll &x,ll &y)
// {
//     if(!b)
//     {
//         x=1,y=0;
//         return a;
//     }
//     ll d=exgcd(b,a%b,x,y),tempx=x,tempy=y;
//     x=tempy;
//     y=tempx-tempy*(a/b);
//     return d;
// }

//循环
int ksm(int a,int b,int Mod)
{
    int ans=1;
    while(b)
    {
        if(b&1) ans=(1ll*ans*a)%Mod;
        a=(1ll*a*a)%Mod;
        b>>=1;
    }
    return ans;
}

void getinv()
{
    // long long x,y;
    //exgcd(s[n],p,x,y); //get s[n]^-1 (mod p)
    //sv[n]=(x%p+p)%p; //sv[i]=s[i]^-1
    //exgcd(k,p,x,y);
    int k_inv=ksm(k,p-2,p);
    sv[n]=ksm(s[n],p-2,p);
    
    for(int i=n;i>=1;i--)
    {
        if(i<n) 
        {
            sv[i]=(1ll*sv[i+1]*a[i+1])%p;
        }
        inv[i]=(1ll*sv[i]*s[i-1])%p;
        // print(inv[i]);
        // putchar('\n');

        //custom
        ans=(ans+(1ll*kk*inv[i])%p)%p;
        kk=(1ll*kk*k_inv)%p;
    }
    print(ans);

}

int main()
{
    read(n),read(p),read(k);
    s[0]=1;
    for(int i=1;i<=n;i++)
    {
        // scanf("%d",&a[i]);
        read(a[i]);
        s[i]=(1ll*s[i-1]*a[i])%p;
        kk=(1ll*kk*k)%p;
    }
    getinv();
    return 0;
}
```



## 线性质数筛 与 基于筛的因数分解

| Funs \|\| Methods | Reliance     | Time Complexity | Inputs | Outputs                                                   | Description               |
| ----------------- | ------------ | --------------- | ------ | --------------------------------------------------------- | ------------------------- |
| linear_sieve      |              | n               | n      | prime,p,v                                                 | 筛出到n为止的质数         |
| 分解k的因数       | linear_sieve | <=sqrt          | k      | res：一个保存所有因数的vector（没排序）                   | 分解k的因数，存在vector中 |
| 分解x的质因数     | linear_sieve | log             | x      | res：保存pair的vector，pair第一个数是prime，第二个数是cnt | 分解x的质因数             |

```c++
//线性筛+分解因数
#include <vector>
#include <iostream>
using namespace std;

namespace L
{
    const int N=(int)1e7+10;

    //local
    int prime[N], p, v[N]; 
    //prime：质数集合
    //p:质数个数
    //v：下标i代表的数的最小质因子

    //筛出到n为止的质数
    void linear_sieve(int n)
    {
        for (int i = 2; i <= n; i++)
        {
            if (!v[i])
            {
                v[i] = i;
                prime[++p] = i;
            }
            for (int j = 1; j <= p && (long long)prime[j] * i <= n && v[i] >= prime[j]; j++)
            {
                v[i * prime[j]] = prime[j];
            }
        }
    }

    //分解k的因数(需要排序！)
    vector<long long> Div(long long k)
    {
        vector<long long> res;
        res.push_back(1);
        for (long long i = 1; prime[i] * prime[i] <= k; i++)
        {
            if (k % prime[i] == 0)
            {
                long long now = prime[i];
                long long len = res.size();
                while (k % prime[i] == 0)
                {
                    for (long long j = 0; j < len; j++)
                    {
                        res.push_back(res[j] * now);
                    }
                    now *= prime[i];
                    k /= prime[i];
                }
            }
        }
        if (k > 1)
        {
            long long len = res.size();
            for (long long j = 0; j < len; j++)
            {
                res.push_back(res[j] * k);
            }
        }
        return res;
    }

    //分解x的质因数
    vector<pair<int,int>> div2(int x) //分解质因数
    {
        vector<pair<int,int>> res;
        while(x>1)
        {
            int minprime=v[x];
            int cnt=0;
            while (x%minprime==0)
            {
                x/=minprime;
                cnt++;
            }
            res.push_back({minprime,cnt});
        }
        return res;
    }
}

using namespace L;

int main()
{
    linear_sieve(1e7);
    int n;
    cin >> n;
    vector<long long> s = Div(n);
    for (auto i : s)
        cout << i << " ";
    return 0;
}

```

## 欧拉函数Phi筛

### 16th 黑龙江省赛 f-function

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
typedef unsigned long long ll;

const int N=1e7+10;
int phi[N];
int pri[N];
ll vis[N];
int cnt=0;
void init()
{
	phi[1] = 1;
	for (int i = 2; i <= 10000000; ++i)
	{
		if (!vis[i])
		{
			phi[i] = i - 1;
			pri[cnt++] = i;
		}
		for (int j = 0; j < cnt; ++j)
		{
			if (1ll * i * pri[j] > 10000000) break;
			vis[i * pri[j]] = pri[j];
			if (i % pri[j])
			{
				phi[i * pri[j]] = phi[i] * (pri[j] - 1);
			}
			else
			{
				// i % pri[j] == 0
				// 换言之，i 之前被 pri[j] 筛过了
				// 由于 pri 里面质数是从小到大的，所以 i 乘上其他的质数的结果一定会被
				// pri[j] 的倍数筛掉，就不需要在这里先筛一次，所以这里直接 break
				// 掉就好了
				phi[i * pri[j]] = phi[i] * pri[j];
				break;
			}
		}
	}
}

ll cal(int xx)
{
	int t=0;
	int tx=vis[xx],txx=xx;
	for(;;)
	{
		if((xx/tx)*tx!=xx)
		{
			break;
		}
		t++;
		xx/=tx;
	}
	ll sum=0;
	sum=t%2==0?t/2:t/2+1;
	sum=txx/pow(tx,sum);
	return sum;
}

int main()
{
	ll ans=0;
	init();
	int n;
	cin>>n;
	for(int i=1; i<=n; i++)
	{
		if(vis[i]==0)
		{
			//cout<<"t"<<1<<endl;
			ans+=1;
			continue;
		}
		else
		{
			auto tmp=cal(i);
			//cout<<"t"<<tmp<<endl;
			ans+=tmp;
		}
	}
	cout<<ans<<endl;
	return 0;
}


```

## 莫比乌斯函数

```c++
namespace Mo
{
    const int NN=1e7+10;
    int mu[NN],v[NN],prime[NN],prime_tot;

    //筛出质数与莫比乌斯函数的值
    void linear_sieve_mo(int n)
    {
        prime_tot=0;
        mu[1]=1;
        for(int i=2;i<=n;i++)
        {
            if(!v[i]) 
            {
                v[i]=i;
                prime[++prime_tot]=i;
                mu[i]=-1;
            }
            for(int j=1;j<=prime_tot && 1ll*i*prime[j]<=n;j++)
            {
                v[i*prime[j]]=prime[j];

                if(i%prime[j]==0)
                {
                    mu[i*prime[j]]=0;
                    break;
                }

                mu[i*prime[j]] = -mu[i];
            }
        }
    }
}
using namespace Mo;
```



## 数论分块

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

namespace Shulunfenkuai
{
    inline ll ceil(ll n,ll m)
    {
        return n/m+(n%m!=0);
    }

    //向上取整求和
    ll calculate1(ll k,ll n)
    {
        ll ans=0;
        for(ll x=1,gx;x<=n;x=gx+1)
        {
            gx= (k/x) ? min(k/(k/x) , n) : n;
            ans+= k/x;
        }
        return ans;
    }

    //向下取整求和 : k/m <= i <= (k-1)/(m-1) , (m=ceil(k/i))
    //https://www.cnblogs.com/zjjws/p/13393858.html
    ll calculate2(ll k,ll n)
    {
        ll ans=0;
        for(ll x=1,gx;x<=n;x=gx+1)
        {
            ll v=ceil(n,x);
            gx= (v!=1) ? min((k-1)/(v-1) , n) : (n);

            ans+=v;
        }
        return ans;
    }
}
using namespace Shulunfenkuai;


int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    return 0;
}
```


## 快速幂

```c++
const long long Mod=10;

//递归
long long ksm(long long a,long long b)
{
    if(b==0) return 1;
    if(b==1) return a%Mod;
    long long ans=ksm(a,b>>1);
    ans*=ans;
    ans%=Mod;
    if(b&1)
    {
        ans*=a;
    }
    return ans%Mod;
}

//循环
long long ksm(long long a,long long b,long long Mod)
{
    long long ans=1;
    while(b)
    {
        if(b&1) ans=(ans*a)%Mod;
        a=(a*a)%Mod;
        b>>=1;
    }
    return ans;
}

//快速乘法（用于解决乘法爆longlong）
long long mul(long long a,long long b,long long Mod)
{
    long long ans=a;
    while (b)
    {
        if(b&1) ans=(ans+a)%Mod;
        a=(a+a)%Mod;
        b>>=1;
    }
    return ans;
}
```



## gcd与exgcd、线性同余方程

| Funs \|\| Methods     | Reliance | Time Complexity | Inputs  | Outputs                        | Description                                             |
| --------------------- | -------- | --------------- | ------- | ------------------------------ | ------------------------------------------------------- |
| gcd                   |          | log             | a,b     | gcd(a,b)                       | gcd                                                     |
| exgcd                 |          | log             | a,b,x,y | 返回gcd(a,b)，修改x、y作为结果 | exgcd：ax+by=1                                          |
| get_inv               |          | log             | a,p     | x：a^-1                        | 求出a关于p的逆元。但要注意gcd(a,p)==1时逆元才存在       |
| solve_linear_equation |          | log             | a,b,p   | 一个解x                        | 求出方程ax=b(mod p) 的解。但要注意有解当且仅当 gcd(a,p) |

```c++


namespace EXGCD
{
    typedef long long ll;

    //普通的gcd
    ll gcd(ll a,ll b)
    {
        return b?gcd(b,a%b):a;
    }

    //扩展欧几里得：ax+by=1
    ll exgcd(ll a,ll b,ll &x,ll &y)
    {
        if(!b)
        {
            x=1,y=0;
            return a;
        }
        ll d=exgcd(b,a%b,x,y),tempx=x,tempy=y;
        x=tempy;
        y=tempx-tempy*(a/b);
        return d;
    }
    
    //求a关于p的逆元（求同余方程ax=1(mod p)-> p|(ax-1) -> ax+py=1）
    ll get_inv(ll a,ll p)
    {
        ll x,y;
        ll g = exgcd(a, p, x, y);
        //x就是解，但是要注意如果希望得到正整数解应该对x取模
        return x=(x%p+p)%p;
    }

    //求出方程ax=b(mod p) 的解。但要注意有解当且仅当 gcd(a,p)|b
    ll solve_linear_equation(ll a,ll b,ll p)
    {
        //ax=b(mod p) -> ax+py=b
        //有解当且仅当 gcd(a,p)|b，求出ax0+py0解后x0乘以b/gcd(a,m)即可得原方程的一个解
        ll x,y;
        ll g=exgcd(a,p,x,y);

        if(b%g!=0) return -1;

        x*=(b/g); //注意：需要g|b，否则无解
        x=(x%p+p)%p;
        return x;
    }

}

using namespace EXGCD;

```


## 中国剩余定理

```c++
#include <iostream> //Chinese remainder theorem
using namespace std;
int congruence_equation_number;
long long m[20],a[20],t[20],M=1;

long long exgcd(long long a,long long b,long long &x,long long &y)
{
    if(b==0)
    {
        x=1;
        y=0;
        return a;
    }
    long long d=exgcd(b,a%b,x,y),temp_x=x,temp_y=y;
    x=temp_y;
    y=temp_x-temp_y*(a/b);
    return d;
}

int main()
{
    ios::sync_with_stdio(false);
    cin>>congruence_equation_number;
    for(int i=1;i<=congruence_equation_number;i++)
    {
        cin>>m[i]>>a[i];
        M*=m[i];
    }
    for(int i=1;i<=congruence_equation_number;i++)
    {
        long long x,y;
        exgcd(M/m[i],m[i],x,y);
        t[i]=x;
    }
    long long ans=0;
    for(int i=1;i<=congruence_equation_number;i++)
    {
        ans+=(a[i]*M/m[i]*t[i])%M;
        ans%=M;
    }
    cout<<(ans+M)%M<<endl;
    return 0;
}

```

## 扩展欧几里得求整数点个数

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
typedef long double ld;



namespace EXGCD
{
    typedef long long ll;

    //普通的gcd
    ll gcd(ll a,ll b)
    {
        return b?gcd(b,a%b):a;
    }

    //扩展欧几里得：ax+by=1
    ll exgcd(ll a,ll b,ll &x,ll &y)
    {
        if(!b)
        {
            x=1,y=0;
            return a;
        }
        ll d=exgcd(b,a%b,x,y),tempx=x,tempy=y;
        x=tempy;
        y=tempx-tempy*(a/b);
        return d;
    }
    
    //求a关于p的逆元（求同余方程ax=1(mod p)-> p|(ax-1) -> ax+py=1）
    ll get_inv(ll a,ll p)
    {
        ll x,y;
        ll g = exgcd(a, p, x, y);
        //x就是解，但是要注意如果希望得到正整数解应该对x取模
        return x=(x%p+p)%p;
    }

    //求出方程ax=b(mod p) 的解。但要注意有解当且仅当 gcd(a,p)|b
    ll solve_linear_equation(ll a,ll b,ll p)
    {
        //ax=b(mod p) -> ax+py=b
        //有解当且仅当 gcd(a,p)|b，求出ax0+py0解后x0乘以b/gcd(a,m)即可得原方程的一个解
        ll x,y;
        ll g=exgcd(a,p,x,y);

        x*=(b/g); //注意：需要g|b，否则无解
        x=(x%p+p)%p;
        return x;
    }

}
using namespace EXGCD;

namespace Zhengshu_points
{
    //(ai+b)/c=k (k>0,0<=i<=n)
    // a,b,c>0, n>=0
    ll get_zhengshu_points(ll a, ll b, ll c,ll n)
    {
        ll i0,k0;
        ll g=exgcd(a,-c,i0,k0);

        if(-b%g!=0) return 0; //no solution

        // ll Kr=-b*i0
        double Krd=(-b*i0)*(ld)1.0/c;
        double Kld=(n*g+b*i0)*(ld)1.0/(-c);

        if(g<0)
        {
            swap(Kld,Krd);
        }
        ll Kr= floor(Krd);
        ll Kl= ceil(Kld);
        return max(0ll, Kr-Kl+1);
    }
}
using namespace Zhengshu_points;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    ll a,b,c,n;
    cin>>a>>b>>c>>n;

    cout<<get_zhengshu_points(a,b,c,n)<<endl;

    return 0;
}

```

## 类欧几里得算法

### 202 ICPC Shenyang

原题可转化为计算
$$
f(a,b,c,n)=\sum_{i=0}^n \lfloor\frac{ai+b}{c}\rfloor
$$

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
typedef long double ld;
typedef __int128 vl;

ll H,M,A;

namespace Like_Euclid
{
    //
    vl ff(vl a,vl b,vl c,vl n)
    {
        if(a==0) return b/c*(n+1);
        if(a>=c || b>=c)
        {
            return ff(a%c, b%c, c, n) + (a/c)*n*(n+1)/2 + (b/c)*(n+1);
        }
        vl m=(a*n+b)/c;
        return n*m - ff(c,c-b-1, a, m-1);
    }
}
using namespace Like_Euclid;

namespace EXGCD
{
    //扩展欧几里得：ax+by=1
    vl exgcd(vl a,vl b,vl &x,vl &y)
    {
        if(!b)
        {
            x=1,y=0;
            return a;
        }
        vl d=exgcd(b,a%b,x,y),tempx=x,tempy=y;
        x=tempy;
        y=tempx-tempy*(a/b);
        return d;
    }

}
using namespace EXGCD;

namespace Zhengshu_points
{
    template<typename Ty>
    Ty fl(Ty a,Ty b)
    {
        if((a<0 && b>0) || (a>0 && b<0))
        {
            return (a/b) - (a%b !=0);
        }
        return a/b;
    }


    template<typename Ty>
    Ty ce(Ty a,Ty b)
    {
        if((a<0 && b>0) || (a>0 && b<0))
        {
            return (a/b);
        }
        return (a/b) + (a%b !=0);
    }



    //(ai+b)/c=k (k>0,0<=i<=n)
    // a,b,c>0, n>=0
    vl get_zhengshu_points(vl a, vl b, vl c,vl n)
    {
        vl i0,k0;
        vl g=exgcd(a,-c,i0,k0);

        if(-b%g!=0) return 0; //no solution
        
        // ld Krd=(-b*i0)*(ld)1.0/c;
        // ld Kld=(n*g+b*i0)*(ld)1.0/(-c);

        // if(g<0)
        // {
        //     swap(Kld,Krd);
        // }
        // vl Kr= floor(Krd);
        // vl Kl= ceil(Kld);
        
        
        vl Kr,Kl;
        if(g<0)
        {
            // Kl=(-b*i0)/c + ((-b*i0)%c != 0);
            Kl=ce(-b*i0,c);
            // Kr=(n*g+b*i0)/(-c);
            Kr=fl(n*g+b*i0,-c);
        }
        else
        {
            // Kl=(n*g+b*i0)/(-c) + ((n*g+b*i0)%(-c) != 0);
            Kl=ce(n*g+b*i0, -c);
            // Kr=(-b*i0)/c;
            Kr=fl(-b*i0,c);
        }

        return max((vl)0, Kr-Kl+1);
    }
}
using namespace Zhengshu_points;

ll solve(ll H, ll M, ll A)
{
    //a=MH,b=A,c=H-1
    vl ans=ff(M*H,A,H-1,H-2);

    // ld zero_point=A/(M*H);
    vl z1=A/(M*H);
    vl z2=z1+1;

    z1=min(z1,(vl)(H-2));
    vl bb=M*H*z1-A;
    // bb=-bb; bb<=0
    ans+=ff(M*H,-bb,H-1,z1);


    if(z2<=H-2)
    {
        vl bb2=M*H*z2-A;
        ans-=ff(M*H,bb2,H-1,H-2-z2);
        ans+=get_zhengshu_points(M*H,bb2,H-1,H-2-z2);
    }

    // cout<<"ans:"<<(ll)ans<<endl;
    // cout<<"z1:"<<(ll)z1<<endl;
    // cout<<"a2mh:"<<(ll)(A*2!=M*H)<<endl;
    
    // return ans+z1+(A*2!=M*H);
    return ans+z1+1;
}


int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    cin>>H>>M>>A;

    if(H*M==A*2)
    {
        cout<<H*M<<endl;
    }
    // else if(A==0)
    // {
    //     cout<<1<<endl;
    // }
    else
    cout<<solve(H,M,A)<<endl;

    return 0;
}

```


## 米勒罗宾质数判别法 与 Pollard Rho 因数分解法

说起来这玩意会用到mt19937这玩意……

```c++
//pollard Rho & Miller Rabin LUOGU 4718
#include <iostream>
#include <cstdio>
#include <ctime>
#include <map>
#include <random>
using namespace std;
typedef long long ll;
typedef __int128_t lll;

int T;
ll n;

namespace PR
{
    //local
    std::mt19937 rd(time(0));
    ll __a[]={2,325,9375,28178,450775,9780504,1795265022}; //b[]={2,7,61}; (for 2^32)

    ll gcd(ll a,ll b)
    {
        return b==0?a:gcd(b,a%b);
    }

    ll ksm(ll a,ll b,ll Mod) //注意：使用lll防炸
    {
        ll ans=1;
        while(b)
        {
            if(b&1) ans=((lll)ans*a)%Mod;
            a=((lll)a*a)%Mod;
            b>>=1;
        }
        return ans;
    }

    //质数判别
    bool miller_rabin(ll n)
    {
        if(n==2 || n==3) return true;
        if(n<2 || n%2==0) return false; // n%2==0 spj for even num
        ll d=n-1,r=0;
        while (d%2==0)
        {
            r++;
            d/=2;
        }
        for(int a_p=0;a_p<7;a_p++)
        {
            ll aa=__a[a_p];
            ll x=ksm(aa,d,n); //maybe 0, need to special judge x=0
            if(x<=1 || x==n-1) continue; //a^{d}=1 or -1
            for(int i=0;i<r-1;i++)
            {
                x=(lll)x*x%n;
                if(x==n-1) break; // find a^{2^r d}=-1 (mod n)
            }
            if(x!=n-1) return false; //!=
        }
        return true;
    }

    //计算x^2+c，会被下面的PR函数用到
    ll __f(ll x,ll c,ll Mod)
    {
        return ((lll)x*x+c)%Mod;
    }

    //求出n的一个因数
    ll Pollard_Rho(ll n) //n must be composite
    {
        ll s=0,t=0;
        ll c=(ll)rd()%(n-1)+1;
        ll val=1;
        for(ll goal=1;;goal<<=1,s=t,val=1)
        {
            for(ll step=1;step<=goal;step++)
            {
                t=__f(t,c,n);
                val=(lll)val*abs(t-s)%n;
                if(step%127==0)
                {
                    ll d=gcd(val,n);
                    if(d>1) return d;
                }
            }
            ll d=gcd(val,n);
            if(d>1) return d;
        }
    }

    //local
    ll maxfactor=0;
    //maxfactor：保存n的最大因数
    //分解n并求n的最大因数
    void do_div(ll n) //maximun prime factor of n
    {
        if(n<=maxfactor || n<2) return;
        if(miller_rabin(n))
        {
            maxfactor=max(maxfactor,n);
            return ;
        }
        ll m=n;
        while (m==n)
        {
            m=Pollard_Rho(n);
        }
        while (n%m==0)
        {
            n/=m;
        }
        do_div(n);
        do_div(m);
    }

    map<ll,ll> dv;
    //dv：用于保存因数分解结果
    //分解因数n，并将n的所有因数保存在dv中
    void do_div2(map<ll,ll> &dv,ll n)
    {
        if(miller_rabin(n))
        {
            dv[n]++;
            return ;
        }
        ll m=n;
        while (m==n)
        {
            m=Pollard_Rho(n);
        }
        n/=m;
        do_div2(dv,n);
        do_div2(dv,m);
    }
}
using namespace PR;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    cin>>T;
    while (T--)
    {
        maxfactor=0;
        cin>>n;
        do_div(n);
        if(maxfactor==n)
            cout<<"Prime"<<endl;
        else
            cout<<maxfactor<<endl;
    }
    
    return 0;
}
```



## BSGS（模意义下取对数）

复杂度是根号级别的，用于求解
$$
a^x\equiv b\pmod p 
$$


```c++
#include <iostream>
#include <unordered_map>
#include <cmath>
using namespace std;
typedef long long ll;


namespace BSGS
{
    //public
    long long a,b,p;
    //local
    unordered_map<long long,long long> ha;
    //ha:用于保存中途穷举的结果

    //a^x=b(mod p) 
    //a^(it-j)=b (mod p)
    //a^t^i=ba^j (mod p)
    long long ksm(long long a,long long b,long long m)
    {
        if(b==0) return 1;
        if(b==1) return a%m;
        long long ans=ksm(a,b/2,m);
        ans*=ans;
        ans%=m;
        if(b%2)
        {
            ans*=a;
            ans%=m;
        }
        return ans;
    }

    long long bsgs(long long a,long long b,long long p)
    {
        ha.clear();
        long long t=sqrt(p);
        if(t*t!=p) t++;
        for(int j=0;j<=t-1;j++)
        {
            long long val=(b%p*ksm(a,j,p))%p;
            ha[val]=j;
        }
        long long at=ksm(a,t,p);
        if(a==0) return b==0?1:-1;
        for(int i=0;i<=t;i++)
        {
            long long val=ksm(at,i,p);
            int j=(ha.find(val)==ha.end()?-1:ha[val]);
            if(j>=0 && i*t-j>=0)
            {
                return i*t-j;
            }
        }
        return -1;
    }
}
using namespace BSGS;

int main()
{
    ios::sync_with_stdio(false);
    cin>>a>>b>>p; //b<p
    cout<<bsgs(a,b,p)<<endl;
    return 0;
}

/*
69 5 19260817  
15013953
*/
```





## ⭐原根

### luogu 6091 原根模版

题意：给定整数 n，求它的所有原根。为了减小你的输出量，给出输出参数 d，设 n 的所有原根有 c 个，从小到大分别为 g1,..,gc, 只需要依次次输出g_d,g_2d,...,g_{(c/d)*d}



```c++
//P6091 原根模版
//有待优化（例如将欧拉函数改成线性筛形式、gcd改成ksm验证之类的）
#include <iostream>
#include <cstdio>
#include <cmath>
#include <memory.h>
#include <algorithm>
using namespace std;
const int N=1e6+5;
int T,n,d;
int prime_list[N],pp,m_phi;
int g_list[N],g_p;

int phi(int n)
{
    int ans=n;
    for(int i=2;i<=sqrt(n);i++)
    {
        if(n%i==0)
        {
            ans=ans/i*(i-1);
            while (n%i==0)
            {
                n/=i;
            }
        }
    }
    if(n>1) ans=ans/n*(n-1);
    return ans;
}

void divide(int n)
{
    pp=0;
    for(int i=2;i<=sqrt(n);i++)
    {
        if(n%i==0)
        {
            prime_list[++pp]=i;
            while (n%i)
            {
                n/=i;
            }
        }
    }
    if(n>1) prime_list[++pp]=n;
}

long long ksm(long long a,long long b,long long Mod)
{
    long long ans=1;
    while(b)
    {
        if(b&1) ans=(ans*a)%Mod;
        a=(a*a)%Mod;
        b>>=1;
    }
    return ans;
}

long long gcd(long long a,long long b)
{
    return b?gcd(b,a%b):a;
}

int get_min_primitive_root(int m)
{
    m_phi=phi(m);
    if(m==1) return 0;
    if(m==2) return 1;
    divide(m_phi);
    for(int g=2;g<=m-1;g++)
    {
        if(gcd(g,m)!=1) continue;
        bool flag=true;
        for(int i=1;i<=pp;i++)
        {
            if(ksm(g,m_phi/prime_list[i],m)==1)
            {
                flag=false;
                break;
            }
        }
        if(flag)
        {
            return g;
        }
    }
    return -1;
}

int main()
{
    //ios::sync_with_stdio(false);
    scanf("%d",&T);
    //cin>>T;
    while(T--)
    {
        //cin>>n>>d;
        scanf("%d%d",&n,&d);
        pp=g_p=0;
        int g=get_min_primitive_root(n);
        if(g==-1)
        {
            printf("0\n\n");
            //cout<<0<<endl<<endl;
            continue;
        }
        /*int m_phi_phi=phi(m_phi);
        //cout<<m_phi_phi<<endl;
        printf("%d\n",m_phi_phi);*/
        int m_phi_phi=0;
        for(int i=1;i<=m_phi;i++)
        {
            if(gcd(i,m_phi)==1)
            {
                m_phi_phi++;
                g_list[++g_p]=ksm(g,i,n);
            }
        }
        sort(g_list+1,g_list+1+g_p);
        printf("%d\n",m_phi_phi);//此处答案为phi(m_phi)
        for(int i=d;i<=m_phi_phi/d*d;i+=d)//输出控制，可以无视这部分
        {
            //cout<<g_list[i]<<" ";
            printf("%d ",g_list[i]);
        }
        printf("\n");
    }
    return 0;
}

```



## 求解二次剩余的Cipolla 算法

```c++
//Cipolla算法求解一个数的模意义下根号

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
#include <random>
#include <ctime>
using namespace std;
typedef long long ll;
const int N=2e5+10;

mt19937 rd(time(0));

int T;

namespace Cipolla
{
    //local
    ll w,p;
    //w: i^2 = a*a-n
    //p: 模数

    struct Imagenum
    {
        ll x,y;

        Imagenum(){}
        Imagenum(ll x,ll y): x(x), y(y) {}
        Imagenum operator *(const Imagenum &b) const
        {
            return Imagenum(
                ((x*b.x%p + y*b.y%p*w%p)%p+p)%p,
                ((x * b.y % p + y * b.x % p) % p + p) % p
            );
        }
    };

    //循环
    long long ksm(long long a,long long b,long long Mod)
    {
        long long ans=1;
        while(b)
        {
            if(b&1) ans=(ans*a)%Mod;
            a=(a*a)%Mod;
            b>>=1;
        }
        return ans;
    }

    Imagenum ksm(Imagenum a, ll b)
    {
        Imagenum ans(1,0);
        while(b)
        {
            if(b&1)
            {
                ans=ans*a;
            }
            a=a*a;
            b>>=1;
        }
        return ans;
    }

    //cipolla: 计算n在mod p意义下的平方根，注意p一定要是奇数质数
    ll cipolla(ll n,ll P)
    {
        p=P;
        n%=p;
        if(p==2) return n;
        if(ksm(n,(p-1)/2,p)==p-1) return -1; //勒让德符号=-1，表明不存在解
        if(n==0) return 0; //必要的特判，否则底下循环会卡住
        ll a;
        while(1)
        {
            a=rd()%p;
            w = ((a * a % p - n) % p + p) % p;
            if(ksm(w,(p-1)/2,p)==p-1) break;
        }

        Imagenum x(a,1); // (a+i)^((p+1)/2) == n^(1/2)
        return ksm(x,(p+1)/2).x; // 实部为答案，虚部一定为0
    }

}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    cin>>T;
    for(int _=1;_<=T;_++)
    {
        ll n,p;
        cin>>n>>p;
        auto ans=Cipolla::cipolla(n,p);
        if(ans==-1)
        {
            cout<<"Hola!\n";
        }
        else
        {
            auto ans2=(p-ans)%p;
            if(ans>ans2) swap(ans,ans2);
            if(ans==ans2) cout<<ans<<'\n';
            else cout<<ans<<" "<<ans2<<'\n';
        }
    }
    return 0;
}
```


## FFT

| Funs \|\| Methods | Reliance | Time Complexity | Inputs                                    | Outputs              | Description                                                                             |
| ----------------- | -------- | --------------- | ----------------------------------------- | -------------------- | --------------------------------------------------------------------------------------- |
| getlen1           |          | logn            | n                                         | len                  | 取得比n大的幂长len                                                                      |
| getlen2           |          | logn            | len1,len2                                 | len                  | 取得比len1\*2、len2\*2都大的幂长                                                        |
| fft               |          | nlogn           | 输入复数数组y、幂长len（2的幂）、ifft=1。 | 修改数组y作为结果。  | 得到由系数表示法转换为点值表示法（ifft=-1则反过来）的数组：ifft为1时做fft，-1则做ifft。 |
| multi             |          | n               | 输入复数数组y1、y2、y3，幂长len           | 修改数组y3作为结果。 | 对点值表示法的数组y1、y2做乘法保存至y3中。                                              |

```c++
//FFT
#include <iostream>
#include <cmath>
#include <complex>
#include <cstring>
#include <memory.h>
using namespace std;

namespace FFT
{
    const int N = 2e6 + 5; //N：长度上限
    const double PI = acos(-1.0); //PI

    //public
    complex<double> s1[N * 2], s2[N * 2], s3[N * 2]; //s1,s2,s3：输入系数表示法用的数组
    int sum[N*2]; //乘法完成后计算求和用的数组
    char a[N], b[N]; //a、b:输入的两个数的字符串形式

    //local
    int rev[N * 2]; //rev：🦋变换用数组

    //预处理🦋变换
    void __pre_rev(int len) 
    {
        rev[0] = 0;
        for (int i = 1; i <= len; i++)
        {
            rev[i] = rev[i >> 1] >> 1;
            if (i & 1)
            {
                rev[i] |= len >> 1;
            }
        }
    }

    // 🦋变换
    void __change(complex<double> y[], int len) 
    {
        __pre_rev(len);
        for (int i = 0; i < len; i++)
        {
            if (i < rev[i])
            {
                swap(y[i], y[rev[i]]);
            }
        }
    }

    //取得比n大的幂长len
    int getlen1(int n)
    {
        int len=1;
        while (len<n) {
            len<<=1;
        }
        return len;
    }

    //取得比len1\*2、len2\*2都大的幂长
    int getlen2(int len1,int len2)
    {
        int len = 1;
        while (len < len1 * 2 || len < len2 * 2)
            len <<= 1;                                //*2，不然进位会炸掉，并且🦋变换会错
        return len;
    }

    //得到由系数表示法转换为点值表示法（ifft=-1则反过来）的数组：输入复数数组y、长度len（2的幂）、ifft=1。修改数组y作为结果。ifft为1时做fft，-1则做ifft。
    void fft(complex<double> y[], int len, int ifft = 1) 
    {
        __change(y, len);
        for (int h = 2; h <= len; h <<= 1) // 合并的长度
        {
            complex<double> w_n(cos(2 * PI / h), sin(ifft * 2 * PI / h)); //对应长度的单位复根
            for (int j = 0; j < len; j += h)                              //起始位置
            {
                complex<double> w(1, 0);            //初始的w_n^k
                for (int k = j; k < j + h / 2; k++) // 遍历位置
                {
                    complex<double> u = y[k], v = w * y[k + h / 2]; //偶数项、奇数项
                    y[k] = u + v;
                    y[k + h / 2] = u - v;
                    w *= w_n;
                }
            }
        }
        if (ifft == -1)
        {
            for (int i = 0; i < len; i++)
            {
                y[i] /= len;
            }
        }
    }

    //对点值表示法的数组y1、y2做乘法保存至y3中。
    void multi(complex<double> y1[],complex<double> y2[],complex<double> y3[],int len) 
    {
        for(int i=0;i<len;i++)
        {
            y3[i]=y1[i]*y2[i];
        }
    }
}
using namespace FFT;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    cin >> a + 1 >> b + 1;
    int len1 = strlen(a + 1), len2 = strlen(b + 1);
    int len = getlen2(len1, len2); //*2，不然进位会炸掉，并且🦋变换会错
    //while (len < len1 * 2 || len < len2 * 2)
    //    len <<= 1;                                
    for (int i = 0, j = len1; i < len1; i++, j--) //注意倒着存
    {
        s1[i] = {(double)(a[j] - '0'), 0};
    }
    for (int i = 0, j = len2; i < len2; i++, j--)
    {
        s2[i] = {(double)(b[j] - '0'), 0};
    }
    for (int i = len1; i < len; i++)
        s1[i] = {0, 0};
    for (int i = len2; i < len; i++)
        s2[i] = {0, 0};
    fft(s1, len);
    fft(s2, len);
    for (int i = 0; i < len; i++) // 乘法……
        s3[i] = s1[i] * s2[i];
    fft(s3, len, -1);
    //len=len1+len2-1;
    //len=(len1+len2-1);
    sum[len] = 0; //最高进位复写为0
    for (int i = 0; i < len; i++)
        sum[i] = round(real(s3[i]));
    for (int i = 0; i < len; i++)
    {
        sum[i + 1] += sum[i] / 10;
        sum[i] %= 10;
    }
    while (sum[len] == 0 && len > 0)
    {
        len--;
    }
    for (int i = len; i >= 0; i--)
        cout << sum[i];
    cout << endl;
    return 0;
}
```



## NTT (998244353)

| Funs \|\| Methods | Reliance | Time Complexity | Inputs                                    | Outputs              | Description                                                                             |
| ----------------- | -------- | --------------- | ----------------------------------------- | -------------------- | --------------------------------------------------------------------------------------- |
| getlen1           |          | logn            | n                                         | len                  | 取得比n大的幂长len                                                                      |
| getlen2           |          | logn            | len1,len2                                 | len                  | 取得比len1\*2、len2\*2都大的幂长                                                        |
| ntt               |          | nlogn           | 输入复数数组y、幂长len（2的幂）、ifft=1。 | 修改数组y作为结果。  | 得到由系数表示法转换为点值表示法（ifft=-1则反过来）的数组：ifft为1时做fft，-1则做ifft。 |
| multi             |          | n               | 输入复数数组y1、y2、y3，幂长len           | 修改数组y3作为结果。 | 对点值表示法的数组y1、y2做乘法保存至y3中。                                              |

```c++
//FFT but use NTT

#include <iostream>
#include <cmath>
#include <complex>
#include <cstring>
#include <memory.h>
using namespace std;
typedef long long ll;

namespace NTT
{
    const int N = 2e6 + 5,Mod=998244353; //N：长度上限, Mod: 默认模数
    const double PI = acos(-1.0);//Pi
    const int primitive_root=3; //primitive-root: 3

    //public
    ll s1[N * 2], s2[N * 2], s3[N * 2]; //s1,s2,s3：输入系数表示法用的数组

    //local
    int rev[N * 2]; //rev：🦋变换用数组

    //快速幂
    long long __ksm(long long a,long long b,long long Mod)
    {
        long long ans=1;
        while(b)
        {
            if(b&1) ans=(ans*a)%Mod;
            a=(a*a)%Mod;
            b>>=1;
        }
        return ans;
    }

    //预处理🦋变换
    void __pre_rev(int len) 
    {
        rev[0] = 0;
        for (int i = 1; i <= len; i++)
        {
            rev[i] = rev[i >> 1] >> 1;
            if (i & 1)
            {
                rev[i] |= len >> 1;
            }
        }
    }

    // 🦋变换
    void __change(ll y[], int len) 
    {
        __pre_rev(len);
        for (int i = 0; i < len; i++)
        {
            if (i < rev[i])
            {
                swap(y[i], y[rev[i]]);
            }
        }
    }

    //取得比n大的幂长len
    int getlen1(int n)
    {
        int len=1;
        while (len<n) {
            len<<=1;
        }
        return len;
    }

    //取得比len1\*2、len2\*2都大的幂长
    int getlen2(int len1,int len2)
    {
        int len = 1;
        while (len < len1 * 2 || len < len2 * 2)
            len <<= 1;  //*2，不然进位会炸掉，并且🦋变换会错
        return len;
    }

    //得到由系数表示法转换为点值表示法（ifft=-1则反过来）的数组：ifft为1时做fft，-1则做ifft。
    void ntt(ll y[], int len, int ifft = 1) 
    {
        __change(y, len);
        for (int h = 2; h <= len; h <<= 1) // 合并的长度
        {
            //complex<double> w_n(cos(2 * PI / h), sin(ifft * 2 * PI / h)); //对应长度的单位复根
            ll gn;
            if(ifft==1) gn=__ksm(primitive_root,((ll)Mod-1)/h,Mod); //gn
            else gn=__ksm(primitive_root,((ll)Mod-1)/h*(h-1),Mod);  //gn^-1
            //else gn=ksm(332748118,(Mod-1)/h,Mod);
            for (int j = 0; j < len; j += h) //起始位置
            {
                //complex<double> w(1, 0);            //初始的w_n^k
                ll g=1;
                for (int k = j; k < j + h / 2; k++) // 遍历位置
                {
                    ll u = y[k], v = (g * y[k + h / 2]%Mod); //偶数项、奇数项
                    y[k] = (u + v+Mod)%Mod;
                    y[k + h / 2] = (u - v+Mod)%Mod;
                    //w *= w_n;
                    //debug
                    //cout<<k<<":"<<y[k]<<endl;
                    //cout<<k+h/2<<":"<<y[k+h/2]<<endl;

                    g=(g*gn)%Mod;
                }
            }
        }
        if (ifft == -1)
        {
            ll len_inv=__ksm(len,Mod-2,Mod);
            for (int i = 0; i < len; i++)
            {
                y[i] = y[i] * len_inv %Mod;
            }
        }
    }

    //对点值表示法的数组y1、y2做乘法保存至y3中。
    void multi(ll y1[],ll y2[],ll y3[],int len) 
    {
        for(int i=0;i<len;i++)
        {
            y3[i]=(y1[i]*y2[i]%Mod+Mod)%Mod;
        }
    }
}
using namespace NTT;

int n,m;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    cin>>n>>m;
    n++,m++;
    for(int i=0;i<n;i++)
    {
        cin>>s1[i];
    }
    for(int i=0;i<m;i++)
    {
        cin>>s2[i];
    }
    int len=getlen2(n,m);//*2，不然进位会炸掉，并且🦋变换会错
    ntt(s1,len);
    ntt(s2,len);
    multi(s1,s2,s3,len);
    ntt(s3,len,-1);

    for(int i=0;i<(n-1)+(m-1)+1;i++)
    {
        cout<<s3[i]<<" ";
    }


    return 0;
}
```

## FWT

用于解决与位运算有关的卷积问题：$\vec c=\vec a \oplus \vec b$
$$
C_i=\sum_{i=j|k}A_j B_k
$$
其中竖线表示或、与和异或三种运算。结果对Mod取模。

对或运算来说，构造：
$$
A'=FWT[A]:A'_i=\sum_{i=i|j}A_j
$$
也即FWT变换后A'_i表示二进制下的1是i的子集的Aj的总和。

| Funs \|\| Methods | Reliance | Time Complexity | Inputs                                                 | Outputs              | Description                                                                                                            |
| ----------------- | -------- | --------------- | ------------------------------------------------------ | -------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| fwt               |          | nlogn           | 输入复数数组y、长度len、三种运算控制符bitcal、ifft=1。 | 修改数组y作为结果。  | 作FWT变换（ifft=-1则反过来）的数组：ifft为1时做fft，-1则做ifft。bitcal为1、2、3时分别表示对or、and和xor三种运算做FWT。 |
| multi             |          | n               | 输入复数数组y1、y2、y3，幂长len                        | 修改数组y3作为结果。 | 对数组y1、y2做乘法（模数为Mod）保存至y3中。                                                                            |

```c++
//luogu 4717
#include <iostream>
#include <cstdio>
#include <cstring>
#include <memory.h>
#include <algorithm>
#include <map>
#include <vector>
#include <queue>
#include <cmath>
using namespace std;
typedef long long ll;


namespace FWT
{
    const int N=2e5+10; //N: 长度上限
    const int Mod=998244353,inv2=499122177; //Mod:模数 inv2: pow(2,998244353-2,998244353)

    //public
    ll s1[N*2],s2[N*2],s3[N*2]; //s1,s2,s3：输入系数表示法用的数组

    //bitcal: 1 or ;  2 and ; 3 xor
    void fwt(ll y[],int len,int bitcal,int ifft=1)
    {
        for(int h=2;h<=len;h<<=1)
        {
            for(int j=0;j<len;j+=h) //initiative position
            {
                for (int k = j; k < j + h / 2; k++) // traverse 
                {
                    ll u=y[k], v=y[k+h/2];
                    if(ifft==1)
                    {
                        if(bitcal==3) //xor （同或就对调+-符号，下同）
                        {
                            y[k]=(u+v)%Mod;
                            y[k+h/2]=(u-v+Mod)%Mod;
                        }
                        else if(bitcal==1) //or
                        {
                            //y[k]=u;
                            y[k+h/2]=(u+v)%Mod;
                        }
                        else if(bitcal==2)// and
                        {
                            y[k]=(u+v)%Mod;
                            //y[k+h/2]=(v);
                        }
                    }
                    else
                    {
                        if(bitcal==3) //xor
                        {
                            y[k]=(u+v)*inv2%Mod;
                            y[k+h/2]=((u-v)*inv2%Mod+Mod)%Mod;
                        }
                        else if(bitcal==1) //or
                        {
                            //y[k]=u;
                            y[k+h/2]=(v-u+Mod)%Mod;
                        }
                        else if(bitcal==2)// and
                        {
                            y[k]=(u-v+Mod)%Mod;
                            //y[k+h/2]=(v);
                        }
                    }
                }
            }
        }
    }

    //点值表示法多项式的乘法，输入y1,y2是点值表示法下的多项式
    void multi(ll y1[],ll y2[],ll y3[],int len) 
    {
        for(int i=0;i<len;i++)
        {
            y3[i]=(y1[i]*y2[i]%Mod+Mod)%Mod;
        }
    }
}
using namespace FWT;

ll A[N],B[N];
int n;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    cin>>n;
    n=(1<<n);
    for(int i=0;i<n;i++)
    {
        cin>>A[i];
    }
    for(int i=0;i<n;i++)
    {
        cin>>B[i];
    }
    int len=n;
    //while(len<n) len<<=1;
    //or
    memcpy(s1,A,sizeof(A));
    memcpy(s2,B,sizeof(B));
    fwt(s1,len,1);
    fwt(s2,len,1);
    multi(s1,s2,s3,len);
    fwt(s3,len,1,-1);
    for(int i=0;i<n;i++)
        cout<<s3[i]<<" ";
    cout<<endl;

    //and
    memcpy(s1,A,sizeof(A));
    memcpy(s2,B,sizeof(B));
    fwt(s1,len,2);
    fwt(s2,len,2);
    multi(s1,s2,s3,len);
    fwt(s3,len,2,-1);
    for(int i=0;i<n;i++)
        cout<<s3[i]<<" ";
    cout<<endl;

    //xor
    memcpy(s1,A,sizeof(A));
    memcpy(s2,B,sizeof(B));
    fwt(s1,len,3);
    fwt(s2,len,3);
    multi(s1,s2,s3,len);
    fwt(s3,len,3,-1);
    for(int i=0;i<n;i++)
        cout<<s3[i]<<" ";
    cout<<endl;

    return 0;
}
```



## 分治FFT

可以快速计算下标范围在0 to n-1的序列f：给定下标在1 to n-1 的序列g，计算
$$
f_i=\sum_{j=1}^if_{i-j}g_{j}
$$


```c++
//实际上要用NTT……

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

namespace NTT
{
    const int N = 2e5 + 5,Mod=998244353; //N：长度上限, Mod: 默认模数
    const double PI = acos(-1.0);//Pi
    const int primitive_root=3; //primitive-root: 3

    //public
    // ll s1[N * 2], s2[N * 2], s3[N * 2]; //s1,s2,s3：输入系数表示法用的数组

    //local
    int rev[N * 2]; //rev：🦋变换用数组

    //快速幂
    long long __ksm(long long a,long long b,long long Mod)
    {
        long long ans=1;
        while(b)
        {
            if(b&1) ans=(ans*a)%Mod;
            a=(a*a)%Mod;
            b>>=1;
        }
        return ans;
    }

    //预处理🦋变换
    void __pre_rev(int len) 
    {
        rev[0] = 0;
        for (int i = 1; i <= len; i++)
        {
            rev[i] = rev[i >> 1] >> 1;
            if (i & 1)
            {
                rev[i] |= len >> 1;
            }
        }
    }

    // 🦋变换
    void __change(ll y[], int len) 
    {
        __pre_rev(len);
        for (int i = 0; i < len; i++)
        {
            if (i < rev[i])
            {
                swap(y[i], y[rev[i]]);
            }
        }
    }

    //取得比n大的幂长len
    int getlen1(int n)
    {
        int len=1;
        while (len<n) {
            len<<=1;
        }
        return len;
    }

    //取得比len1\*2、len2\*2都大的幂长
    int getlen2(int len1,int len2)
    {
        int len = 1;
        while (len < len1 * 2 || len < len2 * 2)
            len <<= 1;  //*2，不然进位会炸掉，并且🦋变换会错
        return len;
    }

    //得到由系数表示法转换为点值表示法（ifft=-1则反过来）的数组：ifft为1时做fft，-1则做ifft。
    void ntt(ll y[], int len, int ifft = 1) 
    {
        __change(y, len);
        for (int h = 2; h <= len; h <<= 1) // 合并的长度
        {
            //complex<double> w_n(cos(2 * PI / h), sin(ifft * 2 * PI / h)); //对应长度的单位复根
            ll gn;
            if(ifft==1) gn=__ksm(primitive_root,((ll)Mod-1)/h,Mod); //gn
            else gn=__ksm(primitive_root,((ll)Mod-1)/h*(h-1),Mod);  //gn^-1
            //else gn=ksm(332748118,(Mod-1)/h,Mod);
            for (int j = 0; j < len; j += h) //起始位置
            {
                //complex<double> w(1, 0);            //初始的w_n^k
                ll g=1;
                for (int k = j; k < j + h / 2; k++) // 遍历位置
                {
                    ll u = y[k], v = (g * y[k + h / 2]%Mod); //偶数项、奇数项
                    y[k] = (u + v+Mod)%Mod;
                    y[k + h / 2] = (u - v+Mod)%Mod;
                    //w *= w_n;
                    //debug
                    //cout<<k<<":"<<y[k]<<endl;
                    //cout<<k+h/2<<":"<<y[k+h/2]<<endl;

                    g=(g*gn)%Mod;
                }
            }
        }
        if (ifft == -1)
        {
            ll len_inv=__ksm(len,Mod-2,Mod);
            for (int i = 0; i < len; i++)
            {
                y[i] = y[i] * len_inv %Mod;
            }
        }
    }

    //对点值表示法的数组y1、y2做乘法保存至y3中。
    void multi(ll y1[],ll y2[],ll y3[],int len) 
    {
        for(int i=0;i<len;i++)
        {
            y3[i]=(y1[i]*y2[i]%Mod+Mod)%Mod;
        }
    }
}
using namespace NTT;


namespace CDQ_FFT
{
    int n;
    ll ans[N]; 
    ll gg[N*2];
    ll g[N*2],f[N*2];
    //ans：答案序列f，求解solve(l,r)时，l~mid的答案序列f已经求出，而mid+1~r中仅仅保存了部分贡献
    //gg：输入的g序列
    //g、f:用于ntt计算用的序列，在做分治时要反复写入

    //对l~r范围内的序列做分治ntt
    //注意：不可调换子分治的调用顺序（提前调用会导致使用不完整的贡献值去计算新贡献）
    void solve(int l,int r)
    {
        if(l==r)
            return ;
        int mid=(l+r)>>1;

        solve(l,mid);//分治做l~mid范围内的ntt
        //接下来计算l~mid范围内的f(0~mid)对mid+1~r的f的贡献。

        int len=r-l+1;
        len=getlen1(len);

        //copy
        for(int i=0;i<len;i++) 
        {
            g[i]=gg[i];
        }
        int j=0;
        for(int i=l;i<=mid;i++,j++)
        {
            f[j]=ans[i];  //映射关系：l->0, l+1->1
        }
        for(int i=mid+1;i<=l+len-1;i++,j++)
        {
            f[j]=0;
        }

        ntt(g,len);
        ntt(f,len);
        multi(g,f,f,len);
        ntt(f,len,-1);

        for(int i=mid+1;i<=r;i++)
        {
            ans[i]=(ans[i]+f[i-l])%Mod; //这里套用上面的映射关系
        }

        solve(mid+1,r);//分治做mid+1~r范围内的ntt
    }
}
using namespace CDQ_FFT;


int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    cin>>n;
    for(int i=1;i<=n-1;i++)
    {
        cin>>gg[i];
    }
    ans[0]=1;
    solve(0,n-1);

    for(int i=0;i<=n-1;i++)
    {
        cout<<ans[i]<<" ";
    }



    return 0;
}
```

## ⭐拉格朗日插值

### luogu 4791 lagrange

```c++
// 拉格朗日插值
#include <iostream>
#include <cstring>
#include <memory.h>
#include <cmath>
using namespace std;

const int N=2e3+10;
const int Mod=998244353;
long long x[N],y[N],n,k;

long long ksm(long long a,long long b,long long Mod)
{
    long long ans=1;
    while(b)
    {
        if(b&1) ans=ans*a%Mod;
        a=(a*a)%Mod;
        b>>=1;
    }
    return ans;
}

long long lagrange()
{
    long long fz,fm,temp,ans=0;

    for(int i=1;i<=n;i++)
    {
        fz=fm=1;
        temp=y[i];
        for(int j=1;j<=n;j++)
        {
            if(j==i) continue;
            fz=(fz*(k-x[j]+Mod)%Mod)%Mod;
            fm=(fm*(x[i]-x[j]+Mod)%Mod)%Mod;
        }
        temp=(temp*fz)%Mod;
        temp=(temp*ksm(fm,Mod-2,Mod))%Mod;

        ans=(ans+temp)%Mod;
    }
    return ans;

}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);cout.tie(0);
    cin>>n>>k;
    for(int i=1;i<=n;i++)
    {
        cin>>x[i]>>y[i];
    }
    cout<<lagrange()<<endl;
    return 0;
}

```

## 规范组合数

```c++
long long C(long long y,long long x)
{
    if(y<0 || x<0 || y<x) return 0;//No sense
    y%=Mod; //x far below than Mod so that x%Mod==x
    if(y==0 && x==0) return 1;
    long long ans=1;
    for(long long j=y;j>=y-x+1;j--)
    {
        ans*=j;
        ans%=Mod;
    }
    ans*=f_inv[x];
    return ans%Mod;
}
```



# 数据结构

## ST表（一维）

```c++
template <typename T>
struct ST_table {
    static const int N=2e5+10,M=25;

    T ma[N][M],mi[N][M];
    int n,t;

    void init(T a[]) {
        t=log2(n)+1;
        for (int i = 1; i <= n; i++)
            ma[i][0] = ma[i][0] = make_pair(a[i], i);
        for (int k = 1; k < t; k++) {
            for (int i = 1; i <= n - (1 << k) + 1; i++) {
            ma[i][k] = max(ma[i][k - 1], ma[i + (1 << (k - 1))][k - 1]);
            mi[i][k] = min(mi[i][k - 1], mi[i + (1 << (k - 1))][k - 1]);
            }
        }
    }

    T ask_mi(int l, int r) {
        int k = (int)log2(r - l + 1);
        return min(mi[l][k], mi[r - (1 << k) + 1][k]);
    }

    T ask_ma(int l, int r) {
        int k = (int)log2(r - l + 1);
        return max(ma[l][k], ma[r - (1 << k) + 1][k]);
    }
};
```



## 线段树

同时支持加法、乘法操作（对p取模）的线段树。线段树的每个节点保存其所维护的区间大小。

| Funs \|\| Methods | Reliance   | Time Complexity | Inputs                                                           | Outputs | Description                                                              |
| ----------------- | ---------- | --------------- | ---------------------------------------------------------------- | ------- | ------------------------------------------------------------------------ |
| __tagdown         |            | 1               | u：树节点下标。ln:左子树对应区间长度。rn：右子树对应区间长度     |         | 标记下放：先更新乘法标记后更新加法标记，最后清空u的标记。                |
| build_tree        |            | nlogn           | u：树节点下标（初始调用时u=1）。l、r：节点管辖的左右端点         |         | 初始化点段树：更新每个节点的val值为孩子val值和，叶子节点的值被设为a[l]。 |
| asksum            | build_tree | logn            | u：树节点下标。L、R：询问区间。                                  | 区间和  | 返回区间L、R的和。                                                       |
| segupd            | build_tree | logn            | u：树节点下标（初始调用时u=1）。L、R：询问区间。addval：增加的值 |         | 区间L,R加法更新                                                          |
| segupd2           | build_tree | logn            | u：树节点下标（初始调用时u=1）。L、R：询问区间。addval：乘法的值 |         | 区间L,R乘法更新                                                          |

```c++
//Luogu 3373 Segment t 2 (下标保存版)
#include <iostream>
using namespace std;
typedef long long ll;

namespace Segment_tree
{
    const int MAXN=1e5+10; //MAXN：线段树大小上限

	//public
    ll n, m, a[MAXN], p; //n:长度， m：询问个数， a：初始数组个数， p：模数

    template<typename Ty, int MAXN = MAXN>
	struct SEGMENT_TREE
	{
		struct NODE
		{
			Ty val, tagplus, tagmulti; //val, tagplus, tagmulti：值，加法标记，乘法标记
			Ty l, r; //l、r：线段树节点所维护的区间
		}t[MAXN * 4];

		//标记下放：先更新乘法标记后更新加法标记，最后清空u的标记。
		//ln:左子树对应区间长度 rn：右子树对应区间长度
		void __tagdown(Ty u, Ty ln, Ty rn) 
		{	
			//按当前节点（u0）标记更新左右儿子的值
			t[u * 2].val = (t[u * 2].val * t[u].tagmulti + t[u].tagplus * ln) % p;
			t[u * 2 + 1].val = (t[u * 2 + 1].val * t[u].tagmulti + t[u].tagplus * rn) % p;
			//更新左右儿子乘法标记(先乘后加法)
			t[u * 2].tagmulti = (t[u * 2].tagmulti * t[u].tagmulti) % p;
			t[u * 2 + 1].tagmulti = (t[u * 2 + 1].tagmulti * t[u].tagmulti) % p;
			//更新左右儿子加法标记
			t[u * 2].tagplus = (t[u * 2].tagplus * t[u].tagmulti + t[u].tagplus) % p;
			t[u * 2 + 1].tagplus = (t[u * 2 + 1].tagplus * t[u].tagmulti + t[u].tagplus) % p;
			//重设标记
			t[u].tagplus = 0;
			t[u].tagmulti = 1;
		}

		//初始化点段树：更新每个节点的val值为孩子val值和，叶子节点的值被设为a[l]。
		void build_tree(Ty u, Ty l, Ty r)
		{
			t[u].tagplus = 0;
			t[u].tagmulti = 1;
			t[u].l = l;
			t[u].r = r;
			if (l == r)
			{
				t[u].val = a[l];
				return;
			}
			ll mid = (l + r) >> 1;
			build_tree(u * 2, l, mid);
			build_tree(u * 2 + 1, mid + 1, r);
			t[u].val = (t[u * 2].val + t[u * 2 + 1].val) % p;
		}

		Ty asksum(Ty u, Ty L, Ty R) //LR:询问 lr：节点
		{
			if (L <= t[u].l && t[u].r <= R)
				return t[u].val % p;
			Ty mid = (t[u].l + t[u].r) >> 1, ans = 0;
			__tagdown(u, mid - t[u].l + 1, t[u].r - mid);
			if (L <= mid)
			{
				ans += asksum(u * 2, L, R);
				ans %= p;
			}
			if (R > mid)
			{
				ans += asksum(u * 2 + 1, L, R);
				ans %= p;
			}
			return ans;
		}

		//加法更新
		void segupd(Ty u, Ty L, Ty R, Ty addval) 
		{
			if (L <= t[u].l && t[u].r <= R)
			{
				t[u].val = (t[u].val + addval * (t[u].r - t[u].l + 1)) % p;
				t[u].tagplus = (addval + t[u].tagplus) % p;
				return;
			}
			ll mid = (t[u].l + t[u].r) >> 1;
			__tagdown(u, mid - t[u].l + 1, t[u].r - mid);
			if (L <= mid)
				segupd(u * 2, L, R, addval);
			if (mid < R)
				segupd(u * 2 + 1, L, R, addval);
			t[u].val = (t[u * 2].val + t[u * 2 + 1].val) % p;
		}

		//乘法更新
		void segupd2(Ty u, Ty L, Ty R, Ty addval) 
		{
			if (L <= t[u].l && t[u].r <= R)
			{
				t[u].tagplus = (t[u].tagplus * addval) % p;
				t[u].tagmulti = (t[u].tagmulti * addval) % p;
				t[u].val = (t[u].val * addval) % p;
				return;
			}
			Ty mid = (t[u].l + t[u].r) >> 1;
			__tagdown(u, mid - t[u].l + 1, t[u].r - mid);
			if (L <= mid)
				segupd2(u * 2, L, R, addval);
			if (mid < R)
				segupd2(u * 2 + 1, L, R, addval);
			t[u].val = (t[u * 2].val + t[u * 2 + 1].val) % p;
		}
	};
} // namespace Segment_tree
using namespace Segment_tree;

SEGMENT_TREE<ll> tree;
int main()
{
	ios::sync_with_stdio(false);
	cin.tie(0);
	cout.tie(0);
	cin >> n >> m >> p;
	for (ll i = 1; i <= n; i++)
	{
		cin >> a[i];
	}
	tree.build_tree(1, 1, n);
	for (ll i = 1; i <= m; i++)
	{
		ll caozuo, l, r;
		cin >> caozuo >> l >> r;
		if (caozuo == 2)
		{
			ll k;
			cin >> k;
			tree.segupd(1, l, r, k);
		}
		else if (caozuo == 1)
		{
			ll k;
			cin >> k;
			tree.segupd2(1, l, r, k);
		}
		else
		{
			cout << tree.asksum(1, l, r) << endl;
		}
	}
	return 0;
}
```



## 可持久化权值线段树

静态区间第 k 小。

可持久化线段树维护的是权值(也即，数组索引代表值本身，数组对应的值表示这个值有多少个)。原数组的区间对应到可持久化线段树的应该是不同版本的树根。

- 调用前需要手动设定range_upb。

| Funs \|\| Methods | Reliance   | Time Complexity | Inputs                                                                                       | Outputs                                 | Description                                                                                             |
| ----------------- | ---------- | --------------- | -------------------------------------------------------------------------------------------- | --------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| build_tree        |            | NlogN           | l，r: 权值左右边界                                                                           |                                         | 初始化权值线段树                                                                                        |
| change            | build_tree | logN            | u：上一版本树根下标。l、r：权值左右边界。pos：修改权值的位置。val(=1)：修改权值大小          | 当前版本根下标                          | 在上一版本的基础上，给pos位置的个数+1                                                                   |
| ask               | build_tree | logN            | pa、pb：旧版本、新版本线段树的根。l、r：二分权值范围（初始为1和range_upb）。rnk：第rnk小的数 | 介于旧版本、新版本线段树之间的第rnk小数 | 返回介于旧版本、新版本线段树之间（对应数组区间L、R）的第rnk小数。如果离散化了则这个返回值应该表示索引。 |

```c++
#include <iostream>
#include <cstdio>
#include <map>
#include <algorithm>
#include <memory.h>
using namespace std;
typedef long long ll;

namespace Chairman_Tree {
    const int N=2e5+5;

    //public
    int n,m; //n:数的个数 m:询问个数 （其实这两个变量可以放到外面……）
    int range_upb;// range_upb:权值范围上限

    //local

    template<typename Ty>
    struct CHairman_Tree 
    {
        //public
        int root[N]; //root: 不同版本的线段树树根

        //local
        struct Node
        {
            int lc,rc; //lc、rc：左右孩子索引
            Ty cnt; //cnt: 权值和
        }t[20*N];
        int total; //total: 线段树节点的个数

        //初始化权值线段树
        int build_tree(int l,int r)
        {
            int p=++total;
            if(l==r)
            {
                t[p].cnt=0;
                return p;
            }
            int mid=(l+r)>>1;
            t[p].lc=build_tree(l,mid);
            t[p].rc=build_tree(mid+1,r);
            t[p].cnt=0;
            return p;
        }

        //在上一版本的基础上，给pos位置的个数+1
        int change(int p,int l,int r,int pos,int val=1)
        {
            int q=++total; // new node
            t[q]=t[p]; // copy
            if(l==r)
            {
                t[q].cnt+=val;
                return q;
            }
            int mid=(l+r)>>1;
            if(pos<=mid) t[q].lc=change(t[p].lc,l,mid,pos,val);
            else t[q].rc=change(t[p].rc,mid+1,r,pos,val);
            t[q].cnt=t[t[q].lc].cnt+t[t[q].rc].cnt;
            return q;
        }

        //返回介于旧版本、新版本线段树之间的第rnk小数。如果离散化了则这个返回值应该表示索引。
        int ask(int pa,int pb,int l,int r,int rnk)// 获得离散化后的值（对值域进行二分）
        {
            if(l==r)
            {
                return l;
            }
            int mid=(l+r)>>1;
            int cnt=t[t[pb].lc].cnt-t[t[pa].lc].cnt; // Count of [L,mid]
            if(cnt>=rnk) return ask(t[pa].lc,t[pb].lc,l,mid,rnk);
            else return ask(t[pa].rc,t[pb].rc,mid+1,r,rnk-cnt);
        }
    };
}
using namespace Chairman_Tree;

namespace Discretization {

    const int N=1e6+10;
    typedef int Ty;

    //public
    Ty a[N],b[N];
    int dlen;

    // 复制a到b
    void copy_to_b()
    {
        memcpy(b, a, sizeof(a));
    }

    // 对b排序、去重，返回去重后的数组长度
    int sort_and_unique(int n)
    {
        sort(b+1,b+1+n);
        return dlen=unique(b+1,b+1+n)-b-1;
    }

    // 返回离散化后的值（1开始计数）
    inline int get_id(Ty x)
    {
        return lower_bound(b+1,b+1+dlen,x)-b;
    }
}
//using namespace Discretization;


CHairman_Tree<int> t;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    cin>>n>>m;
    for(int i=1;i<=n;i++)
    {
        cin>>Discretization::a[i];
    }
    //离散化
    Discretization::copy_to_b();
    range_upb=Discretization::sort_and_unique(n);
    //建树
    t.build_tree(1,range_upb);
    //修改并建立历史版本
    for(int i=1;i<=n;i++)
    {
        int x=Discretization::get_id(Discretization::a[i]);
        t.root[i]=t.change(t.root[i-1], 1, range_upb, x);
    }
    //查询区间第k大：通过与历史版本作差实现权值二分
    for(int i=1;i<=m;i++)
    {
        int l,r,k;
        cin>>l>>r>>k;
        int index=t.ask(t.root[l-1],t.root[r],1,range_upb,k);
        cout<<Discretization::b[index]<<endl;
    }


    return 0;
}
```

## CDQ分治

```c++
    //Luogu P3810 【模板】三维偏序（陌上花开
//題意：求滿足aj<=ai, bj<=bi, cj<=ci (j!=i)的點對數量，對於d in [0,n)，求f(i)=d的數量
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
const int N = 4e5 + 10;

namespace BIT
{
    const int MAXN = 4e5 + 10;

    template <typename Ty>
    struct Bit
    {
        //local
        Ty t[MAXN];
        int __lowbit(int x)
        {
            return x & (-x);
        }

        void add(int pos, Ty val = 1)
        {
            while (pos < MAXN)
            {
                t[pos] += val;
                pos += __lowbit(pos);
            }
        }

        Ty ask(int pos)
        {
            Ty ans = 0;
            while (pos >= 1)
            {
                ans += t[pos];
                pos -= __lowbit(pos);
            }
            return ans;
        }
    };
}
using namespace BIT;

int n, k;
struct P
{
    int x, y, z;
    int id;
    int cnt;
} a[N], b[N];
int ap;

struct Q
{
    int y, z;
    int typ; //0:op , 1:query
    int id;
    int cnt;
} c[N];
int cp;

Bit<int> tree;

int cnt[N];
int ans[N];
int ans2[N];

int cmpx(const P &a, const P &b)
{
    if (a.x == b.x && a.y == b.y)
        return a.z < b.z;
    if (a.x == b.x)
        return a.y < b.y;
    return a.x < b.x;
}

int cmpq(const Q &a, const Q &b)
{
    if (a.y == b.y && a.z == b.z)
        return a.typ < b.typ;
    if (a.y == b.y)
        return a.z < b.z;
    return a.y < b.y;
}

void solve(int l, int r)
{
    if (l == r)
    {
        return;
    }
    int mid = (l + r) / 2;
    solve(l, mid);
    solve(mid + 1, r);
    cp = 0;
    for (int i = l; i <= mid; i++)
    {
        c[++cp] = {a[i].y, a[i].z, 0, a[i].id, a[i].cnt};
    }
    for (int i = mid + 1; i <= r; i++)
    {
        c[++cp] = {a[i].y, a[i].z, 1, a[i].id, a[i].cnt};
    }
    sort(c + 1, c + 1 + cp, cmpq);

    for (int i = 1; i <= cp; i++)
    {
        if (c[i].typ == 0) //op
        {
            tree.add(c[i].z, c[i].cnt);
        }
        else //query
        {
            ans[c[i].id] += tree.ask(c[i].z);
        }
    }

    //undo change of BIT
    for (int i = 1; i <= cp; i++)
    {
        if (c[i].typ == 0)
        {
            tree.add(c[i].z, -c[i].cnt);
        }
    }
}

void my_unique()
{
    ap = 0;
    for (int i = 1; i <= n; i++)
    {
        if (a[ap].x == b[i].x && a[ap].y == b[i].y && a[ap].z == b[i].z)
        {
            a[ap].cnt++;
        }
        else
        {
            a[++ap] = b[i];
        }
    }
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0), cout.tie(0);
    cin >> n >> k;
    for (int i = 1; i <= n; i++)
    {
        cin >> b[i].x;
        cin >> b[i].y;
        cin >> b[i].z;
        b[i].id = i;
        b[i].cnt = 1;
    }
    sort(b + 1, b + 1 + n, cmpx);
    my_unique();
    sort(a + 1, a + 1 + ap, cmpx);

    for (int i = 1; i <= ap; i++)
    {
        cnt[a[i].id] = a[i].cnt;
    }

    solve(1, ap);

    for (int i = 1; i <= n; i++)
    {
        ans[i] += cnt[i] - 1;
        if (ans[i] >= 0)
            ans2[ans[i]] += cnt[i];

        // cout<<ans[i]<<endl;
    }
    // cout<<endl;

    for (int i = 0; i < n; i++)
    {
        cout << ans2[i] << endl;
    }

    return 0;
}//Luogu P3810 【模板】三维偏序（陌上花开
//題意：求滿足aj<=ai, bj<=bi, cj<=ci (j!=i)的點對數量，對於d in [0,n)，求f(i)=d的數量
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
const int N = 4e5 + 10;

namespace BIT
{
    const int MAXN = 4e5 + 10;

    template <typename Ty>
    struct Bit
    {
        //local
        Ty t[MAXN];
        int __lowbit(int x)
        {
            return x & (-x);
        }

        void add(int pos, Ty val = 1)
        {
            while (pos < MAXN)
            {
                t[pos] += val;
                pos += __lowbit(pos);
            }
        }

        Ty ask(int pos)
        {
            Ty ans = 0;
            while (pos >= 1)
            {
                ans += t[pos];
                pos -= __lowbit(pos);
            }
            return ans;
        }
    };
}
using namespace BIT;

int n, k;
struct P
{
    int x, y, z;
    int id;
    int cnt;
} a[N], b[N];
int ap;

struct Q
{
    int y, z;
    int typ; //0:op , 1:query
    int id;
    int cnt;
} c[N];
int cp;

Bit<int> tree;

int cnt[N];
int ans[N];
int ans2[N];

int cmpx(const P &a, const P &b)
{
    if (a.x == b.x && a.y == b.y)
        return a.z < b.z;
    if (a.x == b.x)
        return a.y < b.y;
    return a.x < b.x;
}

int cmpq(const Q &a, const Q &b)
{
    if (a.y == b.y && a.z == b.z)
        return a.typ < b.typ;
    if (a.y == b.y)
        return a.z < b.z;
    return a.y < b.y;
}

void solve(int l, int r)
{
    if (l == r)
    {
        return;
    }
    int mid = (l + r) / 2;
    solve(l, mid);
    solve(mid + 1, r);
    cp = 0;
    for (int i = l; i <= mid; i++)
    {
        c[++cp] = {a[i].y, a[i].z, 0, a[i].id, a[i].cnt};
    }
    for (int i = mid + 1; i <= r; i++)
    {
        c[++cp] = {a[i].y, a[i].z, 1, a[i].id, a[i].cnt};
    }
    sort(c + 1, c + 1 + cp, cmpq);

    for (int i = 1; i <= cp; i++)
    {
        if (c[i].typ == 0) //op
        {
            tree.add(c[i].z, c[i].cnt);
        }
        else //query
        {
            ans[c[i].id] += tree.ask(c[i].z);
        }
    }

    //undo change of BIT
    for (int i = 1; i <= cp; i++)
    {
        if (c[i].typ == 0)
        {
            tree.add(c[i].z, -c[i].cnt);
        }
    }
}

void my_unique()
{
    ap = 0;
    for (int i = 1; i <= n; i++)
    {
        if (a[ap].x == b[i].x && a[ap].y == b[i].y && a[ap].z == b[i].z)
        {
            a[ap].cnt++;
        }
        else
        {
            a[++ap] = b[i];
        }
    }
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0), cout.tie(0);
    cin >> n >> k;
    for (int i = 1; i <= n; i++)
    {
        cin >> b[i].x;
        cin >> b[i].y;
        cin >> b[i].z;
        b[i].id = i;
        b[i].cnt = 1;
    }
    sort(b + 1, b + 1 + n, cmpx);
    my_unique();
    sort(a + 1, a + 1 + ap, cmpx);

    for (int i = 1; i <= ap; i++)
    {
        cnt[a[i].id] = a[i].cnt;
    }

    solve(1, ap);

    for (int i = 1; i <= n; i++)
    {
        ans[i] += cnt[i] - 1;
        if (ans[i] >= 0)
            ans2[ans[i]] += cnt[i];

        // cout<<ans[i]<<endl;
    }
    // cout<<endl;

    for (int i = 0; i < n; i++)
    {
        cout << ans2[i] << endl;
    }

    return 0;
}
```

## 树状数组(BIT)

| Funs \|\| Methods | Reliance | Time Complexity | Inputs                                 | Outputs     | Description          |
| ----------------- | -------- | --------------- | -------------------------------------- | ----------- | -------------------- |
| __lowbit          |          | 1               | x                                      | x&-x        | 初始化权值线段树     |
| add               |          | logn            | pos：添加的位置。val（=1）：添加的值。 |             | 将pos位置的值加上val |
| ask               |          | logn            | pos：查询的前缀位置                    | 返1~pos的和 | 返回1~pos的和        |

```c++
#include <iostream>
using namespace std;

namespace BIT {
    const int MAXN=4e5+10;

    template <typename Ty>
    struct Bit
    {
        //local
        Ty t[MAXN];
        int __lowbit(int x)
        {
            return x&(-x);
        }

        void add(int pos,Ty val=1)
        {
            while(pos<MAXN)
            {
                t[pos]+=val;
                pos+=__lowbit(pos);
            }
        }

        Ty ask(int pos)
        {
            Ty ans=0;
            while(pos>=1)
            {
                ans+=t[pos];
                pos-=__lowbit(pos);
            }
            return ans;
        }
    };
}
using namespace BIT;

```

## 并查集（按秩合并）

| Funs \|\| Methods | Reliance | Time Complexity | Inputs      | Outputs                                      | Description                                        |
| ----------------- | -------- | --------------- | ----------- | -------------------------------------------- | -------------------------------------------------- |
| init              |          | n               | n：元素个数 |                                              | 初始化并查集中的每个节点，令每个节点的父亲为其本身 |
| getfa             | init     | 1               | x：节点     | x的父亲                                      | 取得父亲并路径压缩                                 |
| merge             | init     | 1               | x、y：节点  | 合并后的父亲节点。若为-1表明xy已经在同一集合 | 合并x、y（按秩合并）                               |
| check             | init     | 1               | x、y：节点  | 是否已经在同一集合                           | 检查是否在同一集合                                 |

```c++
#include <algorithm>
#include <cmath>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <iostream>
#include <map>
#include <queue>
#include <set>
#include <string>
#include <unordered_map>
#include <unordered_set>
#include <vector>

using namespace std;
typedef long long ll;

namespace Union_find_set {
struct Unionfindset {
  static const int N = 3e5 + 10;
  // local
  int fa[N], sz[N]; //fa,sz：父亲和秩的大小

    //初始化并查集
  void init(int n) {
    for (int i = 1; i <= n; i++) {
      fa[i] = i;
      sz[i] = 1;
    }
  }

    //取得父亲并路径压缩
  int getfa(int x) {
    if (fa[x] == x)
      return x;
    return fa[x] = getfa(fa[x]);
  }

    //合并x、y
  int merge(int x, int y) {
    int x_fa = getfa(x), y_fa = getfa(y);
    if (x_fa == y_fa)
      return -1;
    if (sz[x_fa] > sz[y_fa]) {
      swap(x, y);
      swap(x_fa, y_fa);
    }
    fa[x_fa] = y_fa;
    sz[y_fa] += sz[x_fa];

    return y_fa;
  }

    //检查是否在同一集合
  bool check(int x, int y) { return getfa(x) == getfa(y); }
} ufs;
} // namespace Union_find_set

int main() {
  ios::sync_with_stdio(false);
  cin.tie(0), cout.tie(0);
  return 0;
}
```

## KDTree（二维）

- ⚠️：使用前需要先处理树中的s数组和变量n



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

namespace KDTREE
{
    typedef ll Ty;
    const int N=2e5+10;

    //平方
    template<typename T>
    T sqr(T x)
    {
        return x*x;
    }

    struct Node
    {
        Ty x,y;
        Ty L,R,U,D;
        int lc,rc;
        int d;
        //x,y:点的坐标
        //LRUD：点所管辖的子树的最小矩形覆盖
        //lc，rc：左右孩子
        // d：该节点子树是按哪一维划分的，1是按x，2按y

        //距离的平方
        Ty dis2(const Node &b) const
        {
            return sqr(x-b.x)+sqr(y-b.y);
        }
    };

    //按x轴坐标比较
    bool __cmpx(const Node &a,const Node &b) 
    {
        return a.x<b.x;
    }

    //按y轴坐标比较
    bool __cmpy(const Node &a,const Node &b) 
    {
        return a.y<b.y;
    }

    //二维kd树，使用前需要先整好n和s数组
    struct Kdtree
    {
        Node s[N];
        int n;
        //s:点（需要预先读入）
        //n：点的个数（需要预先读入）

        //建树时维护子树
        void __upd(int p)
        {
            s[p].L=s[p].R=s[p].x;
            s[p].U=s[p].D=s[p].y;
            if(s[p].lc)
            {   //注意这里min和max的次序
                s[p].L=min(s[p].L,s[s[p].lc].L);
                s[p].R=max(s[p].R,s[s[p].lc].R);
                s[p].U=max(s[p].U,s[s[p].lc].U);
                s[p].D=min(s[p].D,s[s[p].lc].D);
            }
            if(s[p].rc)
            {
                s[p].L=min(s[p].L,s[s[p].rc].L);
                s[p].R=max(s[p].R,s[s[p].rc].R);
                s[p].U=max(s[p].U,s[s[p].rc].U);
                s[p].D=min(s[p].D,s[s[p].rc].D);
            }
        }

        //取得方差以决定子树划分方式
        bool __get_variance(int l,int r)
        {
            double sumx=0,sumy=0;
            for(int i=l;i<=r;i++)
            {
                sumx+=s[i].x;
                sumy+=s[i].y;
            }
            sumx/=r-l+1;
            sumy/=r-l+1;
            double vx=0,vy=0;
            for(int i=l;i<=r;i++)
            {
                // vx+=(s[i].x-sumx)*(s[i].x-sumx);
                vx+=sqr(s[i].x-sumx);
                // vy+=(s[i].y-sumy)*(s[i].y-sumy);
                vy+=sqr(s[i].y-sumy);
            }
            if(vx>=vy) return true;
            return false;
        }

        //建树
        int build(int l,int r)
        {
            if(l>r) return 0;
            if(l==r)
            {
                __upd(l);
                return l;
            }
            int mid=(l+r)/2;
            bool mark=__get_variance(l,r);
            if(mark)
            {
                
                nth_element(s+l,s+mid,s+r+1,__cmpx);
                s[mid].d=1;
            }
            else
            {
                
                nth_element(s+l,s+mid,s+r+1,__cmpy);
                s[mid].d=2;
            }

            s[mid].lc=build(l,mid-1);
            s[mid].rc=build(mid+1,r);
            __upd(mid);
            return mid;
        }

        //下标为p的点到下标为b的点所维护的矩形的距离
        Ty __to_rectangle_dis(int p,int b)
        {
            Ty ans=0;
            if(s[b].L>s[p].x)
            {
                // ans+=(s[b].L-s[p].x)*(s[b].L-s[p].x);
                ans+=sqr(s[b].L-s[p].x);
            }
            if(s[b].R<s[p].x)
            {
                ans+=sqr(s[b].R-s[p].x);
            }
            if(s[b].U<s[p].y)
            {
                ans+=sqr(s[b].U-s[p].y);
            }
            if(s[b].D>s[p].y)
            {
                ans+=sqr(s[b].D-s[p].y);
            }
            return ans;
        }

        // 询问下标为index的点到树上除了自己外最近点的距离
        void query(int l,int r,int index,Ty &ans)
        {
            if(l>r)
            {
                return ;
            }
            int mid=(l+r)/2;
            if(mid!=index)
            {
                ans=min(ans,s[mid].dis2(s[index]));
            }
            if(l==r) return ;
            Ty dis1=__to_rectangle_dis(index,s[mid].lc);
            Ty dis2=__to_rectangle_dis(index,s[mid].rc);
            if(dis1<dis2)
            {
                if(dis1<ans) query(l,mid-1,index,ans);
                if(dis2<ans) query(mid+1,r,index,ans);
            }
            else
            {
                if(dis2<ans) query(mid+1,r,index,ans);
                if(dis1<ans) query(l,mid-1,index,ans);
            }
            /*
            if(dis1<ans && dis2<ans)
            {
                if(dis1<dis2)
                {
                    if(dis1<ans) query(l,mid-1,index,ans);
                    if(dis2<ans) query(mid+1,r,index,ans);
                }
                else
                {
                    if(dis2<ans) query(mid+1,r,index,ans);
                    if(dis1<ans) query(l,mid-1,index,ans);
                }
            }
            else
            {
                if(dis1<ans) query(l,mid-1,index,ans);
                if(dis2<ans) query(mid+1,r,index,ans);
            }*/
        }
    }kdt;
}
using namespace KDTREE;


int main()
{
    //ios::sync_with_stdio(false);
    //cin.tie(0),cout.tie(0);
    scanf("%d",&kdt.n);
    for(int i=1;i<=kdt.n;i++)
    {
        Ty x,y;
        scanf("%lld%lld",&x,&y);
        kdt.s[i].x=x;
        kdt.s[i].y=y;
    }
    kdt.build(1,kdt.n);

    Ty ans=5e18;
    for(int i=1;i<=kdt.n;i++)
    {
        kdt.query(1,kdt.n,i,ans);
    }

    printf("%.4lf\n",sqrt(ans));
    return 0;
}
```

Finding Hotels

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

namespace KDTREE
{
    typedef ll Ty;
    const int N=1e6+10;

    //平方
    template<typename T>
    T sqr(T x)
    {
        return x*x;
    }

    struct Node
    {
        Ty x,y;
        Ty L,R,U,D;
        int lc,rc;
        int d;
        //x,y:点的坐标
        //LRUD：点所管辖的子树的最小矩形覆盖
        //lc，rc：左右孩子
        // d：该节点子树是按哪一维划分的，1是按x，2按y

        int c;
        int cc;
        int id;

        //距离的平方
        Ty dis2(const Node &b) const
        {
            return sqr(x-b.x)+sqr(y-b.y);
        }
    };

    //按x轴坐标比较
    bool __cmpx(const Node &a,const Node &b) 
    {
        return a.x<b.x;
    }

    //按y轴坐标比较
    bool __cmpy(const Node &a,const Node &b) 
    {
        return a.y<b.y;
    }

    //二维kd树，使用前需要先整好n和s数组
    struct Kdtree
    {
        Node s[N];
        int n;
        //s:点（需要预先读入）
        //n：点的个数（需要预先读入）

        //建树时维护子树
        void __upd(int p)
        {
            s[p].L=s[p].R=s[p].x;
            s[p].U=s[p].D=s[p].y;
            s[p].cc=s[p].c;
            if(s[p].lc)
            {   //注意这里min和max的次序
                s[p].L=min(s[p].L,s[s[p].lc].L);
                s[p].R=max(s[p].R,s[s[p].lc].R);
                s[p].U=max(s[p].U,s[s[p].lc].U);
                s[p].D=min(s[p].D,s[s[p].lc].D);

                s[p].cc=min(s[p].cc,s[s[p].lc].cc);
            }
            if(s[p].rc)
            {
                s[p].L=min(s[p].L,s[s[p].rc].L);
                s[p].R=max(s[p].R,s[s[p].rc].R);
                s[p].U=max(s[p].U,s[s[p].rc].U);
                s[p].D=min(s[p].D,s[s[p].rc].D);

                s[p].cc=min(s[p].cc,s[s[p].rc].cc);
            }
        }

        //取得方差以决定子树划分方式
        bool __get_variance(int l,int r)
        {
            double sumx=0,sumy=0;
            for(int i=l;i<=r;i++)
            {
                sumx+=s[i].x;
                sumy+=s[i].y;
            }
            sumx/=r-l+1;
            sumy/=r-l+1;
            double vx=0,vy=0;
            for(int i=l;i<=r;i++)
            {
                // vx+=(s[i].x-sumx)*(s[i].x-sumx);
                vx+=sqr(s[i].x-sumx);
                // vy+=(s[i].y-sumy)*(s[i].y-sumy);
                vy+=sqr(s[i].y-sumy);
            }
            if(vx>=vy) return true;
            return false;
        }

        //建树
        int build(int l,int r,bool mark)
        {
            if(l>r) return 0;
            if(l==r)
            {
                __upd(l);
                return l;
            }
            int mid=(l+r)/2;
            // bool mark=__get_variance(l,r);
            mark=__get_variance(l,r);
            if(mark)
            {
                nth_element(s+l,s+mid,s+r+1,__cmpx);
                s[mid].d=1;
            }
            else
            {
                nth_element(s+l,s+mid,s+r+1,__cmpy);
                s[mid].d=2;
            }

            s[mid].lc=build(l,mid-1,((mark)?(0):(1)));
            s[mid].rc=build(mid+1,r,((mark)?(0):(1)));
            __upd(mid);
            return mid;
        }

        Ty __to_rectangle_dis(Node &nd,Node &b)
        {
            Ty ans=0;
            if(b.L>nd.x)
            {
                // ans+=(s[b].L-s[p].x)*(s[b].L-s[p].x);
                ans+=sqr(b.L-nd.x);
            }
            if(b.R<nd.x)
            {
                ans+=sqr(b.R-nd.x);
            }
            if(b.U<nd.y)
            {
                ans+=sqr(b.U-nd.y);
            }
            if(b.D>nd.y)
            {
                ans+=sqr(b.D-nd.y);
            }
            return ans;
        }

        //以点搜最近点
        void query(int l,int r,Node &nd,Ty &ans, Node &ans2)
        {
            if(l>r) return ;
            int mid=(l+r)/2;    
            if(s[mid].c<=nd.c)
            {
                // ans=min(ans,nd.dis2(s[mid]));
                Ty dis=nd.dis2(s[mid]);
                if(ans>dis)
                {
                    ans=dis;
                    ans2=s[mid];
                }
                else if(ans==dis)
                {
                    if(ans2.id>s[mid].id)
                    {
                        ans=dis;
                        ans2=s[mid];
                    }
                }
            }
            if(l==r) return ;

            //剪枝判据……
            Ty dis1=__to_rectangle_dis(nd,s[s[mid].lc]);
            Ty dis2=__to_rectangle_dis(nd,s[s[mid].rc]);

            //...
            if(dis1<dis2)
            {
                if(dis1<=ans && s[s[mid].lc].cc<=nd.c)
                    query(l,mid-1,nd,ans,ans2);
                if(dis2<=ans && s[s[mid].rc].cc<=nd.c)
                    query(mid+1,r,nd,ans,ans2);
            }
            else
            {
                if(dis2<=ans && s[s[mid].rc].cc<=nd.c)
                    query(mid+1,r,nd,ans,ans2);
                if(dis1<=ans && s[s[mid].lc].cc<=nd.c)
                    query(l,mid-1,nd,ans,ans2);
            }
        }
    }kdt;
}
using namespace KDTREE;


int T;
int n1,n2;
int main()
{
    scanf("%d",&T);
    while(T--)
    {
        scanf("%d",&n1);
        scanf("%d",&n2);
        kdt.n=n1;
        for(int i=1;i<=n1;i++)
        {
            kdt.s[i]=kdt.s[0];
            int x,y,c;
            scanf("%d%d%d",&x,&y,&c);
            kdt.s[i].x=x;
            kdt.s[i].y=y;
            kdt.s[i].c=c;
            kdt.s[i].id=i;
        }   

        kdt.build(1,kdt.n,1);


        for(int i=1;i<=n2;i++)
        {
            int x,y,c;
            scanf("%d%d%d",&x,&y,&c);
            Node nd,ans2;
            ll ans=1e18;
            nd.x=x;
            nd.y=y;
            nd.c=c;

            kdt.query(1,kdt.n,nd,ans,ans2);
            printf("%lld %lld %d\n" , ans2.x , ans2.y, ans2.c);
        }

    }



    return 0;
}
```


## 树上启发式合并

```c++
// CF600E Lomsat gelral
//树上启发式合并。题意翻译：树的节点有颜色，一种颜色占领了一个子树，当且仅当没有其他颜色在这个子树中出现得比它多。求占领每个子树的所有颜色之和。
#include <iostream>
#include <cstdio>
#include <cstring>
#include <memory.h>
#include <algorithm>
#include <map>
#include <vector>
#include <queue>
#include <cmath>
using namespace std;
typedef long long ll;
const int N = 2e5 + 10;
int n;
struct E
{
    int to, inext;
} edge[N * 2];
struct NODE
{
    int sz, color, heavy_son;//子树大小，点的颜色，重儿子
} node[N];
int head[N], total;

void adde(int x, int y)
{
    edge[++total].to = y;
    edge[total].inext = head[x];
    head[x] = total;
}

namespace dsu_on_tree
{
    int cnt[N];
    ll sum=0,maxval=-1; //sum，maxval：影响部分，sum表示数量最多的那个颜色的个数，maxval表示数量最多的那个颜色的标号
    ll ans[N]; //答案部分：每个点的答案保存在此

    //统计某个子树下（除了heavy_son的子树因为已经统计过了）对答案造成的影响
    void addans(int x, int fa, int heavy_son,int val) 
    {
        //暴力怎么写下面就怎么写
        cnt[node[x].color]+=val;
        if(cnt[node[x].color]>maxval)
        {
            maxval=cnt[node[x].color];
            sum=node[x].color;
        }
        else if(cnt[node[x].color]==maxval) sum+=(ll)node[x].color;
        for (int e = head[x]; e; e = edge[e].inext)
        {
            int y = edge[e].to;
            if (y == fa || y == heavy_son)
                continue;
            addans(y, x, heavy_son, val);
        }
    }

    //第一次遍历，得到子树size和heavy_son
    void dfs(int x, int fa)
    {
        node[x].sz = 1;
        node[x].heavy_son = 0;
        int maxsz = 0;
        for (int e = head[x]; e; e = edge[e].inext)
        {
            int y = edge[e].to;
            if (y == fa)
                continue;
            dfs(y, x);
            node[x].sz += node[y].sz;
            if (node[y].sz > maxsz)
            {
                maxsz = node[y].sz;
                node[x].heavy_son = y;
            }
        }
    }

    //第二次遍历，统计答案
    void dfs2(int x, int fa, bool sv) //sv:是否保留
    {
        //轻儿子（递归）
        for (int e = head[x]; e; e = edge[e].inext)
        {
            int y = edge[e].to;
            if (y == fa || y == node[x].heavy_son)
                continue; //跳过父亲和重儿子
            dfs2(y, x, 0);
        }
        //重儿子（递归）
        if (node[x].heavy_son)
        {
            dfs2(node[x].heavy_son, x, 1);
        }
        //轻儿子（暴力）
        addans(x,fa,node[x].heavy_son,1);
        ans[x]=sum;//update ans
        if(sv==0)//撤销子树下的所有影响
        {
            addans(x,fa,0,-1);
            sum=0;
            maxval=-1;
        }
    }
} // namespace dsu_on_tree
using namespace dsu_on_tree;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        cin >> node[i].color;
    }
    for (int i = 1; i <= n - 1; i++)
    {
        int x, y;
        cin >> x >> y;
        adde(x, y);
        adde(y, x);
    }
    dfs(1, 0); //root is 1, predo:heavy_son
    dfs2(1,0,0);
    for(int i=1;i<=n;i++)
    {
        cout<<ans[i]<<" ";
    }
    return 0;
}

```

```c++
/**
 * 2021牛客多校9 E Eyjafjalla
 题意：给定一个以 1 为根的有根树，孩子的点权小于父亲的点权。多次询问，每次询问包含 x 节点的权值范围为 [l, r] 的极大连通的大小。
 做法：离线然后离散化，之后用个树状数组统计每个询问在子树中的答案。
**/

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

namespace BIT {
    const int MAXN=4e5+10;

    template <typename Ty>
    struct Bit
    {
        //local
        Ty t[MAXN];
        int __lowbit(int x)
        {
            return x&(-x);
        }

        void add(int pos,Ty val=1)
        {
            while(pos<MAXN)
            {
                t[pos]+=val;
                pos+=__lowbit(pos);
            }
        }

        Ty ask(int pos)
        {
            Ty ans=0;
            while(pos>=1)
            {
                ans+=t[pos];
                pos-=__lowbit(pos);
            }
            return ans;
        }
    };
}
using namespace BIT;
Bit<int> bit;

//...
int n;
int w[N];

int ww[N*2],ww_p;
unordered_map<int,int> mp;

struct Q
{
    int x,l,r;
    int ans;
}query[N];
int q;


struct E
{
    int to,inext;
}edge[N*2];
int head[N],total;
int f[N][30];
int vis[N];

struct Node
{
    vector<int> qid;
    int sz;
    int heavy_son;
}node[N];

//...

void adde(int x,int y)
{
    edge[++total].to=y;
    edge[total].inext=head[x];
    head[x]=total;
}

int upb;

//bfs，使得能树上倍增
void predo(int rt=1)
{
    queue<int> q;
    q.push(rt);
    vis[rt]=1;
    while(q.size())
    {
        int x=q.front();
        q.pop();
        for(int e=head[x];e;e=edge[e].inext)
        {
            int y=edge[e].to;
            if(vis[y]==0)
            {
                vis[y]=vis[x]+1;
                f[y][0]=x;
                for(int k=1;k<=upb;k++)
                {
                    f[y][k]=f[f[y][k-1]][k-1];
                }
                q.push(y);
            }
        }
    }
}

//预处理离线答案
void predo2(int i)
{
    int x=query[i].x;
    int r=query[i].r;
    int l=query[i].l;

    // int tmpans=x;
    if(l<=w[x] &&  w[x]<=r)
    {
        for(int k=upb;k>=0;k--)
        {
            if(f[x][k]!=0 && w[f[x][k]]<=r)
            {
                x=f[x][k];
            }
        }
        node[x].qid.emplace_back(i);
    }
    else
    {
        query[i].ans=0;
    }
}

//dsu on tree

void dfs1(int x,int fa)
{
    node[x].sz=1;
    node[x].heavy_son=0;
    int maxsz=0;
    for(int e=head[x];e;e=edge[e].inext)
    {
        int y=edge[e].to;
        if(y==fa) continue;
        dfs1(y,x);
        node[x].sz+=node[y].sz;
        if (node[y].sz > maxsz)
        {
            maxsz = node[y].sz;
            node[x].heavy_son = y;
        }
    }
}

void addans(int x,int fa,int heavy_son,int val=1)
{   

    bit.add(mp[w[x]],val);

    for(int e=head[x];e;e=edge[e].inext)
    {
        int y=edge[e].to;
        if(y==fa || y==heavy_son) continue;
        addans(y,x,heavy_son,val);
    }
}

//dsu on tree，树上启发式
void dfs2(int x,int fa,bool sv)
{
    for(int e=head[x];e;e=edge[e].inext)
    {
        int y=edge[e].to;
        if(y==fa || y==node[x].heavy_son) continue;
        dfs2(y,x,0);
    }
    if(node[x].heavy_son)
    {
        dfs2(node[x].heavy_son,x,1);
    }

    addans(x,fa,node[x].heavy_son,1);

    //getans
    for(auto v:node[x].qid)
    {
        int l=query[v].l;
        query[v].ans=node[x].sz-bit.ask(mp[l]-1);
    }


    if(sv==0)
    {
        addans(x,fa,0,-1); //重要：删除子树影响的heavy_son一定要是0，不能和之前那个用addans的一样
    }
}


int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    cin>>n;
    upb=log2(n)+1;
    for(int i=1;i<=n-1;i++)
    {
        int x,y;
        cin>>x>>y;
        adde(x,y);
        adde(y,x);
    }

    for(int i=1;i<=n;i++)
    {
        cin>>w[i];
        ww[++ww_p]=w[i];
    }

    cin>>q;
    for(int i=1;i<=q;i++)
    {   
        cin>>query[i].x;
        cin>>query[i].l;
        cin>>query[i].r;

        ww[++ww_p]=query[i].l;
        ww[++ww_p]=query[i].r;
    }
    sort(ww+1,ww+1+ww_p);
    for(int i=1;i<=ww_p;i++)
    {
        mp[ww[i]]=i;
    }

    //bfs
    predo();
    //预处理离线答案
    for(int i=1;i<=q;i++)
    {
        predo2(i);
    }

    dfs1(1,0);
    dfs2(1,0,0);
    for(int i=1;i<=q;i++)
    {
        cout<<query[i].ans<<'\n';
    }


    return 0;
}
```

## ⭐Splay 伸展樹
~~（附註：可能這個版本的Splay有點問題，到時候得換掉）（尚未整理）~~

### 普通平衡树

您需要写一种数据结构（可参考题目标题），来维护一些数，其中需要提供以下操作：

- 插入 xx 数
- 删除 xx 数(若有多个相同的数，因只删除一个)
- 查询 xx 数的排名(排名定义为比当前数小的数的个数 +1+1 )
- 查询排名为 xx 的数
- 求 xx 的前驱(前驱定义为小于 xx，且最大的数)
- 求 xx 的后继(后继定义为大于 xx，且最小的数)

```c++
#include <iostream>
#include <memory.h>
using namespace std;

namespace SPLAY
{
    const int N=114514;
    using Ty=int;
    const Ty Inf=1<<30; 
    //Inf：记得longlong的话要改这里！
    //注意：Inf不应该与任何可能的值相等，否则你需要考虑别的方式
    struct Splay
    {
        struct Node
        {
            int fa,ch[2];
            Ty val; //val：要维护的值
            Ty cnt,size; //cnt、size：固定，每个节点的个数和子树大小
        }t[N];

        //root:根节点下标
        //total:总节点数
        int root,total; 

        //更新节点p的信息
        void __upd(int p)
        {
            if(p==0) return ;
            t[p].size=t[t[p].ch[0]].size+t[t[p].ch[1]].size+t[p].cnt;
        }

        //判断p是否是在右儿子位置 judge whether p is father's R-child
        bool __get(int p)
        {
            return p==t[t[p].fa].ch[1];
        }

        //在内存节点池中分配一个新的值，并返回它的下标
        int __add(Ty val)
        {
            t[++total].val=val;
            t[total].cnt=t[total].size=1;
            t[total].fa=0; 
            t[total].ch[0]=t[total].ch[1]=0;
            return total;
        }

        void init()
        {
            root=1;
            total=0;
            __add(-Inf); //1
            __add(Inf); //2
            t[1].ch[1]=2;
            t[2].fa=1;
            __upd(root);
        }

        //zip or zap (depending on [whether p is father's R-child])
        void __rotate(int p)
        {
            // z->y->p
            int y=t[p].fa, z=t[y].fa, chk=__get(p); //!!!You must use previous version Get(p)
            if(y==0 || p==0) return ;
            // Modify p's R(L)Child as y's L(R)Child
            t[y].ch[chk]=t[p].ch[chk^1];
            if(t[p].ch[chk^1]) t[t[p].ch[chk^1]].fa=y;
            // Then change p's Child
            t[p].ch[chk^1]=y;
            t[y].fa=p;
            // Finally change p's father as z (which was y's father)
            t[p].fa=z;
            if(z) t[z].ch[y==t[z].ch[1]]=p;

            __upd(y);
            __upd(p);
        }

        //伸展操作 : 
        // 如果 p 的父亲是根节点，直接将 p 左旋或右旋
        // 如果 p 的父亲不是根节点，且 p 和父亲的儿子类型相同，首先将其父亲左旋或右旋，然后将 p 右旋或左旋
        // 如果 p 的父亲不是根节点，且 p 和父亲的儿子类型不同，将 p 左旋再右旋、或者右旋再左旋
        void __splay(int p)
        {
            for(int f=t[p].fa;f=t[p].fa;__rotate(p))
            {
                if(t[f].fa) __rotate(__get(p)==__get(f) ? f : p); //Case 3,4:(p,y,z at the same line) | 5,6:(else)
            }
            root=p;
        }

        //插入一个值为val的元素
        void insert(Ty val)
        {
            if(root==0)
            {
                root=__add(val);
                __upd(root);
                return ;
            }

            int p=root, f=0;
            while(true)
            {
                if(t[p].val==val)
                {
                    t[p].cnt++;
                    __upd(p);
                    __upd(f);
                    __splay(p);
                    return ;
                }

                f=p; //f!=0;
                p=t[p].ch[ t[p].val<val ];

                if(!p)//Child does not exist
                {
                    t[f].ch[ t[f].val<val ] = __add(val);
                    t[total].fa=f;
                    __upd(total);
                    __upd(f);
                    __splay(total);
                    return ;
                }
            }
        }

        //取得>=val的节点最小排名（从1计数）和下标，相当于lower_bound
        //注意这几个操作会立即使得被找到的节点被splay到根节点，不过下标仍然是有效的
        pair<Ty,int> get_rank_by_val(Ty val)
        {
            int p=root;
            if(p==0) return {t[root].size+1,-1};

            int sum=0; //当前 < val 的节点cnt之和
            int ans=t[root].size+1; //ans:当前最好排名
            int cur=-1; //cur：当前答案的下标

            while(p)
            {
                if(t[p].val < val) // <
                {
                    sum+=t[p].cnt;
                    if(t[p].ch[0]) sum+=t[t[p].ch[0]].size;
                    p=t[p].ch[1];
                }
                else // >= 
                {
                    int tmpans=sum+1+((t[p].ch[0])?(t[t[p].ch[0]].size):(0));
                    if(tmpans<ans)
                    {
                        ans=tmpans;
                        cur=p;
                    }
                    p=t[p].ch[0];
                }
            }

            // *如果不选择在init时插入两个初始无穷大值，则请在这里移除这段代码
            ans-=1;
            // *移除代码段结束

            if(cur!=-1) __splay(cur);
            return {ans, cur};
        }

        //根据排名（从1开始）取得值和下标
        //注意这几个操作会立即使得被找到的节点被splay到根节点，不过下标仍然是有效的
        pair<Ty,int> get_val_by_rank(int rank)
        {
            // *如果不选择在init时插入两个初始无穷大值，则请在这里移除这段代码
            rank+=1;
            // *移除代码段结束

            int p=root;

            
            while (true)
            {
                if(t[p].ch[0] && t[t[p].ch[0]].size >= rank)
                {
                    p=t[p].ch[0];
                }
                else
                {
                    rank-=t[p].cnt+t[t[p].ch[0]].size;
                    if(rank<=0)
                    {
                        __splay(p);
                        return {t[p].val,p};
                    }
                    p=t[p].ch[1];
                }
            }
        }

        //根据val的值，寻找对应节点的后继，返回节点下标
        //注意：1.一定要保证节点存在， 2.一定要保证树中有无穷小和无穷大两个节点，且下标分别是1、2
        int get_next_index(Ty val)
        {
            int ans=2,p=root; //t[ans].val== Inf (INIT)
            while (p)
            {
                if(val==t[p].val)//search success
                {
                    if(t[p].ch[1])
                    {
                        p=t[p].ch[1];
                        while(t[p].ch[0]) p=t[p].ch[0];
                        ans=p;
                    }
                    break;
                }
                //else update ans using path
                if(t[p].val>val && t[p].val<t[ans].val) ans=p;
                p=(t[p].val<val) ? t[p].ch[1] : t[p].ch[0];
            }
            return ans; //Notice that return value is subscript
        }

        //根据val的值，寻找对应节点的前驱，返回节点下标
        //注意：1.一定要保证节点存在， 2.一定要保证树中有无穷小和无穷大两个节点，且下标分别是1、2
        int get_pre_index(Ty val)
        {
            int ans=1,p=root; //t[ans].val== -Inf (INIT)
            while (p)
            {
                if(val==t[p].val)//search success
                {
                    if(t[p].ch[0])
                    {
                        p=t[p].ch[0];
                        while(t[p].ch[1]) p=t[p].ch[1];
                        ans=p;
                    }
                    break;
                }
                //else update ans using path
                if(t[p].val<val && t[p].val>t[ans].val) ans=p;
                p=(t[p].val<val) ? t[p].ch[1] : t[p].ch[0];
            }
            return ans;//Notice that return value is subscript
        }

        //根据val删去节点：先通过lower_bound找到第一个等于val的节点，把他splay到根，然后删除之
        //注意：树中一定要有等于val值的节点！
        void del(Ty val)
        {
            if(get_rank_by_val(val).second==-1) return;
            if(t[root].cnt>1)
            {
                t[root].cnt--;
                __upd(root);
                return ;
            }

            int p=root;
            int q=get_pre_index(val);
            __splay(q);
            t[t[p].ch[1]].fa=q;
            t[q].ch[1]=t[p].ch[1];
        }
    }splay_tree;

}


int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);

    SPLAY::splay_tree.init();

    int n;
    cin>>n;
    for(int i=1;i<=n;i++)
    {
        int op,x;
        cin>>op>>x;
        if(op==1) SPLAY::splay_tree.insert(x);
        else if(op==2) SPLAY::splay_tree.del(x);
        else if(op==3) cout<< SPLAY::splay_tree.get_rank_by_val(x).first<<endl;
        else if(op==4) cout<< SPLAY::splay_tree.get_val_by_rank(x).first<<endl;
        else if(op==5) cout<< SPLAY::splay_tree.t[SPLAY::splay_tree.get_pre_index(x)].val<<endl;
        else cout<< SPLAY::splay_tree.t[SPLAY::splay_tree.get_next_index(x)].val<<endl;
    }
    return 0;
}


/*
旧的操作
int Get_Rank_By_Val(int x)
{
    int ans=0,p=root;
    while (true)
    {
        if(x<t[p].val)
        {
            p=t[p].ch[0];
        }
        else
        {
            ans+=t[t[p].ch[0]].size;
            if(x==t[p].val)
            {
                Splay(p);
                return ans+1;
            }
            ans+=t[p].cnt;
            p=t[p].ch[1];
        }
    }
}
*/
```

### 文艺平衡树
您需要写一种数据结构（可参考题目标题），来维护一个有序数列。
其中需要提供以下操作：翻转一个区间，例如原有序序列是 5 4 3 2 1，翻转区间是 [2,4][2,4] 的话，结果是 5 2 3 4 1。

```c++
#include <iostream>
using namespace std;
const int N=114514;

namespace SPLAY
{
    const int N=114514;
    using Ty=int;
    const Ty Inf=1<<30; 
    //Inf：记得longlong的话要改这里！
    //注意：Inf不应该与任何可能的值相等，否则你需要考虑别的方式
    struct Splay
    {
        struct Node
        {
            int fa,ch[2];
            Ty val; //val：要维护的值
            Ty cnt,size; //cnt、size：固定，每个节点的个数和子树大小
            int T; //T:反转标记
        }t[N];

        //root:根节点下标
        //total:总节点数
        int root,total; 

        //更新节点p的信息
        void __upd(int p)
        {
            if(p==0) return ;
            t[p].size=t[t[p].ch[0]].size+t[t[p].ch[1]].size+t[p].cnt;
        }

        //判断p是否是在右儿子位置 judge whether p is father's R-child
        bool __get(int p)
        {
            return p==t[t[p].fa].ch[1];
        }

        //在内存节点池中分配一个新的值，并返回它的下标
        int __add(Ty val)
        {
            t[++total].val=val;
            t[total].cnt=t[total].size=1;
            t[total].fa=0; 
            t[total].ch[0]=t[total].ch[1]=0;
            t[total].T=0;
            return total;
        }

        void init()
        {
            root=1;
            total=0;
            __add(-Inf); //1
            __add(Inf); //2
            t[1].ch[1]=2;
            t[2].fa=1;
            __upd(root);
        }

        //下传反转标记
        void __spread(int p)
        {
            if(t[p].T)
            {
                swap(t[t[p].ch[0]].ch[0],t[t[p].ch[0]].ch[1]); //LChild
                swap(t[t[p].ch[1]].ch[0],t[t[p].ch[1]].ch[1]); //RChild
                t[t[p].ch[0]].T^=1;
                t[t[p].ch[1]].T^=1;
                t[p].T=0;
            }
        }

        //zip or zap (depending on [whether p is father's R-child])
        void __rotate(int p)
        {
            __spread(p);
            // z->y->p
            int y=t[p].fa, z=t[y].fa, chk=__get(p); //!!!You must use previous version Get(p)
            if(y==0 || p==0) return ;
            // Modify p's R(L)Child as y's L(R)Child
            t[y].ch[chk]=t[p].ch[chk^1];
            if(t[p].ch[chk^1]) t[t[p].ch[chk^1]].fa=y;
            // Then change p's Child
            t[p].ch[chk^1]=y;
            t[y].fa=p;
            // Finally change p's father as z (which was y's father)
            t[p].fa=z;
            if(z) t[z].ch[y==t[z].ch[1]]=p;

            __upd(y);
            __upd(p);
        }

        //伸展操作 : 
        // 如果 p 的父亲是根节点，直接将 p 左旋或右旋
        // 如果 p 的父亲不是根节点，且 p 和父亲的儿子类型相同，首先将其父亲左旋或右旋，然后将 p 右旋或左旋
        // 如果 p 的父亲不是根节点，且 p 和父亲的儿子类型不同，将 p 左旋再右旋、或者右旋再左旋
        void __splay(int p)
        {
            __spread(p);
            for(int f=t[p].fa;f=t[p].fa;__rotate(p))
            {
                if(t[f].fa) __rotate(__get(p)==__get(f) ? f : p); //Case 3,4:(p,y,z at the same line) | 5,6:(else)
            }
            root=p;
        }

        //将p不断旋转到根前为止
        void __splay2(int p)
        {
            __spread(p);
            for(int f=t[p].fa;f=t[p].fa,f!=root;__rotate(p))
            {
                if(t[f].fa!=root) __rotate(__get(p)==__get(f) ? f : p); //Case 3,4:(p,y,z at the same line) | 5,6:(else)
            }
        }

        //插入一个值为val的元素
        void insert(Ty val)
        {
            if(root==0)
            {
                root=__add(val);
                __upd(root);
                return ;
            }

            int p=root, f=0;
            while(true)
            {
                if(t[p].val==val)
                {
                    t[p].cnt++;
                    __upd(p);
                    __upd(f);
                    __splay(p);
                    return ;
                }

                f=p; //f!=0;
                p=t[p].ch[ t[p].val<val ];

                if(!p)//Child does not exist
                {
                    t[f].ch[ t[f].val<val ] = __add(val);
                    t[total].fa=f;
                    __upd(total);
                    __upd(f);
                    __splay(total);
                    return ;
                }
            }
        }

        //根据排名（从1开始）取得值和下标
        //注意这几个操作会立即使得被找到的节点被splay到根节点，不过下标仍然是有效的
        pair<Ty,int> get_val_by_rank(int rank, bool root_RChild_rotate)
        {
            // *如果不选择在init时插入两个初始无穷大值，则请在这里移除这段代码
            rank+=1;
            // *移除代码段结束
            int p=root;

            while (true)
            {
                __spread(p);
                if(t[p].ch[0] && t[t[p].ch[0]].size >= rank)
                {
                    p=t[p].ch[0];
                }
                else
                {
                    rank-=t[p].cnt+t[t[p].ch[0]].size;
                    if(rank<=0)
                    {
                        if(root_RChild_rotate) __splay2(p);
                        else __splay(p);
                        return {t[p].val,p};
                    }
                    p=t[p].ch[1];
                }
            }
        }

        void reverse(int l,int r)
        {
            get_val_by_rank(l-1,0); //l-1 is root
            get_val_by_rank(r+1,1); //r+1 is rchild of l-1 now
            //now r+1's lchild is the range needed to be reversed.

            int p=root;
            __spread(p);
            p=t[p].ch[1];
            __spread(p);
            p=t[p].ch[0];
            __spread(p);

            swap(t[p].ch[0],t[p].ch[1]);
            t[p].T^=1;
        }

        void output(int p)
        {
            __spread(p);
            if(t[p].ch[0])
            {
                output(t[p].ch[0]);
            }
            if(p && p!=1 && p!=2)
            {
                cout<<t[p].val<<" ";
            }
            if(t[p].ch[1])
            {
                output(t[p].ch[1]);
            }
        }

    }splay_tree;

}

int n,m;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);

    cin>>n>>m;
    SPLAY::splay_tree.init();
    for(int i=1;i<=n;i++)
    {
        SPLAY::splay_tree.insert(i);
    }

    for(int i=1;i<=m;i++)
    {
        int l,r;
        cin>>l>>r;
  
        SPLAY::splay_tree.reverse(l,r);
    }
    SPLAY::splay_tree.output(SPLAY::splay_tree.root);
    return 0;
}
```



## 吉如一线段树



### 区间取最小值+维护区间最大值与和

（从OI-wiki抄的）

区间取 min，意味着只对那些大于 $t$ 的数有更改。因此这个操作的对象不再是整个区间，而是“这个区间中大于 $t$ 的数”。于是我们可以有这样的思路：每个结点维护该区间的最大值 $Max$、次大值 $Se$、区间和 $Sum$ 以及最大值的个数 $Cnt$。接下来我们考虑区间对 $t$ 取 $\min$ 的操作。

1. 如果 $Max\le t$，显然这个 $t$ 是没有意义的，直接返回；
2. 如果 $Se<t\le Max$，那么这个 $t$ 就能更新当前区间中的最大值。于是我们让区间和加上 $Cnt(t-Max)$，然后更新 $Max$ 为 $t$，并打一个标记。
3. 如果 $t\le Se$，那么这时你发现你不知道有多少个数涉及到更新的问题。于是我们的策略就是，暴力递归向下操作。然后上传信息。

这个算法的复杂度如何？使用势能分析法可以得到复杂度是 $O(m\log n)$ 的。具体分析过程见论文。

5306_Gorgeous_Sequence3_myfastio
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
int T;

namespace JSJSEgmentTree
{
    using Ty=ll;
    const int MAXN=2e6+10;
    const Ty Inf=1e17;

    int n,m;
    Ty a[MAXN];

    struct JSJSegmentTree
    {
        struct Node
        {
            Ty maxval,sec_maxval,maxval_cnt;
            Ty mintag;
            Ty sum;
            int l,r;
        }t[MAXN*4];

        void __upd(int p)
        {
            t[p].sum=t[p*2].sum + t[p*2+1].sum;

            if(t[p*2].maxval == t[p*2+1].maxval)
            {
                t[p].maxval = t[p*2].maxval;
                t[p].maxval_cnt = t[p*2].maxval_cnt + t[p*2+1].maxval_cnt;
                t[p].sec_maxval = max(t[p*2].sec_maxval, t[p*2+1].sec_maxval);
            }
            else if(t[p*2].maxval > t[p*2+1].maxval)
            {
                t[p].maxval = t[p*2].maxval;
                t[p].maxval_cnt = t[p*2].maxval_cnt;
                t[p].sec_maxval = max(t[p*2].sec_maxval, t[p*2+1].maxval);
            }
            else
            {
                t[p].maxval = t[p*2+1].maxval;
                t[p].maxval_cnt = t[p*2+1].maxval_cnt;
                t[p].sec_maxval = max(t[p*2].maxval, t[p*2+1].sec_maxval);
            }
        }

        void __tagpush(int p, Ty mintag)
        {
            if(t[p].maxval>mintag)
            {
                t[p].sum+= (mintag - t[p].maxval) * t[p].maxval_cnt;

                t[p].maxval = mintag; //  mintag < t[p].maxval <= t[p].mintag always hold
                t[p].mintag = mintag;
            }
        }

        void __tagdown(int p)
        {
            if(t[p].mintag!=Inf)
            {
                __tagpush(p*2, t[p].mintag);
                __tagpush(p*2+1, t[p].mintag);

                t[p].mintag=Inf;
            }
        }

        void build_tree(int p,int l,int r)
        {
            t[p].mintag=Inf;
            t[p].l=l;
            t[p].r=r;
            if(l==r)
            {
                t[p].sum = t[p].maxval = a[l];
                t[p].maxval_cnt = 1;
                t[p].sec_maxval = -Inf;
                return ;
            }

            int mid=(l+r)/2;
            build_tree(p*2,l,mid);
            build_tree(p*2+1,mid+1,r);
            __upd(p);
        }

        void modify_min(int p,int L,int R,Ty val)
        {
            if(t[p].maxval > val)
            {
                if(L<=t[p].l && t[p].r<=R && t[p].sec_maxval<val)
                {
                    return __tagpush(p, val);
                }
                __tagdown(p);
                int mid=(t[p].l + t[p].r)/2;
                if(L<=mid)
                {
                    modify_min(p*2,L,R,val);
                }
                if(mid<R)
                {
                    modify_min(p*2+1,L,R,val);
                }
                __upd(p);
            }
        }

        Ty query_sum(int p,int L,int R)
        {
            if(L<=t[p].l && t[p].r<=R)
            {
                return t[p].sum;
            }
            __tagdown(p);
            int mid=(t[p].l + t[p].r)/2;
            Ty ans=0;
            if(L<=mid)
            {
                ans+=query_sum(p*2,L,R);
            }
            if(mid<R)
            {
                ans+=query_sum(p*2+1,L,R);
            }
            return ans;
        }

        Ty query_max(int p,int L,int R)
        {
            if(L<=t[p].l && t[p].r<=R)
            {
                return t[p].maxval;
            }
            __tagdown(p);
            int mid=(t[p].l + t[p].r)/2;
            Ty ans=-Inf;
            if(L<=mid)
            {
                ans=max(query_max(p*2,L,R),ans);
            }
            if(mid<R)
            {
                ans=max(query_max(p*2+1,L,R),ans);
            }
            return ans;
        }
    };
}
using namespace JSJSEgmentTree;

JSJSegmentTree t;

namespace Fast_IO {

    char __nc()
    {
        static char buf[1000000], *p = buf, *q = buf;
        return p == q && (q = (p = buf) + fread(buf, 1, 1000000, stdin), p == q)
                ? EOF
                : *p++;
    }
    
    template <typename T> inline void read(T &x) {
    char c;
    int sgn = 1;
    x = 0;
    c = __nc();
    while (c > '9' || c < '0') {
        if (c == '-')
        sgn = -1;
        c = __nc();
    }
    while (c <= '9' && c >= '0') {
        x = (x << 3) + (x << 1) + (c - '0');
        c = __nc();
    }
    x *= sgn;
    }

    template <typename T> inline void print(T x) {
    if (x < 0) {
        putchar('-');
        x = -x;
    }
    if (x > 9) {
        print(x / 10);
    }
    putchar(x % 10 + '0');
    }
} // namespace Fast_IO
using namespace Fast_IO;


int main()
{
    read(T);
    for(int _=1;_<=T;_++)
    {
        read(n);
        read(m);

        for(int i=1;i<=n;i++)
        {
            read(a[i]);
        }

        t.build_tree(1,1,n);

        for(int i=1;i<=m;i++)
        {
            int op;
            read(op);

            if(op==0)
            {
                int l,r;
                ll val;
                read(l);
                read(r);
                read(val);
                t.modify_min(1,l,r,val);
            }
            else if(op==1) //get maxval
            {
                int l,r;
                read(l);
                read(r);
                printf("%lld\n",t.query_max(1,l,r));
            }
            else //op==2: get sum
            {
                int l,r; 
                read(l);
                read(r);
                printf("%lld\n",t.query_sum(1,l,r));
            }
        }

    }
    return 0;
}

```

### BZOJ4695 最假女选手

> 长度为 $n$ 的序列，支持区间加 $x$/区间对 $x$ 取 $\max$/区间对 $x$ 取 $\min$/求区间和/求区间最大值/求区间最小值。
>
> $N,M\le 5\times 10^5,|A_i|\le 10^8$。

同样的方法，我们维护最大、次大、最大个数、最小、次小、最小个数、区间和。除了这些信息，我们还需要维护区间 $\max$、区间 $\min$、区间加的标记。相比上一道题，这就涉及到标记下传的顺序问题了。我们采用这样的策略：

1. 我们认为区间加的标记是最优先的，其余两种标记地位平等。
2. 对一个结点加上一个 $v$ 标记，除了用 $v$ 更新卫星信息和当前结点的区间加标记外，我们用这个 v 更新区间 $\max$ 和区间 $\min$ 的标记。
3. 对一个结点取 $v$ 的 $\min$（这里忽略暴搜的过程，假定标记满足添加的条件），除了更新卫星信息，我们要与区间 $\max$ 的标记做比较。如果 $v$ 小于区间 $\max$ 的标记，则所有的数最后都会变成 v，那么把区间 $\max$ 的标记也变成 $v$。否则不管。
4. 区间取 v 的 $\max$ 同理。

另外，BZOJ 这道题卡常……多数组线段树的常数比结构体线段树的常数大……在维护信息的时侯，当只有一两个数的时侯可能发生数集重合，比如一个数既是最大值又是次小值。这种要特判。


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

namespace JSJSEgmentTree
{
    using Ty=ll;
    const int MAXN=5e5+10;
    const Ty Inf=1e17;

    int n,m;
    Ty a[MAXN];

    struct JSJSegmentTree
    {
        struct Node
        {
            Ty maxval,sec_maxval,maxval_cnt;
            Ty minval,sec_minval,minval_cnt;
            Ty mintag,maxtag,addtag;
            Ty sum;
            int l,r;
        }t[MAXN*4];

        void __upd(int p)
        {
            t[p].sum=t[p*2].sum + t[p*2+1].sum;

            if(t[p*2].maxval == t[p*2+1].maxval)
            {
                t[p].maxval = t[p*2].maxval;
                t[p].maxval_cnt = t[p*2].maxval_cnt + t[p*2+1].maxval_cnt;
                t[p].sec_maxval = max(t[p*2].sec_maxval, t[p*2+1].sec_maxval);
            }
            else if(t[p*2].maxval > t[p*2+1].maxval)
            {
                t[p].maxval = t[p*2].maxval;
                t[p].maxval_cnt = t[p*2].maxval_cnt;
                t[p].sec_maxval = max(t[p*2].sec_maxval, t[p*2+1].maxval);
            }
            else
            {
                t[p].maxval = t[p*2+1].maxval;
                t[p].maxval_cnt = t[p*2+1].maxval_cnt;
                t[p].sec_maxval = max(t[p*2].maxval, t[p*2+1].sec_maxval);
            }

            if(t[p*2].minval == t[p*2+1].minval)
            {
                t[p].minval = t[p*2].minval;
                t[p].minval_cnt = t[p*2].minval_cnt + t[p*2+1].minval_cnt;
                t[p].sec_minval = min(t[p*2].sec_minval, t[p*2+1].sec_minval);
            }
            else if(t[p*2].minval < t[p*2+1].minval)
            {
                t[p].minval = t[p*2].minval;
                t[p].minval_cnt = t[p*2].minval_cnt;
                t[p].sec_minval = min(t[p*2].sec_minval, t[p*2+1].minval);
            }
            else
            {
                t[p].minval = t[p*2+1].minval;
                t[p].minval_cnt = t[p*2+1].minval_cnt;
                t[p].sec_minval = min(t[p*2].minval, t[p*2+1].sec_minval);
            }
        }

        void __tagpush_add(int p, Ty val)
        {
            t[p].sum += (t[p].r-t[p].l+1ll)*val;
            t[p].maxval += val;
            t[p].minval += val;

            if(t[p].sec_maxval != -Inf) t[p].sec_maxval += val;
            if(t[p].sec_minval != Inf) t[p].sec_minval += val;
            if(t[p].maxtag != -Inf) t[p].maxtag += val;
            if(t[p].mintag != Inf) t[p].mintag += val;

            t[p].addtag += val;
        }

        void __tagpush_min(int p, Ty val)
        {
            if(t[p].maxval > val)
            {
                t[p].sum+=(1ll*val-t[p].maxval)*t[p].maxval_cnt;

                if(t[p].sec_minval == t[p].maxval) t[p].sec_minval=val;
                if(t[p].minval == t[p].maxval) t[p].minval = val;
                if(t[p].maxtag > val) t[p].maxtag = val;

                t[p].maxval = t[p].mintag = val;
            }
        }

        void __tagpush_max(int p, Ty val)
        {   
            if(t[p].minval < val)
            {
                t[p].sum+=(1ll*val-t[p].minval)*t[p].minval_cnt;

                if(t[p].sec_maxval == t[p].minval) t[p].sec_maxval = val;
                if(t[p].maxval == t[p].minval) t[p].maxval = val;
                if(t[p].mintag < val) t[p].mintag = val;

                t[p].minval = t[p].maxtag = val;
            }
        }

        void __tagdown(int p)
        {
            if(t[p].addtag)
            {
                __tagpush_add(p*2,t[p].addtag);
                __tagpush_add(p*2+1,t[p].addtag);
                t[p].addtag=0;
            }
            if(t[p].maxtag!=-Inf)
            {
                __tagpush_max(p*2,t[p].maxtag);
                __tagpush_max(p*2+1,t[p].maxtag);
                t[p].maxtag=-Inf;
            }
            if(t[p].mintag!=Inf)
            {
                __tagpush_min(p*2,t[p].mintag);
                __tagpush_min(p*2+1,t[p].mintag);
                t[p].mintag=Inf;
            }
        }

        void build_tree(int p,int l,int r)
        {
            t[p].mintag=Inf;
            t[p].maxtag=-Inf;
            t[p].l=l;
            t[p].r=r;
            if(l==r)
            {
                t[p].sum = t[p].maxval = t[p].minval = a[l];
                t[p].maxval_cnt =t[p].minval_cnt= 1;
                t[p].sec_maxval = -Inf;
                t[p].sec_minval = Inf;
                return ;
            }

            int mid=(l+r)/2;
            build_tree(p*2,l,mid);
            build_tree(p*2+1,mid+1,r);
            __upd(p);
        }

        void modify_add(int p,int L,int R,Ty val)
        {
            if(L<= t[p].l && t[p].r <= R)
            {
                return __tagpush_add(p,val);
            }
            __tagdown(p);
            int mid=(t[p].l + t[p].r)/2;
            if(L<=mid) modify_add(p*2,L,R,val);
            if(mid<R) modify_add(p*2+1,L,R,val);
            __upd(p);
        }

        void modify_min(int p,int L,int R,Ty val)
        {
            if(t[p].maxval > val)
            {
                if(L<=t[p].l && t[p].r<=R && t[p].sec_maxval<val)
                {
                    return __tagpush_min(p, val);
                }
                __tagdown(p);
                int mid=(t[p].l + t[p].r)/2;
                if(L<=mid)
                {
                    modify_min(p*2,L,R,val);
                }
                if(mid<R)
                {
                    modify_min(p*2+1,L,R,val);
                }
                __upd(p);
            }
        }

        void modify_max(int p,int L,int R,Ty val)
        {
            if(t[p].minval < val)
            {
                if(L<=t[p].l && t[p].r<=R && t[p].sec_minval>val)
                {
                    return __tagpush_max(p, val);
                }
                __tagdown(p);
                int mid=(t[p].l + t[p].r)/2;
                if(L<=mid)
                {
                    modify_max(p*2,L,R,val);
                }
                if(mid<R)
                {
                    modify_max(p*2+1,L,R,val);
                }
                __upd(p);
            }
        }

        Ty query_sum(int p,int L,int R)
        {
            if(L<=t[p].l && t[p].r<=R)
            {
                return t[p].sum;
            }
            __tagdown(p);
            int mid=(t[p].l + t[p].r)/2;
            Ty ans=0;
            if(L<=mid)
            {
                ans+=query_sum(p*2,L,R);
            }
            if(mid<R)
            {
                ans+=query_sum(p*2+1,L,R);
            }
            return ans;
        }

        Ty query_max(int p,int L,int R)
        {
            if(L<=t[p].l && t[p].r<=R)
            {
                return t[p].maxval;
            }
            __tagdown(p);
            int mid=(t[p].l + t[p].r)/2;
            Ty ans=-Inf;
            if(L<=mid)
            {
                ans=max(query_max(p*2,L,R),ans);
            }
            if(mid<R)
            {
                ans=max(query_max(p*2+1,L,R),ans);
            }
            return ans;
        }

        Ty query_min(int p,int L,int R)
        {
            if(L<=t[p].l && t[p].r<=R)
            {
                return t[p].minval;
            }
            __tagdown(p);
            int mid=(t[p].l + t[p].r)/2;
            Ty ans=Inf;
            if(L<=mid)
            {
                ans=min(query_min(p*2,L,R),ans);
            }
            if(mid<R)
            {
                ans=min(query_min(p*2+1,L,R),ans);
            }
            return ans;
        }
    };
}
using namespace JSJSEgmentTree;

JSJSegmentTree t;

namespace Fast_IO {

    char __nc()
    {
        static char buf[1000000], *p = buf, *q = buf;
        return p == q && (q = (p = buf) + fread(buf, 1, 1000000, stdin), p == q)
                ? EOF
                : *p++;
    }
    
    template <typename T> inline void read(T &x) {
    char c;
    int sgn = 1;
    x = 0;
    c = __nc();
    while (c > '9' || c < '0') {
        if (c == '-')
        sgn = -1;
        c = __nc();
    }
    while (c <= '9' && c >= '0') {
        x = (x << 3) + (x << 1) + (c - '0');
        c = __nc();
    }
    x *= sgn;
    }

    template <typename T> inline void print(T x) {
    if (x < 0) {
        putchar('-');
        x = -x;
    }
    if (x > 9) {
        print(x / 10);
    }
    putchar(x % 10 + '0');
    }
} // namespace Fast_IO
using namespace Fast_IO;

int main()
{
    read(n);
    for(int i=1;i<=n;i++) read(a[i]);
    t.build_tree(1,1,n);
    read(m);
    for(int i=1;i<=m;i++)
    {
        int op,l,r;
        ll val;
        read(op),read(l),read(r);
        if(op<=3) read(val);
        if(op==1) //add
        {
            t.modify_add(1,l,r,val);
        }
        else if(op==2) //max
        {
            t.modify_max(1,l,r,val);
        }
        else if(op==3) //min
        {
            t.modify_min(1,l,r,val);
        }
        else if(op==4) //sum
        {
            print(t.query_sum(1,l,r));
            putchar('\n');
        }
        else if(op==5) //max
        {
            print(t.query_max(1,l,r));
            putchar('\n');
        }
        else //min
        {
            print(t.query_min(1,l,r));
            putchar('\n');
        }
    }
    return 0;
}
```


# 图论

## 邻接表

图论算法的实现主要依赖于此。除非有特殊说明，否则大部分图论算法的默认输入接口包括此段所述的邻接表。

- 🔥：total、head。

### 带边权的版本

| Funs \|\| Methods | Reliance | Time Complexity | Inputs                   | Outputs     | Description |
| ----------------- | -------- | --------------- | ------------------------ | ----------- | ----------- |
| adde              |          | 1               | x,y,w: x到y的边权为w的边 | edge, total | 添加边      |

```c++
namespace Edges
{
    typedef int Ty;
    const int N=1e4+10,M=2e5+100;

    //public
    int n,m; //n,m: 点、边数量

    struct E
    {
        int from, to, inext;
        Ty w;
    } edge[M];
    int head[N], total;

    void adde(int x, int y, Ty w)
    {
        edge[++total].from = x;
        edge[total].to = y;
        edge[total].w = w;
        edge[total].inext = head[x];
        head[x] = total;
    }
}
using namespace Edges;
```



## LCA

倍增法。

- 🔥：total，head，d，dist

```c++
#include <iostream>
#include <cmath>
// #include <memory.h>
#include <queue>
#include <cstdio>
#include <cstring>
using namespace std;
typedef long long ll;
int T;

namespace Edges
{
    const int N=20010;
    typedef long long Ty;

    //public
    struct E
    {
        int to,inext;
        Ty w;
    }edge[N*2];
    int head[N],total;

    int n,m;

    void adde(int x,int y, Ty w)
    {
        edge[++total].to=y;
        edge[total].w=w;
        edge[total].inext=head[x];
        head[x]=total;
    }
}
using namespace Edges;

namespace LCA
{
    //local
    int d[N],f[N][20],dist[N],t;
    //d:深度
    //f：倍增数组
    //dist:根到第x个点的带权距离
    //t: t=log2(n)+1

    //bfs并预处理倍增数组f
    void bfs(int rt=1)//init
    {
        t=int(log2(n))+1;
        queue<int> q;
        q.push(rt);
        d[rt]=1;
        while(q.size())
        {
            int x=q.front();
            q.pop();
            for(int i=head[x];i;i=edge[i].inext)
            {
                int y=edge[i].to;
                if (d[y]==0)
                {
                    d[y]=d[x]+1;
                    dist[y]=dist[x]+edge[i].w;
                    f[y][0]=x;
                    for(int k=1;k<=t;k++)
                    {
                        f[y][k]=f[f[y][k-1]][k-1];
                    }
                    q.push(y);
                }
            }
        }
    }

    //通过倍增数组得到LCA
    int lca(int x,int y)
    {
        if(d[x]>d[y]) swap(x,y); //d[x]<=d[y]
        //make d[y]=d[x]
        for(int k=t;k>=0;k--)
        {
            if(d[f[y][k]]>=d[x]) y=f[y][k];
        }
        if(x==y) return x;
        //then climb together
        for(int k=t;k>=0;k--)
        {
            if(f[x][k]!=f[y][k]) x=f[x][k],y=f[y][k];
        }
        return f[x][0];
    }

}
using namespace LCA;

int indegree[N];
int main()
{
    ios::sync_with_stdio(false);
    // cin>>T;
    scanf("%d",&T);
    while (T--)
    {
        total=0;
        memset(head,0,sizeof(head));
        memset(d,0,sizeof(d));
        memset(dist,0,sizeof(dist));
        memset(f,0,sizeof(f));
        memset(indegree,0,sizeof(indegree));
        // cin>>n>>m;
        scanf("%d",&n);
        // scanf("%d",&m);

        t=int(log2(n))+1;
        // t=(int)(log(n*1.0)/log(2.0))+1;
        for(int i=1;i<=n-1;i++)
        {
            int x,y;
            // cin>>x>>y;
            scanf("%d",&x);
            scanf("%d",&y);

            adde(x,y,0);
            indegree[y]++;
        }

        for(int i=1;i<=n;i++)
        {
            if(indegree[i]==0)
            {
                bfs(i);
                break;
            }
        }

        int x,y;
        scanf("%d",&x);
        scanf("%d",&y);
        printf("%d\n",lca(x,y));
    }
    return 0;
}
```



## 树链剖分HLD

通过两次dfs实现，第一次dfs标记重子节点、深度、父亲、子树大小；第二次标记dfn（重边优先遍历）、rnk（dfn的反函数，也即通过dfn获得节点原标号）、top（所在重链链顶）。使用线段树维护链上dfn信息。

**路径上维护**：每次选择深度较大的链往上跳，直到两点在同一条链上。

**子树维护**：记录所在子树连续区间末端的结点（他喵的不是直接记录子树dfn最大是谁就行了吗（））

**LCA**：和路径上维护相似，不过当两个节点跳到同一链时，取深度较小节点作为LCA

- 🤫：dfs、dfs2、init_wdfn、build_tree
- 🔥：total、head、dfn、node、w_dfn
- 注意线段树上可能也要清空（1~4n，我不知道是什么情况……）

```c++
//HLD [P2590 [ZJOI2008\]树的统计]
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

namespace Edges
{
    typedef int Ty;
    const int N=5e5+10,M=2e5+10;

    //public
    int n,m; //n,m: 点、边数量

    struct E
    {
        int to, inext;
        // Ty w;
    } edge[M];
    int head[N], total;

    void adde(int x, int y)
    {
        edge[++total].to = y;
        // edge[total].w = w;
        edge[total].inext = head[x];
        head[x] = total;
    }
}
using namespace Edges;

namespace SEGMENT_TREE
{
    typedef int Ty;
    const Ty INF=1145141919;

    Ty w_dfn[N]; //w_dfn: 由dfn到权值的映射

    //维护dfn用的线段树
    struct Segment_tree
    {
        struct SEGMENT_TREE
        {
            Ty maxval,sum;
            int l,r;
        }t[N*4]; //维护关于dfn的信息

        void __upd(int p)
        {
            t[p].maxval=max(t[p*2].maxval,t[p*2+1].maxval);
            t[p].sum=t[p*2].sum+t[p*2+1].sum;
        }

        //建树
        void build_tree(int p,int l,int r)
        {
            t[p].l=l;
            t[p].r=r;
            if(l==r)
            {
                t[p].maxval=t[p].sum=w_dfn[l];
                return ;
            }
            int mid=(l+r)/2;
            build_tree(p*2,l,mid);
            build_tree(p*2+1,mid+1,r);
            __upd(p);
        }

        //单点修改
        void singlechange(int p,int pos,Ty val)
        {
            if(t[p].l == t[p].r )
            {
                w_dfn[t[p].l]=val;
                t[p].maxval=t[p].sum=w_dfn[t[p].l];
                return ;
            }
            int mid=(t[p].l+t[p].r)/2;
            if(pos<=mid) singlechange(p*2,pos,val);
            else singlechange(p*2+1,pos,val);
            __upd(p);
        }

        //询问区间最大值
        int ask_maxval(int p,int l,int r)
        {
            if(l<=t[p].l && t[p].r<=r)
            {
                return t[p].maxval;
            }
            Ty ans=-INF;
            int mid=(t[p].l+t[p].r)/2;
            if(l<=mid) ans=max(ans,ask_maxval(p*2,l,r));
            if(mid<r) ans=max(ans,ask_maxval(p*2+1,l,r));

            return ans;
        }

        //询问区间和
        int ask_sum(int p,int l,int r)
        {
            if(l<=t[p].l && t[p].r<=r)
            {
                return t[p].sum;
            }
            Ty ans=0;
            int mid=(t[p].l+t[p].r)/2;
            if(l<=mid) ans+=ask_sum(p*2,l,r);
            if(mid<r) ans+=ask_sum(p*2+1,l,r);
            return ans;
        }
    }tree;
}
using namespace SEGMENT_TREE;


namespace HLD
{
    typedef int Ty;

    struct NODE
    {
        int fa,hson,top,size,depth,dfn;
        Ty w;
    }node[N];

    //local
    int dfn=0; //dfn:第二次dfs需要使用dfn

    //第一次dfs：标记重子节点、深度、父亲、子树大小
    void dfs(int x,int depth)
    {
        node[x].depth=depth;
        node[x].hson=0;
        node[x].size=1;
        for(int e=head[x];e;e=edge[e].inext)
        {
            int y=edge[e].to;
            if(y==node[x].fa)
                continue;
            node[y].fa=x;
            dfs(y,depth+1);
            node[x].size+=node[y].size;
            if(node[x].hson==0 || node[node[x].hson].size< node[y].size)
            {
                node[x].hson=y;
            }
        }
    }

    //第二次dfs：标记dfn（重边优先遍历）、rnk（dfn的反函数，也即通过dfn获得节点原标号）、top（所在重链链顶）
    void dfs2(int x,int top)
    {
        node[x].top=top;
        dfn++;
        node[x].dfn=dfn;
        if(node[x].hson)
        {
            dfs2(node[x].hson,top);
            for(int e=head[x];e;e=edge[e].inext)
            {
                int y=edge[e].to;
                if(y==node[x].fa || y==node[x].hson)
                    continue;
                dfs2(y,y);
            }
        }
    }

    //取得x、y路径上的和
    Ty get_sum(int x,int y)
    {
        Ty ans=0;
        while(node[x].top != node[y].top)
        {
            if(node[node[x].top].depth < node[node[y].top].depth)
            {
                swap(x,y);
            }
            ans+=tree.ask_sum(1,node[node[x].top].dfn,node[x].dfn);
            x=node[node[x].top].fa;
        }
        if(node[x].depth<node[y].depth)
            swap(x,y);
        ans+=tree.ask_sum(1,node[y].dfn,node[x].dfn);
        return ans;
    }

    //取得x、y路径上的最大值
    Ty get_max(int x,int y)
    {
        Ty ans=-INF;
        while (node[x].top!=node[y].top)
        {
            if(node[node[x].top].depth<node[node[y].top].depth)
                swap(x,y);
            ans=max(tree.ask_maxval(1,node[node[x].top].dfn,node[x].dfn),ans); //!!
            x=node[node[x].top].fa;
        }
        if(node[x].depth<node[y].depth)
            swap(x,y);
        ans=max(tree.ask_maxval(1,node[y].dfn,node[x].dfn),ans);
        return ans;
    }

    //预存点权到dfn权上
    void init_wdfn()
    {
        for(int i=1;i<=n;i++)
        {
            w_dfn[node[i].dfn]=node[i].w;
        }
        // tree.build_tree(1,1,n);
    }
}
using namespace HLD;


int main()
{
    scanf("%d",&n);
    for(int i=1;i<=n-1;i++)
    {
        int x,y;
        scanf("%d%d",&x,&y);
        adde(x,y);
        adde(y,x);
    }
    dfs(1,1);
    dfs2(1,1);
    for(int i=1;i<=n;i++)
    {
        //Ty
        scanf("%d",&node[i].w);
    }
    init_wdfn();
    tree.build_tree(1,1,n);

    int q;
    scanf("%d",&q);
    for(int i=1;i<=q;i++)
    {
        char op[10];
        int x,y;
        scanf("%s",op);
        scanf("%d%d",&x,&y);
        if(op[0]=='C')
        {
            tree.singlechange(1,node[x].dfn,y);
        }
        else if(op[1]=='M')
        {
            // cout<<get_max(x,y)<<endl;
            printf("%d\n",get_max(x,y));
        }
        else if(op[1]=='S')
        {
            // cout<<get_sum(x,y)<<endl;
            printf("%d\n",get_sum(x,y));
        }
    }
    return 0;
}
```



## 无向图EDCC

```c++
//无向图的桥、边双联通分量
// POJ 3694
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

namespace Edges
{
    typedef int Ty;
    const int N=1e5+10,M=4e5+10;

    //public
    int n,m; //n,m: 点、边数量

    struct E
    {
        int to, inext;
        bool is_cut;
        // is_cut: 是否为割边
    } edge[M];
    int head[N], total=1; //⚠️注意：从1开始计数！
    
    //新图中的边
    E edge_c[M];
    int head_c[N], total_c;

    void adde(int x, int y)
    {
        edge[++total].to = y;
        edge[total].is_cut = false;
        edge[total].inext = head[x];
        head[x] = total;
    }

    void adde_c(int x,int y)
    {
        edge_c[++total_c].to=y;
        edge_c[total_c].inext=head_c[x];
        head_c[x]=total_c;
    }
}
using namespace Edges;

namespace EDCC
{
    struct Node
    {
        int dfn,low,c; 
        //dfn,low：dfn和最小可追溯值
        //c: 点所属的edcc编号
    }node[N];

    //public
    int dfn=0;
    int edcc_cnt=0;
    //dfn:全局dfn编号计数器
    //edcc_cnt：在第二次dfs过程中计算edcc数目

    //tarjan求出桥. in_edge:进入点x的边在原图中的下标，用于阻止遍历反向边
    void tarjan(int x,int in_edge)
    {
        node[x].dfn=node[x].low=++dfn;
        for(int e=head[x];e;e=edge[e].inext)
        {
            int y=edge[e].to;
            if(!node[y].dfn)
            {
                tarjan(y,e);
                node[x].low=min(node[x].low,node[y].low);
                if(node[y].low>node[x].dfn)
                {
                    edge[e].is_cut=edge[e^1].is_cut=true;
                }
            }
            else if(e!=(in_edge^1))
            {
                node[x].low=min(node[x].low,node[y].dfn);
            }
        }
    }

    //对所有点做一次tarjan
    void tarjan_forall(int n=n)
    {
        for(int i=1;i<=n;i++)
        {
            if(node[i].dfn==0)
            {
                tarjan(i,-1);
            }
        }
    }

    //int edcc_cnt=0;
    //dfs标记各点所属的edcc
    void __edcc_dfs(int x)
    {
        node[x].c=edcc_cnt;
        for(int e=head[x];e;e=edge[e].inext)
        {
            if(edge[e].is_cut) continue;
            int y=edge[e].to;
            if(node[y].c) continue;
            __edcc_dfs(y);
        }
    }

    //dfs标记各点所属的edcc（对所有点）
    void edcc_forall(int n=n)
    {
        for(int i=1;i<=n;i++)
        {
            if(node[i].c)
            {
                edcc_cnt++;
                __edcc_dfs(i);
            }
        }
    }

    //edcc缩点：在新图上建图
    void edcc_suodian()
    {
        for(int i=2;i<=total;i++) //注意这里遍历的是边
        {
            int x=edge[i].to,y=edge[i^1].to;
            if(node[x].c == node[y].c) continue;
            adde_c(node[x].c,node[y].c);
        }
    }
}
using namespace EDCC;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    return 0;
}
```



## 无向图VDCC（点双）

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

namespace Edges
{
    typedef int Ty;
    const int N=6e5+10,M=6e5+10;

    //public
    int n,m; //n,m: 点、边数量

    struct E
    {
        int to, inext;
    } edge[M];
    int head[N], total=1; //⚠️注意：从1开始计数！

    //新图中的边
    E edge_c[M];
    int head_c[N], total_c;

    void adde(int x, int y)
    {
        edge[++total].to = y;
        edge[total].inext = head[x];
        head[x] = total;
    }

    void adde_c(int x,int y)
    {
        edge_c[++total_c].to=y;
        edge_c[total_c].inext=head_c[x];
        head_c[x]=total_c;
    }

}
using namespace Edges;

namespace VDCC
{
    struct Node
    {
        int dfn,low; 
        int c;
        bool is_cutpoint;
        //dfn,low：dfn和最小可追溯值
        //is_cutpoint: 是割点标记
        
        //c: 一般点所属的edcc编号 / 割点在新图中所属的点的编号
    }node[N];

    //public
    int dfn=0;
    int vdcc_cnt=0;
    vector<int> vdcc[N];
    //dfn:全局dfn编号计数器
    //vdcc_cnt：在第二次dfs过程中计算vdcc数目
    //vdcc: 保存所有vdcc中点的编号的vector集合

    //local
    int st[N],st_p;
    //st:栈

    //tarjan求出割点，同时顺带求出vdcc
    void tarjan(int x,int root) //for vdcc
    {
        node[x].dfn=node[x].low=++dfn;
        if(x==root && head[x]==0)
        {
            vdcc[++vdcc_cnt].push_back(x);
            // single[x]=true;
            return ;
        }
        st[++st_p]=x;
        int flag=0;
        for(int e=head[x];e;e=edge[e].inext)
        {
            int y=edge[e].to;
            if(!node[y].dfn)
            {
                tarjan(y,root);
                node[x].low=min(node[x].low,node[y].low);
                if(node[x].dfn<=node[y].low)
                {
                    flag++;
                    if(x!=root || flag>1) node[x].is_cutpoint=true;
                    vdcc_cnt++;
                    int z;
                    do
                    {
                        z=st[st_p--];
                        vdcc[vdcc_cnt].push_back(z);
                    } while (z!=y);
                    vdcc[vdcc_cnt].push_back(x);
                }
            }
            else
                node[x].low=min(node[x].low,node[y].dfn);
        }
    }

    //一般的求割点，不求出vdcc
    void tarjan2(int x,int root,int in_edge=-1)
    {
        node[x].dfn=node[x].low=++dfn;
        int flag=0;
        for(int e=head[x];e;e=edge[e].inext)
        {
            int y=edge[e].to;
            if(!node[y].dfn)
            {
                tarjan2(y,root,in_edge);
                node[x].low=min(node[x].low,node[y].low);
                if(node[x].dfn<=node[y].low)
                {
                    flag++;
                    if((x==root && flag>=2) || (x!=root))
                    {
                        node[x].is_cutpoint=true;
                    }
                }
            }
            else if(e!=(in_edge^1))
            //或者直接else
                node[x].low=min(node[x].low,node[y].dfn);
        }
    }

    //tarjan求出割点和vdcc（对所有点）
    void tarjan_forall(int n=n)
    {
        for(int i=1;i<=n;i++)
        {
            if(node[i].dfn==0)
            {
                tarjan(i,i);
            }
        }
    }

    //vdcc缩点：在新图上建图
    //为所有割点的c新分配编号(vdcc_cnt+1 ~ vdcc_cnt*2)，其他点的c保存自己所属的vdcc编号
    //新的图会是以各个割点为中心连接到各个vdcc缩成的点（用vdcc编号表示）的图。
    void vdcc_suodian()
    {
        int num=vdcc_cnt;
        for(int i=1;i<=n;i++)
        {
            if(node[i].is_cutpoint)
                node[i].c=++num; // node[i].new_id
        }

        total_c=1;
        for(int i=1;i<=vdcc_cnt;i++)
        {
            for(auto x:vdcc[i])
            {
                if(node[x].is_cutpoint)
                {
                    adde_c(i,node[x].c);
                    adde_c(node[x].c,i);
                }
                else node[x].c=i;
            }
        }
    }
}
using namespace VDCC;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    return 0;
}
```



## 有向图tarjan缩点

```c++

// LUOGU 3387 缩点 (有向图tarjan缩点)
#include <iostream>
#include <vector>
#include <queue>
using namespace std;
const int N=2e4+5,M=2e5+5;
struct E
{
    int to,inext;
}edge[M],edge_c[M];
int head[N],total;
int head_c[N],total_c;
int n,m,a[N];
//vector<int> scc[N];
int st[N],st_p,cnt,c[N],sum[N]; //stack, scc_count, scc_sum
int dfn[N],low[N],dfn_cnt;
bool inst[N]; //in stack
int in_degree[N];

void adde(int x,int y)
{
    edge[++total].to=y;
    edge[total].inext=head[x];
    head[x]=total;
}

void adde_c(int x,int y)
{
    edge_c[++total_c].to=y;
    edge_c[total_c].inext=head_c[x];
    head_c[x]=total_c;
}

void tarjan(int x)
{
    dfn[x]=low[x]=++dfn_cnt;
    st[++st_p]=x; inst[x]=true;
    for(int e=head[x];e;e=edge[e].inext)
    {
        int y=edge[e].to;
        if(!dfn[y])
        {
            tarjan(y);
            low[x]=min(low[x],low[y]);
        }
        else if(inst[y])
            low[x]=min(low[x],dfn[y]);
        
    }
    if(dfn[x]==low[x]) //scc judge success
    {
        cnt++; //scc_cnt++
        while (st[st_p]!=x)
        {
            int y=st[st_p];
            //scc[cnt].emplace_back(y);
            inst[y]=false;
            c[y]=cnt;
            sum[cnt]+=a[y];
            st_p--;
        }
        //x
        //scc[cnt].emplace_back(x);
        inst[x]=false;
        c[x]=cnt;
        sum[cnt]+=a[x];
        st_p--;
    }
}

void creat_new_graph()
{
    for(int i=1;i<=n;i++)
    {
        for(int e=head[i];e;e=edge[e].inext)
        {
            int y=edge[e].to;
            if(c[i]==c[y]) continue; //same scc
            else 
            {
                adde_c(c[i],c[y]);
                in_degree[c[y]]++;
            }
        }
    }
}

int f[N],ans=0;
void topo() //for Graph2
{
    queue<int> q;
    for(int i=1;i<=cnt;i++)
    {
        if(!in_degree[i])
        {
            q.push(i);
            f[i]=sum[i];
            ans=max(ans,f[i]);
        }
    }

    while (q.size())
    {
        int x=q.front();
        q.pop();

        for(int e=head_c[x];e;e=edge_c[e].inext)
        {
            int y=edge_c[e].to;
            f[y]=max(f[y],f[x]+sum[y]);
            ans=max(ans,f[y]);
            in_degree[y]--;
            if(!in_degree[y])
                q.push(y);
        }
    }
}

int main()
{
    ios::sync_with_stdio(false);
    cin>>n>>m;
    for(int i=1;i<=n;i++)
        cin>>a[i];
    for(int i=1;i<=m;i++)
    {
        int x,y;
        cin>>x>>y;
        adde(x,y);
    }
    for(int i=1;i<=n;i++)
    {
        if(!dfn[i]) tarjan(i);
    }
    creat_new_graph();
    topo();
    cout<<ans<<endl;
    return 0;
}
```





## 匈牙利

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

namespace Edges
{
    typedef int Ty;
    const int N=1e4+10,M=2e5+100;

    //public
    int n,m; //n,m: 点、边数量

    struct E
    {
        int  to, inext;
        Ty w;
    } edge[M];
    int head[N], total;

    void adde(int x, int y, Ty w)
    {
        edge[++total].to = y;
        edge[total].w = w;
        edge[total].inext = head[x];
        head[x] = total;
    }
}
using namespace Edges;


namespace Hungary
{
    const int N=1e4+10;

    //local
    bool vis[N];
    int match[N];
    //vis:匈牙利算法中用于标记节点是否走过
    //match:二分图的右侧节点对应的左侧节点匹配谁（如果是在同一张图中使用统一编号则也可保存左侧点的匹配情况）
    //附注：原算法里面左侧节点的编号和右侧节点的标号是可以相同的。换句话说把左侧和右侧看成了两个不同的图

    //寻找一条增广路并改变匹配
    bool __dfs(int x)
    {
        for(int e=head[x];e;e=edge[e].inext)
        {
            int y=edge[e].to;
            if(!vis[y])
            {
                vis[y]=true;
                if(!match[y] || __dfs(match[y]))
                {
                    match[y]=x;
                    // match[x]=y; //能使用当且仅当所有点编号唯一（左侧点与右侧点编号不会相同）
                    return true;
                }
            }
        }
        return false;
    }

    void __clear_visit(int n=n)
    {
        for(int i=1;i<=n*2;i++) vis[i]=false; //注意要改范围
    }

    //二分图匹配
    int do_Hungary()
    {
        int ans=0;
        for(int i=1;i<=n;i++) //二分图的一侧（左侧）有n个点，编号为1~n
        {
            __clear_visit();
            if(__dfs(i)) ans++;
        }   
        return ans;
    }
}
using namespace Hungary;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    return 0;
}
```

## KM-BFS

```c++
//2021牛客多校5 J Jewels
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
// typedef long long ll;
using ll=long long;
// const int N=2e5+10;

namespace KMKM
{
    using Ty=ll;
    const int N=310;
    const Ty Inf=1e18;  

    int n;
    Ty g[N][N];
    //g：带权左右子图（二分图，左右两边下标都是从1开始）

    int matchx[N],matchy[N];
    int pre[N];
    bool visx[N],visy[N];
    Ty lx[N],ly[N];
    Ty slack[N];
    //match:匹配点映射集合。要求一定是完美匹配，因此一定存在
    //slack：松弛数组，记录了delta值
    //lx、ly：标号数组，参见李煜东蓝书

    //初始化标号数组的值
    void __init(int n)
    {
        for(int i=1;i<=n;i++)
        {
            lx[i]=ly[i]=0;
            for(int j=1;j<=n;j++)
            {
                lx[i]=max(lx[i],g[i][j]);
            }
        }
    }

    bool __check(int y, queue<int> &q)
    {
        visy[y]=1;
        if(matchy[y]!=0)
        {
            q.push(matchy[y]);
            visx[matchy[y]]=1;
            return false;
        }
        while(y!=0)
        {
            matchy[y]=pre[y];
            swap(y,matchx[pre[y]]);
        }
        return true;
    }

    void __bfs(int u)
    {
        queue<int> q;
        q.push(u);
        visx[u]=1;
        while(true)
        {
            while(q.size())
            {
                int x=q.front();
                q.pop();
                for(int y=1;y<=n;y++)
                {
                    if(visy[y]==0)
                    {
                        Ty delta=lx[x]+ly[y]-g[x][y];
                        if(slack[y]>=delta)
                        {
                            pre[y]=x;
                            if(delta)
                            {
                                slack[y]=delta;
                            }
                            else if(__check(y,q))
                            {
                                return;
                            }
                        }
                    }
                }
            }

            Ty delta=Inf;
            for(int j=1;j<=n;j++)
            {
                if(visy[j]==0)
                {
                    delta=min(delta,slack[j]);
                }
            }
            for(int j=1;j<=n;j++)
            {
                if(visx[j])
                {
                    lx[j]-=delta;
                }
                if(visy[j])
                {
                    ly[j]+=delta;
                }
                else
                {
                    slack[j]-=delta;
                }
            }
            for(int j=1;j<=n;j++)
            {
                if(visy[j]==0 && slack[j]==0 && __check(j,q))
                {
                    return;
                }
            }

        }
    }

    //最大权值匹配。要想取得最小权值匹配，将权值取反。
    void KM()
    {
        __init(n);
        for(int i=1;i<=n;i++)
        {
            fill(slack,slack+1+n,Inf);
            fill(visx,visx+1+n,0);
            fill(visy,visy+1+n,0);
            __bfs(i);
        }
    }

}
using namespace KMKM;


struct J
{
    ll x, y, z, v;
} a[310];

ll gw(ll t, int i)
{
    return a[i].v * a[i].v * t * t + a[i].z * 2 * a[i].v * t;
}


int main()
{

    scanf("%d", &n);
    ll ans = 0;
    for (int i = 1; i <= n; i++)
    {
        // cin>>a[i].x
        scanf("%lld", &a[i].x);
        scanf("%lld", &a[i].y);
        scanf("%lld", &a[i].z);
        scanf("%lld", &a[i].v);
        ans += a[i].x * a[i].x;
        ans += a[i].y * a[i].y;
        ans += a[i].z * a[i].z;
    }


    for (int i = 1; i <= n; i++) //t=0~n-1
    {
        ll t = i - 1;
        for (int j = 1; j <= n; j++)
        {
            ll val = gw(t, j);
            g[i][j]=-val;
        }
    }

    KM();

    for(int i=1;i<=n;i++)
    {
        ans+=-g[i][matchx[i]];
    }   
    printf("%lld\n",ans);

    return 0;
}

```


## 网络流：Edmonds Karp

| Funs \|\| Methods | Reliance | Time Complexity | Inputs                                                 | Outputs                                           | Description                                   |
| ----------------- | -------- | --------------- | ------------------------------------------------------ | ------------------------------------------------- | --------------------------------------------- |
| __bfs             |          | n               | Edges：残差网络。S、T：源点和汇点                      | incf：源点到点x的最小剩余容量。pre：点x的前驱数组 | 遍历残差网络找到最长增广路。                  |
| __update          |          | m               | Edges：残差网络；incf、pre：同上输出。S、T：源点和汇点 | 返回此次更新的流量大小。Edges：更新后的残差网络。 | 从汇点t开始倒着更新增广路以及反向边的剩余容量 |
| get_maxflow       |          | nm^2            | s,t：源点与汇点                                        | 最大流大小                                        | 取得最大流                                    |

```c++
//网络流：Edmonds_Karp算法
#include <iostream>
#include <queue>
#include <memory.h>
using namespace std;
typedef long long ll;
namespace Edges
{
    typedef ll Ty;
    const int N=1e4+10,M=2e5+100;

    //public
    int n,m; //n,m: 点、边数量

    //残差网络的边
    struct E
    {
        int from, to, inext;
        Ty w;
    } edge[M];
    int head[N], total;

    void adde(int x, int y, Ty w)
    {
        edge[++total].from = x;
        edge[total].to = y;
        edge[total].w = w;
        edge[total].inext = head[x];
        head[x] = total;
    }
}
using namespace Edges;

namespace Edmonds_Karp
{
    typedef ll Ty;
    const int N=1e4+10; //N：点个数
    const Ty Inf=1e18; //Inf：流量无穷大值
    //public
    int S,T; //S,T:源点和汇点

    //local
    bool v[N]; //v:__bfs时使用的标记遍历数组
    int pre[N]; //pre：__bfs时使用的记录瓶颈路前驱转移方向的数组
    Ty incf[N]; //incf：增广路

    //遍历残差网络找到最长增广路。从汇点t开始倒着更新增广路以及反向边的剩余容量
    bool __bfs(int s, int t)
    {
        memset(v, 0, sizeof(v));
        queue<int> q;
        q.push(s);
        v[s] = 1;
        incf[s] = Inf;
        while (q.size())
        {
            int x = q.front();
            q.pop();
            for (int e = head[x]; e; e = edge[e].inext)
            {
                int y = edge[e].to;
                if (edge[e].w && !v[y])
                {
                    incf[y] = min(incf[x], edge[e].w);
                    pre[y] = e;
                    v[y] = 1;
                    q.push(y);
                    if (y == t)
                        return true;
                }
            }
        }
        return false;
    }

    //从汇点t开始倒着更新增广路以及反向边的剩余容量
    Ty __update(int s, int t)
    {
        int x = t;
        while (x != s)
        {
            int e = pre[x];
            edge[e].w -= incf[t];
            if (e % 2)
                edge[e + 1].w += incf[t];
            else
                edge[e - 1].w -= incf[t];
            x = edge[e].from;
        }
        return incf[t];
    }

    //取得最大流
    Ty get_maxflow(int s=S,int t=T)
    {
        Ty maxflow = 0;
        while (__bfs(s, t))
            maxflow += __update(s, t);
        return maxflow;
    }
}

using namespace Edmonds_Karp;

int main()
{
    ios::sync_with_stdio(false);
    cin >> n >> m >> S >> T;
    for (int i = 1; i <= m; i++)
    {
        int x, y, w;
        cin >> x >> y >> w;
        adde(x, y, w);
        adde(y, x, 0);
    }
    ll maxflow=get_maxflow(S,T);
    cout << maxflow << endl;
    return 0;
}
```

## 网络流：Dinic

别忘了加边的时候加反向边。

10.12更新：现在更新为带当前弧优化的版本，参见注释

~~（⭐：这玩意还有个最短弧优化我没整……有待进一步改进）~~

| Funs \|\| Methods | Reliance | Time Complexity | Inputs                                           | Outputs                                           | Description                           |
| ----------------- | -------- | --------------- | ------------------------------------------------ | ------------------------------------------------- | ------------------------------------- |
| __bfs             |          | n               | Edges：残差网络。S、T：源点和汇点                | d：层次数组                                       | 取得当前残差网络从s触发的各个点的层次 |
| __dinic           |          | n               | Edges：残差网络。d：层次数组。S、T：源点和汇点。 | 返回此次更新的流量大小。Edges：更新后的残差网络。 | 在分层图上更新最大流并返回更新值      |
| get_maxflow       |          | (n^2)*m         | s,t：源点与汇点。                                | 最大流大小                                        | 取得最大流                            |

```c++
//网络流：Dinic算法
//带当前弧优化
#include <iostream>
#include <memory.h>
#include <queue>
using namespace std;
typedef long long ll;
namespace Edges
{
    typedef ll Ty;
    const int N=1e4+10,M=2e5+100;

    //public
    int n,m; //n,m: 点、边数量

    //残差网络的边
    struct E
    {
        int from, to, inext;
        Ty w;
    } edge[M];
    int head[N], total;

    void adde(int x, int y, Ty w)
    {
        edge[++total].from = x;
        edge[total].to = y;
        edge[total].w = w;
        edge[total].inext = head[x];
        head[x] = total;
    }
}
using namespace Edges;


namespace Dinic
{
    typedef ll Ty;
    const int N=1e4+10; //N：点个数
    const Ty Inf=1e18; //Inf：流量无穷大值
    //public
    int S,T; //S,T:源点和汇点

    //local
    int d[N]; //d：层次数组
    int cur_head[N]; // cur_head: 用于当前弧优化

    //取得当前残差网络从s触发的各个点的层次
    bool __bfs(int s, int t)
    {
        memset(d, 0, sizeof(d));
        memcpy(cur_head, head,sizeof(head)); //用于当前弧优化
        queue<int> q;
        q.push(s);
        d[s] = 1;
        while (q.size())
        {
            int x = q.front();
            q.pop();
            // for (int e = head[x]; e; e = edge[e].inext)
            for(int e=cur_head[x];e;e=edge[e].inext)
            {
                cur_head[x]=e; //更新当前弧：原因是一条边增广一次后就不会再次增广了，所以下次增广时不需要再考虑这条边。
                int y = edge[e].to;
                if (edge[e].w && !d[y])
                {
                    q.push(y);
                    d[y] = d[x] + 1;
                    if (y == t)
                        return true;
                }
            }
        }
        return false;
    }

    // 在分层图上更新最大流并返回更新值
    Ty __dinic(int x, Ty flow, int t)
    {
        if (x == t)
            return flow;
        Ty rest = flow, delta;
        for (int e = head[x]; e; e = edge[e].inext)
        {
            if (rest == 0)
                return flow; //!!!
            if (edge[e].w && d[edge[e].to] == d[x] + 1)
            {
                delta = __dinic(edge[e].to, min(edge[e].w, rest), t);
                if (!delta)
                    d[edge[e].to] = 0; //pruning
                edge[e].w -= delta;
                if (e % 2)
                    edge[e + 1].w += delta;
                else
                    edge[e - 1].w += delta; //+= ?
                rest -= delta;
            }
        }
        return flow - rest;
    }

    // 取得最大流
    Ty get_maxflow(int s=S,int t=T)
    {
        Ty maxflow = 0;
        while (__bfs(s, t))
        {
            Ty flow;
            while (flow = __dinic(s, Inf, t))
                maxflow += flow;
        }
        return maxflow;
    }
}

using namespace Dinic;

int main()
{
    ios::sync_with_stdio(false);
    cin >> n >> m >> S >> T;
    for (int i = 1; i <= m; i++)
    {
        int x, y, w;
        cin >> x >> y >> w;
        adde(x, y, w); //别忘了加反向边！
        adde(y, x, 0);
    }
    ll maxflow = get_maxflow(S,T);
    cout << maxflow << endl;
    return 0;
}
```



## 网络流：HLPP（最高标号预流推进算法）

⚠️注意：需要将total初始化为1。

| Funs \|\| Methods | Reliance | Time Complexity | Inputs                                           | Outputs                                                              | Description                                 |
| ----------------- | -------- | --------------- | ------------------------------------------------ | -------------------------------------------------------------------- | ------------------------------------------- |
| __ht_init         |          | n               | Edges：残差网络；s、t：源点和汇点                | 返回值表示s、t是否连通。ht：高度数组。                               | 从t开始计算各点高度。返回值表示s、t是否连通 |
| __gap_init        |          | n               | ht：高度数组；s、t：源点和汇点                   | gap：统计对应高度个数的点数的数组                                    | 统计对应高度个数的点数                      |
| __s_init          |          | s相邻点数       | Edges：残差网络；s、t：源点和汇点                | pq：最高标号优先队列。vis：访问数组。                                | 用源点s相邻的点初始化优先队列               |
| __push            |          | u相邻点数       | Edges：残差网络；s、t：源点和汇点。u：节点标号。 | ex：超额流数组。vis：访问数组（是否在优先队列中）。Edges：残差网络。 | 将点u向着相邻点推送超额流                   |
| __relabel         |          | u相邻点数       | Edges：残差网络；ht：高度数组；u：节点标号。     | ht：高度数组                                                         | 为点u重贴高度标签                           |
| get_maxflow       |          | n^2*sqrt(m)     | s,t：源点与汇点                                  | 最大流大小                                                           | 取得最大流                                  |

```c++
#include <iostream>
#include <memory.h>
#include <queue>
using namespace std;
typedef long long ll;
namespace Edges
{
    typedef ll Ty;
    const int N=2e4+10,M=4e5+100;

    //public
    int n,m; //n,m: 点、边数量

    //残差网络的边
    struct E
    {
        int from, to, inext;
        Ty w;
    } edge[M];
    int head[N], total=1; //total：应当初始化为1

    void adde(int x, int y, Ty w)
    {
        edge[++total].from = x;
        edge[total].to = y;
        edge[total].w = w;
        edge[total].inext = head[x];
        head[x] = total;
    }
}
using namespace Edges;

namespace HLPP {
    typedef ll Ty;
    const int N=2e4+10;  //N：点个数
    const int Inf2=0x3f3f3f3f; //inf2：高度无穷大值
    const Ty Inf=1e18;  //Inf：流量无穷大值

    //public
    int S,T; //S,T:源点和汇点

    //local
    int ht[N],gap[N]; //ht, gap：高度和统计对应高度有多少个数的gap数组
    Ty ex[N]; //ex：超额流
    bool vis[N]; //vis：标记某点是否被遍历
    struct __cmp
    {
        bool operator()(int a,int b) const
        {
            return ht[a]<ht[b];
        }
    }; //伪装比较函数类
    priority_queue<int,vector<int>,__cmp> pq; //pq：优先队列，用于取出最大标号的点
    //从t开始计算各点高度。返回值表示s、t是否连通
    bool __ht_init(int s=S,int t=T)
    {
        memset(ht, 0x3f, sizeof(ht));
        queue<int> q;
        q.push(t);
        ht[t]=0;
        while (q.size()) 
        {
            int u=q.front();
            q.pop();
            for(int e=head[u];e;e=edge[e].inext)
            {
                int y=edge[e].to;
                if(edge[e^1].to && ht[y]>ht[u]+1)
                {
                    ht[y]=ht[u]+1;
                    q.push(y);
                }
            }
        }
        return ht[s]!=Inf2;
    }
    //统计对应高度个数的点数
    void __gap_init()
    {
        memset(gap,0,sizeof(gap));
        for(int i=1;i<=n;i++)
        {
            if(ht[i]!=Inf2)
            {
                gap[ht[i]]++;
            }
        }
    }
    //用源点s相邻的点初始化优先队列
    void __s_init(int s=S,int t=T)
    {
        memset(ex,0,sizeof(ex));
        for(int e=head[s];e;e=edge[e].inext)
        {
            int y=edge[e].to;
            Ty w=edge[e].w;
            if(w==0)
                continue;
            ex[s]-=w;
            ex[y]+=w;
            edge[e].w-=w;
            edge[e^1].w+=w;
            if(y!=s && y!=t && vis[y]==0)
            {
                pq.push(y);
                vis[y]=true;
            }
        }
    }
    //将点u向着相邻点推送超额流
    bool __push(int u,int s=S,int t=T)
    {
        for(int e=head[u];e;e=edge[e].inext)
        {
            int y=edge[e].to;
            Ty w=edge[e].w;
            if(w==0 || ht[u]!=ht[y]+1)
                continue;
            Ty k=min(w,ex[u]);

            ex[u]-=k;
            ex[y]+=k;
            edge[e].w-=k;
            edge[e^1].w+=k;
            if(y!=s && y!=t && vis[y]==0)
            {
                pq.push(y);
                vis[y]=true;
            }
            if(ex[u]==0)
            {
                return false;
            }
        }
        return true;
    }
    // 为点u重贴高度标签
    void __relabel(int u)
    {
        ht[u]=Inf2;
        for(int e=head[u];e;e=edge[e].inext)
        {
            int y=edge[e].to;
            Ty w=edge[e].w;
            if(w)
            {
                ht[u]=min(ht[u],ht[y]+1);
            }
        }
        //ht[u]++;
    }
    //取得最大流
    Ty get_maxflow(int s=S,int t=T)
    {
        if(__ht_init(s,t)==0) // 图不连通
            return 0;
        ht[s]=n; //初始化s的高度
        while (pq.size()) { //清空队列
            pq.pop();
        }

        __gap_init();
        __s_init(s,t);
        while (pq.size()) 
        {
            int u=pq.top();
            pq.pop();
            vis[u]=0;
            while (__push(u,s,t)) 
            {
                if(--gap[ht[u]]==0)
                {
                    for(int i=1;i<=n;i++)
                    {
                        //gap optimization
                        //在算法的某一时刻，h(u)=t 的结点个数为0，那么对于h(i)>t的结点i就永远无法推送超额流到t，因此只能送回s，那么我们就在这时直接让他们的高度变成n+1，以尽快推送回s，减少重贴标签的操作。
                        if(i!=s && i!=t && ht[i]>ht[u] && ht[i]<n+1) 
                        {
                            ht[i]=n+1;
                        }
                    }
                }
                __relabel(u);
                gap[ht[u]]++;
            }
        }
        return ex[t];
    }

}

int main()
{
    ios::sync_with_stdio(false);
    cin >> n >> m >> HLPP::S >> HLPP::T;
    for (int i = 1; i <= m; i++)
    {
        int x, y;
        ll w;
        cin >> x >> y >> w;
        adde(x, y, w); //别忘了加反向边！
        adde(y, x, 0);
    }
    ll maxflow = HLPP::get_maxflow(HLPP::S,HLPP::T);
    cout << maxflow << endl;
    return 0;
}
```

## ⭐最小费用最大流（MCMF）：EK

- ⚠️注意：这玩意不能直接用于有负环（费用负环）的图中。

```c++
//最小费用最大流
#include <iostream>
#include <memory.h>
#include <queue>
#include <utility>
using namespace std;
typedef long long ll;
namespace Edges
{
    typedef int Ty;
    const int N=5e3+10,M=1.2e5+100;

    //public
    int n,m; //n,m: 点、边数量

    //残差网络的边
    struct E
    {
        int from, to, inext;
        Ty w,c; //w:流量, c: 单位费用
    } edge[M];
    int head[N], total;

    void adde(int x, int y, Ty w, Ty c)
    {
        edge[++total].from = x;
        edge[total].to = y;
        edge[total].w = w;
        edge[total].c=c;
        edge[total].inext = head[x];
        head[x] = total;
    }
}
using namespace Edges;

namespace MCMF_EK {
    typedef int Ty;
    const int N=5e3+10; //N：点个数
    const Ty Inf=0x3f3f3f3f;  //Inf：流量无穷大值
    // const int Inf2=0x3f3f3f3f; //inf2：层次无穷大值

    //public
    int S,T; //S,T: 源点、汇点

    //local
    bool v[N];
    int pre[N];
    Ty incf[N],d[N]; 
    //incf：增广路,
    //v:__spfa时使用的标记在队列中数组, 
    //pre：__bfs时使用的记录瓶颈路前驱转移方向的数组, 
    //d: spfa最短路

    bool __spfa(int s=S,int t=T)
    {
        queue<int> q;
        // memset(d,0x3f,sizeof(d)); //c
        //memset(v,0,sizeof(v));
        for(int i=1;i<=n;i++) //注意这个地方的n可能要改
        {
            d[i]=Inf;   //注意这个清空不能使用Inf2，因为d的值不再是层次而是费用最短路长度了
            v[i]=0;
        }

        q.push(s);
        d[s]=0;
        v[s]=1;
        incf[s]=Inf;
        while(q.size())
        {
            int x=q.front();
            q.pop();
            v[x]=0;
            for(int e=head[x];e;e=edge[e].inext)
            {
                int y=edge[e].to;
                if(edge[e].w && d[y]>d[x]+edge[e].c)
                {
                    d[y]=d[x]+edge[e].c;
                    incf[y]=min(incf[x],edge[e].w);
                    pre[y]=e;
                    if(!v[y])
                    {
                        v[y]=1;
                        q.push(y);
                    }
                }
            }
        }
        if(d[t]==Inf) return false; //no connect
        return true;
    }

    Ty __update(Ty& maxflow,int s=S,int t=T)
    {
        int x=t;
        while(x!=s)
        {
            int e=pre[x];
            edge[e].w-=incf[t];
            if(e%2) edge[e+1].w+=incf[t];
            else edge[e-1].w+=incf[t];
            x=edge[e].from;
        }
        maxflow+=incf[t];
        return incf[t]*d[t];
    }

    auto get_mcmf(int s=S,int t=T)
    {
        Ty maxflow=0, cost=0;
        while (__spfa(s,t)) 
        {
            cost+=__update(maxflow,s,t);
        }
        return make_pair(maxflow, cost);
    }
}

int main()
{
    ios::sync_with_stdio(false);
    cin>>n>>m>>MCMF_EK::S>>MCMF_EK::T;
    for(int i=1;i<=m;i++)
    {
        int x,y;
        ll w,c;
        cin>>x>>y>>w>>c;
        adde(x,y,w,c); //!
        adde(y,x,0,-c); //!
    }

    auto ans=MCMF_EK::get_mcmf();
    cout<<ans.first<<" "<<ans.second<<endl;

    return 0;
}
```



## ZKW 费用流

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

namespace Edges
{
    typedef int Ty;
    const int N=5e3+10,M=1.2e5+10;

    //public
    int n,m; //n,m: 点、边数量

    //残差网络的边
    struct E
    {
        int from, to, inext;
        Ty w , c;
    } edge[M];
    int head[N], total;

    void adde(int x, int y, Ty w, Ty c)
    {
        edge[++total].from = x;
        edge[total].to = y;
        edge[total].w = w;
        edge[total].c=c;
        edge[total].inext = head[x];
        head[x] = total;
    }
}
using namespace Edges;

namespace MCMF_ZKW {
    typedef int Ty;
    const int N=5e3+10; //N：点个数
    const Ty Inf=0x3f3f3f3f;  //Inf：流量无穷大值
    // const int Inf2=0x3f3f3f3f; //inf2：层次无穷大值

    //public
    int S,T; //S,T: 源点、汇点

    //local
    bool v[N];
    Ty d[N]; 
    //incf：增广路
    //d: spfa最短路

    //使用SLF优化的spfa寻找分层图
    bool __spfa(int s=S,int t=T)
    {
        deque<int> q;
        // memset(d,0x3f,sizeof(d)); //c
        //memset(v,0,sizeof(v));
        for(int i=1;i<=n;i++)
        {
            d[i]=Inf;   //注意这个清空不能使用Inf2，因为d的值不再是层次而是费用最短路长度了
            v[i]=0;
        }

        q.push_back(s);
        d[s]=0;
        v[s]=1;
        
        while(q.size())
        {
            int x=q.front();
            q.pop_front();
            v[x]=0;
            for(int e=head[x];e;e=edge[e].inext)
            {
                int y=edge[e].to;
                if(edge[e].w>0 && d[y]>d[x]+edge[e].c)
                {
                    d[y]=d[x]+edge[e].c;
                    
                    if(!v[y])
                    {
                        v[y]=1;
                        if(q.size() && d[y]<d[q.front()])
                        {
                            q.push_front(y);
                        }
                        else q.push_back(y);
                    }
                }
            }
        }
        //no connect
        return d[t]<Inf;
    }

    //使用dinic算法推送流。返回的是流的改变量。
    Ty __dinic(int x, int t, Ty flow, Ty &maxflow, Ty &cost)
    {
        if (x == t)
        {
            maxflow+=flow;
            return flow;
        }
        Ty rest = flow, delta;
        v[x]=true;
        for (int e = head[x]; e; e = edge[e].inext)
        {
            if (rest == 0)
                return flow; //!!!
            int y=edge[e].to;
            if(v[y]) continue;
            if (edge[e].w>0 && d[y] == d[x] + edge[e].c)
            {
                delta = __dinic(y, t, min(edge[e].w, rest), maxflow, cost);
                if (!delta)
                    d[y] = 0; //pruning
                edge[e].w -= delta;
                if (e % 2)
                    edge[e + 1].w += delta;
                else
                    edge[e - 1].w += delta; //+= ?

                cost+= delta*edge[e].c; //更新费用
                rest -= delta;
            }
        }
        return flow - rest;
    }

    //取得最大流最小费用
    auto get_mcmf(int s=S,int t=T)
    {
        Ty maxflow=0, cost=0;
        while (__spfa(s,t)) 
        {
            // cost+=__update(maxflow,s,t);
            __dinic(s,t,Inf,maxflow,cost);
        }
        return make_pair(maxflow, cost);
    }
}



int main()
{
    ios::sync_with_stdio(false);
    cin>>n>>m>>MCMF_ZKW::S>>MCMF_ZKW::T;
    for(int i=1;i<=m;i++)
    {
        int x,y;
        ll w,c;
        cin>>x>>y>>w>>c;
        adde(x,y,w,c); //!
        adde(y,x,0,-c); //!
    }

    auto ans=MCMF_ZKW::get_mcmf();
    cout<<ans.first<<" "<<ans.second<<endl;
    return 0;
}
```



### 使用vector的版本，常数是前者的一半左右

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

namespace Edges
{
    typedef ll Ty;
    const int N=5e5+10,M=1e6+10;

    //public
    int n,m; //n,m: 点、边数量

    //残差网络的边
    struct E
    {
        int from, to;
        Ty w , c;
        int dualedge_num;
    };
    // int head[N], total;
    int total;
    vector<E> head[N];

    int adde(int x, int y, Ty w, Ty c)
    {
        ++total;
        // edge[++total].from = x;
        // edge[total].to = y;
        // edge[total].w = w;
        // edge[total].c=c;
        // edge[total].inext = head[x];
        // head[x] = total;
        // head[x].emplace_back(x,y,w,c);
        head[x].push_back({x,y,w,c});
        return head[x].size()-1;
    }

    void adde2(int x, int y, Ty w, Ty c)
    {
        int dual1=adde(x,y,w,c);
        int dual2=adde(y,x,0,-c);
        head[x].back().dualedge_num=dual2;
        head[y].back().dualedge_num=dual1;
    }
}
using namespace Edges;

#include <bitset>

namespace MCMF_ZKW {
    typedef ll Ty;
    const int N=5e5+10; //N：点个数
    const Ty Inf=1e18;  //Inf：流量无穷大值
    // const int Inf2=0x3f3f3f3f; //inf2：层次无穷大值

    //public
    int S,T; //S,T: 源点、汇点

    //local
    // bool v[N];
    bitset<N> v;
    Ty d[N]; 
    //incf：增广路
    //d: spfa最短路

    //使用SLF优化的spfa寻找分层图
    bool __spfa(int s=S,int t=T)
    {
        queue<int> q;
                // memset(d,0x3f,sizeof(d)); //c
        //memset(v,0,sizeof(v));
        fill(d+1,d+1+n,Inf);
        // fill(v+1,v+1+n,0);
        for(int i=1;i<=n;i++)
        {
            // d[i]=Inf;   //注意这个清空不能使用Inf2，因为d的值不再是层次而是费用最短路长度了
            v[i]=0;
        }

        q.push(s);
        d[s]=0;
        v[s]=1;
        
        while(q.size())
        {
            int x=q.front();
            q.pop();
            v[x]=0;
            // for(int e=head[x];e;e=edge[e].inext)
            for(auto &e:head[x])
            {
                // int y=edge[e].to;
                int y=e.to;
                // if(edge[e].w>0 && d[y]>d[x]+edge[e].c)
                if(e.w>0 &&  d[y]>d[x]+e.c)
                {
                    // d[y]=d[x]+edge[e].c;
                    d[y]=d[x]+e.c;
                    
                    if(!v[y])
                    {
                        v[y]=1;
                        // if(q.size() && d[y]<d[q.front()])
                        // {
                        //     q.push_front(y);
                        // }
                        // else 
                            q.push(y);
                    }
                }
            }
        }
        //no connect
        return d[t]<Inf;
    }

    //使用dinic算法推送流。返回的是流的改变量。
    Ty __dinic(int x, int t, Ty flow, Ty &maxflow, Ty &cost)
    {
        if (x == t)
        {
            maxflow+=flow;
            return flow;
        }
        Ty rest = flow, delta;
        v[x]=true;
        // for (int e = head[x]; e; e = edge[e].inext)
        for(auto &e:head[x])
        {
            if (rest == 0)
                return flow; //!!!
            // int y=edge[e].to;
            int y=e.to;
            if(v[y]) continue;
            // if (edge[e].w>0 && d[y] == d[x] + edge[e].c)
            if (e.w>0 && d[y] == d[x] + e.c)
            {
                // delta = __dinic(y, t, min(edge[e].w, rest), maxflow, cost);
                delta = __dinic(y, t, min(e.w, rest), maxflow, cost);
                if (!delta)
                    d[y] = 0; //pruning
                // edge[e].w -= delta;
                e.w -= delta;
                // if (e % 2)
                //     edge[e + 1].w += delta;
                // else
                //     edge[e - 1].w += delta; //+= ?
                head[y][e.dualedge_num].w +=delta;
                // cost+= delta*edge[e].c; //更新费用
                cost += delta * e.c;
                rest -= delta;
            }
        }
        return flow - rest;
    }

    //取得最大流最小费用
    auto get_mcmf(int s=S,int t=T)
    {
        Ty maxflow=0, cost=0;
        while (__spfa(s,t)) 
        {
            // cost+=__update(maxflow,s,t);
            __dinic(s,t,Inf,maxflow,cost);
        }
        return make_pair(maxflow, cost);
    }
}

ll nn,a,b,c;

namespace Fast_IO {

    char __nc()
    {
        static char buf[1000000], *p = buf, *q = buf;
        return p == q && (q = (p = buf) + fread(buf, 1, 1000000, stdin), p == q)
                ? EOF
                : *p++;
    }
    
    template <typename T> inline void read(T &x) {
    char c;
    int sgn = 1;
    x = 0;
    c = __nc();
    while (c > '9' || c < '0') {
        if (c == '-')
        sgn = -1;
        c = __nc();
    }
    while (c <= '9' && c >= '0') {
        x = (x << 3) + (x << 1) + (c - '0');
        c = __nc();
    }
    x *= sgn;
    }

    template <typename T> inline void print(T x) {
    if (x < 0) {
        putchar('-');
        x = -x;
    }
    if (x > 9) {
        print(x / 10);
    }
    putchar(x % 10 + '0');
    }


    void readstr(char *a)
    {
        char c;
        c = __nc();
        while(!(c <= 'z' && c >= 'a')) c=__nc();

        while (c <= 'z' && c >= 'a') {
            *a=c;
            a++;
            c=__nc();
        }
    }
} // namespace Fast_IO
using namespace Fast_IO;

int main()
{
    // ios::sync_with_stdio(false);
    // cin.tie(0);
    // cout.tie(0);
    // cin>>nn>>a>>b>>c;
    read(nn);
    read(a);
    read(b);
    read(c);
    MCMF_ZKW::S=nn+2;
    MCMF_ZKW::T=nn+1;
    n=nn+5;
    for(int i=1;i<=nn;i++)
    {
        ll ai,bi,ci;
        // cin>>ai>>bi>>ci;
        read(ai);
        read(bi);
        read(ci);

        // adde2(nn+1,i,1,0);
        adde2(i,nn+1,1,0);

        adde2(nn+3,i,1,-ai);
        adde2(nn+4,i,1,-bi);
        adde2(nn+5,i,1,-ci);
    }

    adde2(nn+2,nn+3,a,0);
    adde2(nn+2,nn+4,b,0);
    adde2(nn+2,nn+5,c,0);

    auto ans=MCMF_ZKW::get_mcmf();
    // cout<<-ans.second<<endl;
    print(-ans.second);


    // cin>>n>>m>>MCMF_ZKW::S>>MCMF_ZKW::T;
    // for(int i=1;i<=m;i++)
    // {
    //     int x,y;
    //     ll w,c;
    //     cin>>x>>y>>w>>c;
    //     adde(x,y,w,c); //!
    //     adde(y,x,0,-c); //!
    // }

    // auto ans=MCMF_ZKW::get_mcmf();
    // cout<<ans.first<<" "<<ans.second<<endl;
    return 0;
}
```





## 带花树匹配

简要原理：从每个尚未匹配的点出发去寻找增广路，如果找到了就反转。交替地把匹配边的两端的点标记成o、i，从o标记出发的如果遇到了o，表明遇到了奇环，则执行缩花操作：lca找根，然后标记所有环上的点对应的花根。

```c++
// luogu 6113
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
#include <cassert>
#include <random>
#include <chrono>

using namespace std;
typedef long long ll;

// graph

namespace Blossom_Algorithm
{
    template <typename T>
    class graph
    {
    public:
        struct edge
        {
            int from;
            int to;
            T cost;
        };
        vector<edge> edges;
        vector<vector<int>> g;
        int n;
        graph(int _n) : n(_n) { g.resize(n); }
        virtual int add(int from, int to, T cost) = 0;
    };

    // undirectedgraph
    template <typename T>
    class undirectedgraph : public graph<T>
    {
    public:
        using graph<T>::edges; //edges：边表
        using graph<T>::g;     //g：邻接表，保存的是边的标号
        using graph<T>::n;

        undirectedgraph(int _n) : graph<T>(_n) {}
        int add(int from, int to, T cost = 1)
        {
            assert(0 <= from && from < n && 0 <= to && to < n); //标号范围：0~n-1
            int id = (int)edges.size();
            g[from].push_back(id);
            g[to].push_back(id);
            edges.push_back({from, to, cost});
            return id;
        }
    };

    // blossom / find_max_unweighted_matching
    template <typename T>
    vector<int> find_max_unweighted_matching(const undirectedgraph<T> &g)
    {
        std::mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());
        vector<int> match(g.n, -1);  // match：匹配
        vector<int> aux(g.n, -1);    // aux：时间戳记
        vector<int> label(g.n);      // label："o" or "i"，用于在找增广路时确认奇环
        vector<int> orig(g.n);       // orig：花根
        vector<int> parent(g.n, -1); // parent：父节点
        queue<int> q;
        int aux_time = -1;

        //寻找（已经有缩花）图的两个花根的LCA
        auto lca = [&](int v, int u)
        {
            aux_time++; //每次调用时都代表存在一个能缩的花，因此相当于花的编号了

            //两个点轮流交替向前跳，撞到访问过的点就表明是lca了
            while (true)
            {
                if (v != -1)
                {
                    if (aux[v] == aux_time)
                    { // 找到拜访过的点 也就是LCA
                        return v;
                    }
                    aux[v] = aux_time;
                    if (match[v] == -1)
                    {
                        v = -1;
                    }
                    else
                    {
                        v = orig[parent[match[v]]]; // 以匹配点的父节点继续寻找
                    }
                }
                swap(v, u);
            }
        }; // lca

        //以a为根的花进行缩花（注意这两个点u、v都是o型点）
        auto blossom = [&](int v, int u, int a)
        {
            while (orig[v] != a)
            {
                parent[v] = u;
                u = match[v];
                if (label[u] == 1)
                { // 初始点设为"o" 找增广路（相当于缩花后就无视了他本来的oi标记了，i型点也全部改成o型点）
                    label[u] = 0;
                    q.push(u);
                }
                orig[v] = orig[u] = a; // 缩花
                v = parent[u];
            }
        }; // blossom

        //得到了增广路并反转
        auto augment = [&](int v)
        {
            while (v != -1)
            {
                int pv = parent[v];
                int next_v = match[pv];
                match[v] = pv;
                match[pv] = v;
                v = next_v;
            }
        }; // augment

        //bfs来寻找增广路并反转之以实现匹配
        auto bfs = [&](int root)
        {
            fill(label.begin(), label.end(), -1);
            iota(orig.begin(), orig.end(), 0);
            while (!q.empty())
            {
                q.pop();
            }
            q.push(root);
            // 初始点设为 "o", 这里以"0"代替"o", "1"代替"i"
            label[root] = 0;
            while (!q.empty())
            {
                int v = q.front();
                q.pop();
                for (int id : g.g[v])
                {
                    auto &e = g.edges[id];
                    int u = e.from ^ e.to ^ v; //e.from == v，所以异或出来应该一定是e.to，不知道为什么要这么写……
                    if (label[u] == -1)
                    {                  // 找到未拜访点
                        label[u] = 1;  // 标记 "i"
                        parent[u] = v; //对标记为i的点保存parent（相当于未匹配边的反向连接）
                        if (match[u] == -1)
                        {               // 找到未匹配点
                            augment(u); // 寻找增广路径
                            return true;
                        }
                        // 找到已匹配点 将与她匹配的点丢入queue 延伸交错树
                        label[match[u]] = 0;
                        q.push(match[u]);
                        continue;
                    }
                    else if (label[u] == 0 && orig[v] != orig[u])
                    {
                        // 找到已拜访点 且标记同为"o" 代表找到"花"
                        int a = lca(orig[v], orig[u]);
                        // 找LCA 然后缩花
                        blossom(u, v, a);
                        blossom(v, u, a);
                    }
                }
            }
            return false;
        }; // bfs

        //随机打乱并匹配
        auto greedy = [&]()
        {
            vector<int> order(g.n);
            // 随机打乱 order
            iota(order.begin(), order.end(), 0);
            shuffle(order.begin(), order.end(), rng);

            // 将可以匹配的点匹配
            for (int i : order)
            {
                if (match[i] == -1)
                {
                    for (auto id : g.g[i])
                    {
                        auto &e = g.edges[id];
                        int to = e.from ^ e.to ^ i; //？？？
                        if (match[to] == -1)
                        {
                            match[i] = to;
                            match[to] = i;
                            break;
                        }
                    }
                }
            }
        }; // greedy

        // 一开始先随机匹配
        greedy();
        // 对未匹配点找增广路
        for (int i = 0; i < g.n; i++)
        {
            if (match[i] == -1)
            {
                bfs(i);
            }
        }
        return match;
    }
}
using namespace Blossom_Algorithm;

int n,m;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0), cout.tie(0);

    cin>>n>>m;

    undirectedgraph<int> G(n);

    for(int i=1;i<=m;i++)
    {
        int x,y;
        cin>>x>>y;
        G.add(x-1,y-1); //无向边，不需要加两次
    }

    auto res=find_max_unweighted_matching(G);
    int ans=0;
    for(int i=0;i<n;i++)
    {
        if(res[i]!=-1)
        {
            ans++;
        }
    }   

    cout<<ans/2<<'\n';
    for(int i=0;i<n;i++)
    {
        cout<< ((res[i]==-1) ? 0 : (res[i]+1))<<' ';
    }
    return 0;
}
```


# 杂项

## 离散化

| Funs \|\| Methods | Reliance        | Time Complexity | Inputs                      | Outputs                | Description                         |
| ----------------- | --------------- | --------------- | --------------------------- | ---------------------- | ----------------------------------- |
| copy_to_b         |                 | N               | a：数组                     | b                      | 复制a到b                            |
| sort_and_unique   | copy_to_b       | nlogn           | n：长度；b：离散化后数组    | dlen：去重后的数组长度 | 对b排序、去重，返回去重后的数组长度 |
| get_id            | sort_and_unique | logn            | x:查找值；b：离散化后数组； | 返回离散化后的值       | 返回离散化后的值（1开始计数）       |



```c++
typedef long long ll;

namespace Discretization {

    const int N=1e6+10;
    typedef ll Ty;

    //public
    Ty a[N],b[N];
    int dlen;

    // 复制a到b
    void copy_to_b()
    {
        memcpy(b, a, sizeof(a));
    }

    // 对b排序、去重，返回去重后的数组长度
    int sort_and_unique(int n)
    {
        sort(b+1,b+1+n);
        return dlen=unique(b+1,b+1+n)-b-1;
    }

    // 返回离散化后的值（1开始计数）
    inline int get_id(Ty x)
    {
        return lower_bound(b+1,b+1+dlen,x)-b;
    }
}
using namespace Discretization;

```



## 快读

注意：用快读的时候务必要把  ios::sync_with_stdio(false); 注释掉！

| Funs \|\| Methods | Reliance | Time Complexity | Inputs    | Outputs | Description         |
| ----------------- | -------- | --------------- | --------- | ------- | ------------------- |
| read              |          | 1               | 整数变量x | 读入x   | 读入x               |
| print             |          | 1               | 整数变量x |         | 输出x（不包括换行） |

```c++
#include <cstdio>
#include <iostream>

using namespace std;

namespace Fast_IO {

    char __nc()
    {
        static char buf[1000000], *p = buf, *q = buf;
        return p == q && (q = (p = buf) + fread(buf, 1, 1000000, stdin), p == q)
                ? EOF
                : *p++;
    }
    
    template <typename T> inline void read(T &x) {
    char c;
    int sgn = 1;
    x = 0;
    c = __nc();
    while (c > '9' || c < '0') {
        if (c == '-')
        sgn = -1;
        c = __nc();
    }
    while (c <= '9' && c >= '0') {
        x = (x << 3) + (x << 1) + (c - '0');
        c = __nc();
    }
    x *= sgn;
    }

    template <typename T> inline void print(T x) {
    if (x < 0) {
        putchar('-');
        x = -x;
    }
    if (x > 9) {
        print(x / 10);
    }
    putchar(x % 10 + '0');
    }


    void readstr(char *a)
    {
        char c;
        c = __nc();
        while(!(c <= 'z' && c >= 'a')) c=__nc();

        while (c <= 'z' && c >= 'a') {
            *a=c;
            a++;
            c=__nc();
        }
    }
} // namespace Fast_IO
using namespace Fast_IO;

int main() {
  ios::sync_with_stdio(false);
  cin.tie(0), cout.tie(0);
  long long x;
  read(x);
  print(x);
  putchar('\n');
  print(-x);
  putchar('\n');
  print(0);
  putchar('\n');
  return 0;
}
```



## nth_element

```c++
nth_element(a+1,a+k,a+n+1);
```

`#include <algorithm>`内置函数。第k名将在a[k]处，比它小的所有元素在左侧，比他大的所有元素在右侧。



## 二分

只是记一下，一般不会真的手写。包括一般的lowerbound和反过来版本的lowerbound。改成upperbound，则只需要改一下判断合法性的条件。请务必保证答案在l、r内存在。

口诀：以o为正方向，反向取整，“否则”反向+贡献，“贡献”正向。

| Funs \|\| Methods | Reliance | Time Complexity | Inputs                                     | Outputs | Description                                     |
| ----------------- | -------- | --------------- | ------------------------------------------ | ------- | ----------------------------------------------- |
| bs_lowerbound     |          | logn            | L、R：搜索下标范围，a：搜索序列，x：比较值 | 同描述  | 找到序列a中在范围内的>=x最小的一个，并返回其值  |
| bs_lowerbound2    |          | logn            | L、R：搜索下标范围，a：搜索序列，x：比较值 | 同描述  | 找到序列a中在范围内的<= x最大的一个，并返回其值 |

```c++
namespace BS {
    //找到>=x中最小的一个
    template <typename T>
    T bs_lowerbound(T L,T R,T a[],T x)
    {
        T l=L,r=R;
        //左x右o：向下取整，“否则”为左，+1（向右）
        while(l<r)
        {
            T mid=(l+r)>>1; //注意这样写是向下取整，用除法是向0取整，l和r为负数时可能有影响
            if(a[mid]>=x) r=mid;
            else l=mid+1;
        }
        return a[l];
    }

    //找到<=x中最大的一个
    template <typename T>
    T bs_lowerbound2(T L,T R,T a[],T x)
    {
        T l=L,r=R;
        while(l<r)
        {
            T mid=(l+r+1)>>1; //注意这样写是向下取整，用除法是向0取整，l和r为负数时可能有影响
            if(a[mid]<=x) l=mid;
            else r=mid-1;
        }
        return a[l];
    }
}
```





## 三分

这是找最小值的连续值域三分。切记不能直接用于离散值！（除非题目有任何性质保证了）

```c++
namespace TS
{
    template <typename T=double>
    T ts_findmin(T l,T r,T (*f)(T),T eps=1e-9)
    {
        //r>l
        T param=0.01;
        while (r-l>=eps) 
        {
            T mid=(l+r)/2;
            T lmid=mid-(r-l)*param, rmid=mid+(r-l)*param;
            if(f(lmid)>f(rmid))
            {
                l=lmid;
            }
            else r=rmid;
        }
        return f(r);
    }
}

double f(double x)
{
    return x*x;
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    cout<<TS::ts_findmin(-2.0, 5.0, f)<<endl;
    return 0;
}
```



## int128

```c++
#include <iostream>
#include <cstdio>
using namespace std;

void scan(__int128_t &x)//输入
{
    x = 0;
    int f = 1;
    char ch;
    if((ch = getchar()) == '-') f = -f;
    else x = x*10 + ch-'0';
    while((ch = getchar()) >= '0' && ch <= '9')
        x = x*10 + ch-'0';
    x *= f;
}

void print(__int128_t x)
{
    if(x < 0)
    {
        x = -x;
        putchar('-');
    }
     if(x > 9) print(x/10);
    putchar(x%10 + '0');
}

int main()
{
    ios::sync_with_stdio(false);
    __int128_t a,b;
    scan(a);
    scan(b);
    print(a+b);
    return 0;
}
```



## ⭐分数类

注意这个鬼东西在对0的判断上可能有点问题

```c++
ll gcd(ll a, ll b)
{
    return b ? gcd(b, a % b) : a;
}

struct Fr
{
    ll x, y;
    Fr(ll xx, ll yy)
    {
        if (yy == 0)
        {
            x = xx;
            y = yy;
            return;
        }
        ll g = gcd(xx, yy);
        x = xx / g;
        y = yy / g;
    }

    Fr() : x(0), y(1) {}
    Fr(ll x) : x(x), y(1) {}

    bool operator<(const Fr &a) const
    {
        if (a == Fr(0, 0))
        {
            if (x == 0 && y == 0)
                return false;
            return true;
        }
        if (x == 0 && y == 0)
        {
            return false;
        }

        return x * a.y < y * a.x;
    }

    bool operator>(const Fr &a) const
    {
        if (a == Fr(0, 0))
        {
            //if(x==0 && y==0) return false;
            return false;
        }
        if (x == 0 && y == 0)
        {
            return true;
        }

        return x * a.y > y * a.x;
    }

    bool operator==(const Fr &a) const
    {
        if (a.x == 0 && a.y == 0)
        {
            if (x == 0 && y == 0)
                return true;
            return false;
        }
        if (x == 0 && y == 0)
        {
            if (a.x == 0 && a.y == 0)
                return true;
            return false;
        }
        return x * a.y == y * a.x;
    }

    Fr operator+(const Fr &a) const
    {
        return Fr(x * a.y + y * a.x, y * a.y);
    }

    Fr operator-(const Fr &a) const
    {
        return Fr(x * a.y - y * a.x, y * a.y);
    }

    Fr operator*(const Fr &a) const
    {
        return Fr(x * a.x, y * a.y);
    }

    Fr operator/(const Fr &a) const
    {
        return Fr(x * a.y, y * a.x);
    }

    bool is_frac() const
    {
        if (y == 0)
            return false;
        return x % y != 0;
    }
};
```

## 最小二乘法
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

int m;
ll a1[N],a2[N],b[N];

pair<double,double> get_kb(ll a[],ll b[],ll n)
{
    ll x=0,y=0,xy=0,x2=0;
    for(int i=1;i<=n;i++)
    {
        x+=a[i];
        y+=b[i];
        xy+=a[i]*b[i];
        x2+=a[i]*a[i];
    }
    double k=(n*xy-x*y)/(n*x2-x*x);
    double bb=(y*x2-x*xy)/(n*x2-x*x);

    return {k,bb};
}


int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    cin>>T;
    for(int _=1;_<=T;_++)
    {
        scanf("%d",&m);
        for(int i=1;i<=m;i++)
        {
            scanf("%lld",&a1[i]);
        }

        for(int i=1;i<=m;i++)
        {
            scanf("%lld",&a2[i]);
        }

        for(int i=1;i<=m;i++)
        {
            scanf("%lld",&b[i]);
        }
        
        double k1,b1,k2,b2;
        tie(k1,b1)=get_kb(a1,b,m);
        tie(k2,b2)=get_kb(a2,b,m);

        double ans1=0,ans2=0;
        for(int i=1;i<=m;i++)
        {
            ans1+=fabs(b[i]-(k1*a1[i]+b1));
            ans2+=fabs(b[i]-(k2*a2[i]+b2));
        }

        if(ans1>ans2)
        {
            printf("2\n");
        }
        else printf("1\n");


    }
    return 0;
}
```


## 子集枚举与gospers_hack
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

//遍历n中选k个的所有集合
//GH(3,5): 00111,01011,...,11100
auto GospersHack(int k, int n)
{
    int cur = (1 << k) - 1;
    int limit = (1 << n);
    vector<int> res;
    while (cur < limit)
    {
        res.push_back(cur);
        // do something
        int lb = cur & -cur;
        int r = cur + lb;
        cur = ((r ^ cur) >> __builtin_ctz(lb) + 2) | r;
        // 或：cur = (((r ^ cur) >> 2) / lb) | r;
    }
    return res;
}


//枚举子集
void it_subset(int sta)
{
    for (int sub = sta; sub > 0; sub = (sub - 1) & sta)
    {
        //do something...
    }

    //bit:sta的二进制下位数-1，相当于对2取log
    int bit = 31 - __builtin_clz(sta);
    
    //取得子集和（下标从0开始计数）：例如1101表示下标为0、2、3的元素的和
    for (int sta = 1; sta < (1 << n); ++sta)
    {
        //__builtin_clz : 二进制前导零个数
        //bit：在这里是相当于对2取log的结果
        int bit = 31 - __builtin_clz(sta);
        f[sta] = add(f[sta ^ (1 << bit)], a[bit]);
    }
}

// •int __builtin_ffs (unsigned int x)
// 返回x的最后一位1的是从后向前第几位，比如7368（1110011001000）返回4。
// •int __builtin_clz (unsigned int x)
// 返回前导的0的个数。（int 1的返回值是31，2、3是30，4~7是29）
// •int __builtin_ctz (unsigned int x)
// 返回后面的0个个数，和__builtin_clz相对。
// •int __builtin_popcount (unsigned int x)
// 返回二进制表示中1的个数。
// •int __builtin_parity (unsigned int x)
// 返回x的奇偶校验位，也就是x的1的个数模2的结果。

// 此外，这些函数都有相应的usigned long和usigned long long版本，只需要在函数名后面加上l或ll就可以了，比如int __builtin_clzll。

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    auto res=GospersHack(3,5);
    for(auto v:res) cout<<v<<" ";
    return 0;
}
```

## 对拍

```c++
import os
import random as rd
import sys
import time


def generate_one_case():
    n=rd.randint(1,30)
    k=rd.randint(1,n)

    print(n,k)
    for i in range(n):
        val=rd.randint(1,50)
        print(val,end=' ')
    print()

def generate_multi_case():
    T=rd.randint(1,10)
    print(T)
    for i in range(T):
        generate_one_case()
    print(flush=True)


namae="k"

for _ in range(10000):
    sys.stdout=open(f"{namae}_in.txt","w")

    generate_multi_case()
    
    t1=time.perf_counter()
    os.system(f"{namae}.exe < {namae}_in.txt >{namae}_out.txt")
    t2=time.perf_counter()
    print((t2-t1)*1000,file=sys.stderr)
    
    t1=time.perf_counter()
    os.system(f"{namae}_std.exe < {namae}_in.txt >{namae}2_out.txt")
    t2=time.perf_counter()
    print((t2-t1)*1000,file=sys.stderr)


    if os.system(f"fc {namae}_out.txt {namae}2_out.txt"):
        print("WA",file=sys.stderr)
        exit(0)
    print("AC",file=sys.stderr)



```


## 模拟退火

```c++
//P1337 [JSOI2004]平衡点 / 吊打XXX
// 求n个点的带权重心，等价于最小化某个函数值

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
#include <ctime>
#include <random>
#include <iomanip>
using namespace std;
typedef long long ll;
const int N=2e5+10;
using ll=long long;
using ld=long double;

int n;
struct Point
{
    ld x,y,w;
}a[N];

namespace Anneal
{
    //do something...

    //public
    ld ansx,ansy,ansval;
    ld nowx,nowy,nowval;
    //ansx,ansy,ansval:当前维护的最好答案，因题而异
    //nowx,nowy,nowval：当前的答案，同上，因题而异

    //do something end.


    //local
    ld t,tt; 
    ld D;
    ld eps;
    mt19937 rnd(time(0));
    //t：温度
    //d：单次降温比例
    //eps：最大精度误差
    //rnd：随机数生成器

    //生成随机浮点数
    inline ld __Rand()
    {
        return (ld)rand()/RAND_MAX;
    }

    //生成随机整数
    inline int __randint(int r)
    {
        return rnd()%r+1;
    }

    //计算衡量用函数值
    ld calval(ld x,ld y)
    {   
        //do something...
        ld ans=0;
        for(int i=1;i<=n;i++)
        {
            ld dx=a[i].x-x, dy=a[i].y-y;
            ans+=a[i].w*sqrt(dx*dx+dy*dy);
        }
        return ans;
        //do something end.
    }

    //随机改变值
    void rand_change()
    {
        //do something...
        ld nextx=nowx+t*(__Rand()*2-1);
        ld nexty=nowy+t*(__Rand()*2-1);
        ld delta=calval(nextx,nexty)-nowval; //能量差值

        //重要公式：发生转移的几率P(delta E)=e^(-delta E/T) （新状态更劣时）
        if(delta<0 || exp(-delta/t) > __Rand())
        // if( exp(-delta/t) > __Rand())
        {
            ld tmp=nowval+delta;
            tie(nowx,nowy,nowval)=tie(nextx,nexty,tmp);
        }

        if(nowval<ansval)
        {
            tie(ansx,ansy,ansval)=tie(nowx,nowy,nowval);
        }
        //do something end.
    }

    int __cnt=0;
    auto anneal()
    {
        //init
        tt=t=100000;
        D=0.97;
        eps=0.001;
        srand(time(0));

        //do something...
        //init2
        ansx=nowx=ansy=nowy=0;
        for(int i=1;i<=n;i++)
        {
            ansx+=a[i].x;
            ansy+=a[i].y;
        }
        ansx/=n;
        ansy/=n;
        // ansx=0.577;
        // ansy=1.000;
        ansval=calval(ansx,ansy);
        nowx=ansx;
        nowy=ansy;
        nowval=ansval;

        // cerr<<"init_ansx:"<<ansx<<endl;
        // cerr<<"init_ansy:"<<ansy<<endl;
        // cerr<<"init_ansval:"<<ansval<<endl;

        while(t>=eps)
        {
            rand_change();
            t*=D;
            __cnt++;
        }
        //do something end.

        // cerr<<"__cnt:"<<__cnt<<endl;
        // cerr<<"nowval:"<<nowval<<endl;
        // cerr<<"ansval:"<<ansval<<endl;

        //细化精度，对于这题来说是必要的
        for(int i=1;i<=3000;i++)
        {
            ld nextx=ansx+t*(__Rand()*2-1);
            ld nexty=ansy+t*(__Rand()*2-1);
            ld delta=calval(nextx,nexty)-ansval; //能量差值

            if(delta<0)
            {
                ld tmp=ansval+delta;
                tie(ansx,ansy,ansval)=tie(nextx,nexty,tmp);
            }
        }

        return make_pair(ansx,ansy);
    }

}
using namespace Anneal;


int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    cin>>n;
    for(int i=1;i<=n;i++)
    {
        cin>>a[i].x>>a[i].y>>a[i].w;
    }
    anneal();
    cout<<fixed<<setprecision(3)<<ansx<<" "<<ansy<<endl;
    // cout<<ansx<<" "<<ansy<<endl;

    
    return 0;
}
```

## ⭐分隔字符串 in C++

臨時從 Algorithm_Design_and_other/pattern_recognition/classify.cpp裡面抄的

```c++
    //实用类函数
    namespace __Utility
    {
        //非数据特征名
        unordered_set<string> No_data_strings_name{
            "Id","Species"
        };

        //非数据特征位置对应的名字
        unordered_map<int,string> No_data_strings{
            {0,"Id"},
            {5,"Species"}
        };

        //分割字符串
        auto split_str(const string &s, char split_char = ',')
        {
            vector<string> res;
            //lambda
            auto string_find_first_not = [](const string &s, size_t pos = 0, char split_char = ',') {
                for (size_t i = pos; i < s.size(); i++)
                {
                    if (s[i] != split_char && s[i]!=' ' && s[i]!='\t')
                        return i;
                }
                return string::npos;
            };

            size_t begin_pos = string_find_first_not(s, 0, split_char);
            size_t end_pos = s.find(split_char, begin_pos);

            while (begin_pos != string::npos)
            {
                size_t end_pos2=end_pos-1;
                while(begin_pos<end_pos2 && (s[end_pos2]=='\t' || s[end_pos2]==' '))
                {
                    end_pos2--;
                }
                res.emplace_back(s.substr(begin_pos, end_pos2 +1 - begin_pos));
                begin_pos = string_find_first_not(s, end_pos, split_char);
                end_pos = s.find(split_char, begin_pos);
            }
            return res;
        }
    
        //字符串转double
        double str_to_double(const string& s)
        {
            return atof(s.c_str());
        }

        //字符串转int
        int str_to_int(const string& s)
        {
            return atoi(s.c_str());
        }
    } // namespace __Utility
```


## 可反悔贪心

Luogu 1484 种树

题意：有N个坑，每个坑可以种树，且获利a[i]（可以为负）。任何相邻两个坑里不能都种树，问在最多种K棵树的前提下的最大获利

做法：每个节点开个链表，种树以后合并链表两端节点，然后插入优先队列中维护。每次要种哪棵树由优先队列中的队头节点确定。

这么做可以保证每次都只多种一棵树（相当于只花费1的代价），并且得到的贡献就是中对应这么多数的最大值。

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <cmath>
#include <cstdlib>
#include <algorithm>
#include <queue>

using namespace std;

typedef long long ll;
const int N=2e6+10;
int T;

ll n,k;

struct Node
{
    ll val;
    ll lc,rc,ct; //ct: center

    bool operator<(const Node &b) const
    {   
        return val<b.val;
    }
}nd[N];
bool vis[N];


void dpdp()
{
    priority_queue<Node> q;

    for(int i=1;i<=n;i++)
    {
        q.push(nd[i]);
    }

    ll ans=0;
    bool flag=true;
    while(k--)
    {
        while(vis[q.top().ct])
        {
            q.pop();
        }
        auto u=q.top();
        auto index=u.ct;
        if(u.val<=0) {flag=false;break;} //如果是一定要种那么多数就请去掉这个

        ans+=u.val;
        q.pop();

        // merge node
        nd[index].val=nd[nd[index].lc].val + nd[nd[index].rc].val - nd[index].val;

        //list merge
        vis[nd[index].lc] = vis[nd[index].rc] =1;
        nd[index].lc = nd[nd[index].lc].lc;
        nd[nd[index].lc].rc=index;

        nd[index].rc = nd[nd[index].rc].rc;
        nd[nd[index].rc].lc=index;

        q.push(nd[index]);
    }

    if(true)
    {
        cout<<ans<<endl;
    }
    else
    {
        cout<<"Error!"<<endl;
    }

}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);

    cin>>n>>k;
    for(int i=1;i<=n;i++)
    {
        cin>>nd[i].val;
        nd[i].lc=i-1;
        nd[i].rc=i+1;
        nd[i].ct=i;
        vis[i]=0;
    }
    // nd[1].lc=n; 有环的情况（对应luogu 1792）
    // nd[n].rc=1;

    dpdp();

    return 0;
}
```

## 经典二选一问题（2021icpc上海的某题）

问题：n个有序对ai,bi，对于每个有序对，选择将ai加入set A或者将bi加入set B，最小化max(A)+max(B)，（空集时为0）

解法：

先将有序对按ai为第一关键字排序。由于ai<=aj && bi<=bj时， 可以舍弃ai,bi（这是因为较大的那个在最优解中一定会在aj,bj中选一个，而此时ai,bi的选法只要跟aj,bj的选法就一定不会使得答案增大），因此需要保证处理后的序列中，bi随i单调递减，因此倒着扫一遍删除破坏bi单调性的元素即可。具体实现时不是真的删除而是新开一个列表做这个操作。

之后，对于某个aj，只要确定选了j，那么所有比j大的i都可以在不增加答案的前提下选择ai，bj同理可以在不增加答案的前提下选择比j小的i，因此最终答案出现在 a1, a2+b1, a3+b2, ... , an+b_{n-1}, bn中。扫描一遍即可得答案。

### cf1408 D Searchlights

题意：有若干个强盗和探照灯，给出他们的坐标（0到1e6范围内），灯能看到强盗当且仅当灯不严格在强盗的右上方。现在能一次性移动所有的强盗向上或向右一步，问最少需要多少步数才能把强盗全部移出去。

解法：算出每个强盗向上和向右走所需的步数。题目就变成了最小化最大值。套用即可。

（上海的H题的做法参考oi做题记录）


``` c++

#include <iostream>
#include <cstdio>
#include <algorithm>
#include <memory.h>

using namespace std;
int T;
int n,k;
const int N=2e5+10;
int a[N],b[N];
pair<int,int> li[N],li2[N];
int li_p,li_p2;

long long upd(int n)
{
    li_p2=0;
    sort(li+1,li+1+n);
    pair<int,int> last=li[n];
    li2[++li_p2]=last;
    for(int i=n-1;i>=1;i--)
    {
        if(li[i].second>last.second)
        {
            li2[++li_p2]=li[i];
            last=li[i];
        }
    }
    long long ans=min(li2[1].first,li2[li_p2].second);
    for(int i=2;i<=li_p2;i++)
    {
        ans=min(ans,(long long)li2[i].first+li2[i-1].second);
    }
    return ans;
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    cin>>T;

    while(T--)
    {
        cin>>n>>k;
        for(int i=1;i<=k;i++)
        {
            cin>>a[i];
        }
        for(int i=1;i<=k;i++)
        {
            cin>>b[i];
        }
        sort(a+1,a+1+k);
        sort(b+1,b+1+k);
        long long ans=1e18;
        for(int i=1;i<=k;i++) //enumerate first match who;
        {
            li_p=0;
            for(int j=1;j<=k;j++)//match
            {
                int L,R;
                //L
                L=((b[(j+i-2)%k+1]-a[j])%n+n)%n;//注意下标越界

                R=((a[j]-b[(j+i-2)%k+1])%n+n)%n;
                li[++li_p]={2*L,R};
            }
            ans=min(ans,upd(li_p));

            li_p=0;
            for(int j=1;j<=k;j++)//match
            {
                int L,R;
                //L
                L=((b[(j+i-2)%k+1]-a[j])%n+n)%n;

                R=((a[j]-b[(j+i-2)%k+1])%n+n)%n;
                li[++li_p]={L,R*2};
            }
            ans=min(ans,upd(li_p));
        }
        cout<<ans<<endl;
    }
    return 0;
}

```