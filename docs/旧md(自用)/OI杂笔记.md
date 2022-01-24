> 待学习算法：
>
> **树链剖分**相关
>
> - [x] 树链剖分
>
>
> - [ ] LCT
>
> **字符串**相关
>
> - [x] 扩展kmp
>
> - [ ] 后缀数组
>
> - [ ] ac自动机
>
> - [ ] 后缀自动机
>
> **图论**相关：
>
> - [x] Tarjan
>
> - [x] 2-sat
>
> 数据结构相关
>
> - [ ] KDT（？）
> - [ ] **主席树**
> - [ ] 点分治
> - [ ] 笛卡尔树
> - [ ] **整体二分**
>
> 数学：
>
> - [ ] Polya
> - [x] **FFT**(?)
> - [ ] NTT
> - [ ] FWT
> - [x] 线性基
> - [ ] 狄里克雷卷积
> - [ ] 博弈论相关

**正难则反 正难则反 正难则反**



# 踩坑

- 使用FWT时不把数组长度开够导致wa半天（HDU 6057 Kanade’s convolution）
- 数据量过大时切记使用printf和scanf
- 用浮點數跑最短路或者最大流的時候要注意鬆弛條件要放鬆一定精度(例如dy>dx+edge+eps)

# 动态规划

## 单调队列优化

> P6563 一直在你身边
>
> 电线长度为1，2，...，n中某个数，花ai元可以得知长度是否大于（或小于等于）i，问她至少要花多少钱才能保证知道需要电线的长度
>
> 分析：
> $$
> f(L,R)=\min_{1\le k\lt r}(\max(f(L,k),f(k+1,R))+a_k)
> $$
> R扩展时，最优决策点p=k不下降（上升或不变）。
>
> 因此分类讨论f(L,k)与f(k+1,R)的大小，分别维护更新两种情况即可。
>
> 具体地：正序循环R:[1,N]，倒序循环L:[R,1]，对于每个L都后退更新最优决策点k，然后更新f(L,k)>f(k+1,R)的情况。f(L,k)<=f(k+1,R)的情况，f(L,R)=min f(k+1,R)+ak，使用单调队列维护。
>
> ---
>
> P2120 仓库建设
>
> 分析：
> $$
> dp[i]=\min_{0\le j\lt i}(dp_j+x_i(sump_i-sump_j)-\\(sumxp_i-sumxp_j))+c_i
> $$
> 直接套用斜率优化即可

## SOSdp
可以在O(m*2^m)的时间里求解下列式子：
$$
f_{st}=\sum_{i\subset st} w_i
$$

设 $dp(st,i)$ 表示二进制表示集合st的最后i位（从0计数）变化的所有子集贡献的和。

则有转移：
$$
dp(st,i) = \begin{cases}
dp(st,i-1), & \text{第i位为0} \\
dp(st,i-1) + dp(st\oplus(2^i),i-1), & \text{第i位为1} \\
\end{cases}
$$

```c++
void sos(){
	for(int i=0;i<(1<<N);i++)
		f[i]=w[i];
	for(int i=0;i<N;i++)
		for(int st=0;st<(1<<N);st++)
			if(st&(1<<i)) f[st]+=f[st^(1<<i)];
}
```



### 树上背包

P1064 [NOIP2006 提高组] 金明的预算方案

题意：给定一系列物品,每个物品有一个价格v和权值w,并且物品间存在依赖关系.求一种可行方案,使得满足依赖关系并且\sum v \leq m∑*v*≤*m*的情况下\sum w∑*w*尽量大

解法：

我们**对这颗树求一个后序遍历**,这样一个节点的儿子以及左边兄弟在序列中都在它的前部.然后呢?每一步我们都有两种决策.

- 选这个物品,那么状态i可以直接由状态i - 1转移而来
- 不选这个物品,那么是不是**这个点的儿子都不可以选**,那么状态i就只能由它的左兄弟转移而来

设pre[i]是编号为i的节点的左兄弟
$$
f[i,j]=\max\begin{cases}
f[pre[i],j],\\
f[i-1,j-v]+w, & j\ge v
\end{cases}
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
const int N=200;

int n,m;

struct E
{
    int to,inext;
}edge[N*2];
int head[N],total;

struct Node
{
    int val;
    int weight;
    int fa;
    int dfn;
}s[N];

void adde(int x,int y)
{
    edge[++total].to=y;
    edge[total].inext=head[x];
    head[x]=total;
}

int dfn;
//按照dfs序作为索引
struct Node_dfn
{
    int val;
    int weight;
    int pre;
}ss[N];
int dp[N][50000];

void dfs(int x,int fa)
{
    int p=dfn;
    for(int e=head[x];e;e=edge[e].inext)
    {
        int y=edge[e].to;
        if(y==fa) continue;
        dfs(y,x);
    }
    s[x].dfn=++dfn;
    ss[dfn].val=s[x].val;
    ss[dfn].weight=s[x].weight;
    ss[dfn].pre=p;
}

int dpdp()
{
    memset(dp,0,sizeof(dp));
    dp[0][0]=0;
    for(int i=1;i<=n+1;i++) //dfn
    {
        // dp[i][0]=0;
        for(int w=0;w<=m;w++)
        {
            /*dp[i][w]=max(
                dp[ss[i].pre][w],
                dp[i-1][w-1]+ss[i].bb
            );*/
            dp[i][w]=dp[ss[i].pre][w];
            if(w-ss[i].weight>=0) dp[i][w]=max(dp[i][w],dp[i-1][w-ss[i].weight]+ss[i].val);
        }
    }

    int ans=0;
    for(int w=1;w<=m;w++)
    {
        ans=max(ans,dp[n][w]);
    }
    return ans;
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0),cout.tie(0);
    while(cin>>m>>n)
    {
        if(n==0 && m==0) return 0;
        total=0;
        dfn=0;
        for(int i=0;i<=n+10;i++)
        {
            head[i]=0;
            ss[i]=ss[0];
            s[i]=s[0];
        }
        for(int i=1;i<=n;i++)
        {
            int a,b,c;
            cin>>a>>b>>c;

            s[i].weight=a;
            s[i].val=a*b;
            s[i].fa=c;

            s[i].dfn=0;

            adde(i,c);
            adde(c,i);
        }

        dfs(0,0);
        cout<<dpdp()<<endl;
    }
    return 0;
}
```





# 数论

## 同余性质

1. 整除性
   $$
   a\equiv b \pmod m \Rightarrow m|(a-b)
   $$

2. 传递性
   $$
   \begin{cases}
   a\equiv b \pmod m\\
   c\equiv d \pmod m
   \end{cases}
   \Rightarrow a\equiv c \pmod m
   $$

3. 保持基本运算
   $$
   \left. \begin{matrix}
   a \equiv b \pmod{m} \\
   c \equiv d\pmod{m}
   \end{matrix} \right\} \Rightarrow \left\{ \begin{matrix} a \pm c \equiv b \pm d \pmod{m} \\ ac \equiv bd \pmod{m} \end{matrix} \right.
   $$

   $$
   a \equiv b \pmod{m} \Rightarrow \begin{cases}
    an \equiv bn \pmod{m}, \forall n \in \mathbb{Z} \\
    a^n \equiv b^n \pmod{m}, \forall n \in \mathbb{N}^0
   \end{cases}
   $$

4. 底数放缩
   $$
   (km \pm a)^n \equiv (\pm a)^n \pmod{m}
   $$

5. 模数放缩
   $$
   a \equiv b \pmod{m} \Leftrightarrow ka \equiv kb \pmod{km}
   $$

6. 除法原理：当k、m互质时
   $$
   ka \equiv kb \pmod{m} \Rightarrow a \equiv b \pmod{m}
   $$

7. $$
   \begin{cases}
   a \equiv b \pmod m \\
   n|m
   \end{cases}\Rightarrow a\equiv b \pmod n
   $$

8. 对若干同余的同余方程：
   $$
   \left. \begin{matrix} a \equiv b \pmod{m_1} \\ a \equiv b \pmod{m_2} \\ \vdots \\ a \equiv b \pmod{m_n} \\ (n \ge 2) \end{matrix} \right\} \Rightarrow a \equiv b \pmod{ \text{lcm}[m_1,m_2,\cdots,m_n]}
   $$
   
9. $$
   \frac{a}{b}\mod c=\frac{a\mod (bc)}{b}
   $$



## 扩展卢卡斯定理 (exLucas)

当模数p不是质数时


$$
p=p_1^{k_1}p_2^{k_2}\cdots p_s^{k_s}
$$
当k1=k2=...=1时，求下面的同余方程的a1，然后CRT合并答案
$$
\begin{cases}
C_n^m\equiv a_1 \pmod {p_1^{k_1}} \\
C_n^m\equiv a_2 \pmod {p_2^{k_2}} \\
\cdots\\
C_n^m\equiv a_s \pmod {p_s^{k_s}}
\end{cases}
$$
当分解后还不是质数时
$$
C_n^m=\frac{n!}{m!(n-m)!}\mod p^t\\=\frac{\frac{n!}{p^{a_1}}}{\frac{m!}{p^{a_2}}*\frac{(n-m)!}{p^{a_3}}}*p^{a_1-a_2-a_3}\mod p^t\\
$$
计算n!、m!、(n-m)!在mod p^t下的值（后两者再求逆元）

对于阶乘中的每一个数，分为可被p整除的和不可被p整除的
$$
n!=\lfloor\frac{n}{p}\rfloor!~*~p^{\lfloor\frac{n}{p}\rfloor}~*~(\prod_{i=1\\p|i}^{P^t}i)^{\lfloor\frac{n}{p^t}\rfloor}~*(\prod_{i=1\\p∤i}^{N\mod {P^t}}i) \pmod {p^t}
$$
(n/p)!递归解决，后两者预处理解决

参见luogu/4720_exLucas



> [2019-2020 ICPC Asia Taipei-Hsinchu Regional Contest](https://www.cnblogs.com/st1vdy/p/12701920.html)
>
> M.DivModulo:
>
> **题意**：
>
> 求C(N,M) dmod D，其中 x dmod y 指的是去掉 x 中所有 y 因子后取余数。
>
> 分析：
>
> 我们试图将阶乘中所有含有D的项提出来，然后利用类似扩展卢卡斯计算余下的部分。
> $$
> C^M_N=\frac{N!}{M!(N-M)!}=\frac{N!~div~D}{M!~div~D*(N-M)!~div~D}*D^K
> $$
> 这个时候再取模
> $$
> C_N^M~dmod~D=(N!~div~D)*inv(M!~div~D)*inv((N-M)!~div~D) \mod D
> $$
> 分母不一定与D互质，因此考虑质因数分解D=D1...Ds，最后再用CRT合并
> $$
> C_N^M~div~D=\frac{C_N^M}{D^K}=\frac{(C_N^M~div~p_i)*p_i^k}{D^K}=\frac{(C_N^M~div~p_i)*p_i^{k_i-Ka_i}}{(D/D_i)^K}\mod D_i
> $$
> 其中K是使得D^K除C(N,M)后仍是整数的最大的K
> $$
> K=\min_{1\le i\le s}\frac{k_i}{a_i}
> $$
> 至于计算C_N^M div D mod P，则运用类似扩展卢卡斯的方法即可
> $$
> n!=\lfloor\frac{n}{D}\rfloor!~*~D^{\lfloor\frac{n}{D}\rfloor}~*~(\prod_{i=1\\D|i}^{P}i)^{\lfloor\frac{n}{D}\rfloor}~*(\prod_{i=1\\D∤i}^{N\mod {D}}i)\\
> n!~div~D=\lfloor\frac{n}{D}\rfloor!*~(\prod_{i=1\\D|i}^{P}i)^{\lfloor\frac{n}{D}\rfloor}~*(\prod_{i=1\\D∤i}^{N\mod {D}}i)
> $$

## 阶和原根

**阶**：$$(a,m)=1,a^l\equiv1\pmod{m}$$,使得其成立的最小的l称为a关于mod m的阶。记为$$\text{ord}_ma=l$$

- $$
  \text{ord}_ma=l\Rightarrow \text{ord}_ma^t=\frac{l}{(t,l)}
  $$

  证明：
  $$
  设后者原根是x,则显然l|tx\Rightarrow tx=\text{lcm}(t,l)=\frac{tl}{(t,l)}
  $$
  
- $$
  a^n\equiv1\pmod m\Leftrightarrow l|n
  $$

  由于欧拉定理$$a^{\phi(n)}\equiv 1\pmod m$$,则$$l|\phi(n)$$

- $$1,a,a^2,\cdots,a^{l-1}$$关于m两两不同余（l是最小循环周期）

- p为质数，$$l|\phi(p)$$，则存在$$\phi(l)$$个关于mod p阶为l，且两两互不同余的数

- 若$$m=p_1^{a_1}\cdots p_k^{a_k}$$,则$$\text{ord}_ma=[\text{ord}_{p_1}^{a_1},\cdots,\text{ord}_{p_k}^{a_k}]$$

**原根**：

$$(g,m)=1$$，且$$\text{ord}_mg=\phi(m)$$，则g是m的原根。

$$\{g,g^2,\cdots,g^{\phi(m)}\}$$是mod m的一个简约剩余系。

- 原根形式：$$2,4,p^\alpha,2p^\alpha$$。p为奇数质数（除了2外的质数），alpha为正整数

- 所有原根：g为m的一个原根，则下面的集合给出所有原根（原根g构成整数mod m乘法群生成元），因此若m存在原根，则原根数量为$$\phi(\phi(m))$$
  $$
  \{g^s|1\le s\le\phi(m),(s,\phi(m))=1\}
  $$

**求解一个（最小）原根**：

$$(g,m)=1$$,设$$p_1,p_2,\cdots,p_k$$是$$\phi(m)$$的所有不同质因数，则g是m的原根，当且仅当$$1\le i\le k,g^{\frac{\phi(m)}{p_i}}\not\equiv1\pmod m$$

先分解$$\phi(m)$$的质因数，然后穷举g，并依次验证每个质因数是否满足上面的条件。

[https://zh.wikipedia.org/wiki/%E5%8E%9F%E6%A0%B9](https://zh.wikipedia.org/wiki/原根)

| m    | 模m的原根(有*号的数没有原根，此时是有最大模m周期的数) | 周期 ([![OEIS](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d8/OEISicon_light.svg/14px-OEISicon_light.svg.png)](https://zh.wikipedia.org/wiki/OEIS) [A002322](https://oeis.org/A002322)) |
| ---- | ----------------------------------------------------- | ------------------------------------------------------------ |
| 1    | 0                                                     | 1                                                            |
| 2    | 1                                                     | 1                                                            |
| 3    | 2                                                     | 2                                                            |
| 4    | 3                                                     | 2                                                            |
| 5    | 2, 3                                                  | 4                                                            |
| 6    | 5                                                     | 2                                                            |
| 7    | 3, 5                                                  | 6                                                            |
| 8*   | 3, 5, 7                                               | 2                                                            |
| 9    | 2, 5                                                  | 6                                                            |
| 10   | 3, 7                                                  | 4                                                            |
| 11   | 2, 6, 7, 8                                            | 10                                                           |
| 12*  | 5, 7, 11                                              | 2                                                            |
| 13   | 2, 6, 7, 11                                           | 12                                                           |
| 14   | 3, 5                                                  | 6                                                            |
| 15*  | 2, 7, 8, 13                                           | 4                                                            |
| 16*  | 3, 5, 11, 13                                          | 4                                                            |
| 17   | 3, 5, 6, 7, 10, 11, 12, 14                            | 16                                                           |
| 18   | 5, 11                                                 | 6                                                            |
| 19   | 2, 3, 10, 13, 14, 15                                  | 18                                                           |
| 20*  | 3, 7, 13, 17                                          | 4                                                            |
| 21*  | 2, 5, 10, 11, 17, 19                                  | 6                                                            |
| 22   | 7, 13, 17, 19                                         | 10                                                           |
| 23   | 5, 7, 10, 11, 14, 15, 17, 19, 20, 21                  | 22                                                           |
| 24*  | 5, 7, 11, 13, 17, 19, 23                              | 2                                                            |
| 25   | 2, 3, 8, 12, 13, 17, 22, 23                           | 20                                                           |
| 26   | 7, 11, 15, 19                                         | 12                                                           |
| 27   | 2, 5, 11, 14, 20, 23                                  | 18                                                           |
| 28*  | 3, 5, 11, 17, 19, 23                                  | 6                                                            |
| 29   | 2, 3, 8, 10, 11, 14, 15, 18, 19, 21, 26, 27           | 28                                                           |
| 30*  | 7, 13, 17, 23                                         | 4                                                            |
| 31   | 3, 11, 12, 13, 17, 21, 22, 24                         | 30                                                           |
| 32*  | 3, 5, 11, 13, 19, 21, 27, 29                          | 8                                                            |
| 33*  | 2, 5, 7, 8, 13, 14, 17, 19, 20, 26, 28, 29            | 10                                                           |
| 34   | 3, 5, 7, 11, 23, 27, 29, 31                           | 16                                                           |
| 35*  | 2, 3, 12, 17, 18, 23, 32, 33                          | 12                                                           |
| 36*  | 5, 7, 11, 23, 29, 31                                  | 6                                                            |

## 费马小定理

p是质数，则
$$
a^p\equiv a \pmod p
$$
特别的,当a不是p的倍数时(这时a,p互质)有
$$
a^{p-1}\equiv 1 \pmod p
$$


## Miller Rabin 质数判别法

### 费马素性测试

在\[2到n-1\]里面选择一些基a,检查是否满足费马小定理,不满足就说明不是质数

- 当然满足了也不一定是质数

### 二次探测定理

p是奇质数，则方程$x^2\equiv1\pmod p$的解为$x=1$ 或者 $x=p-1$ （也即$x=-1$ (同余意义下的)）

> 证明：
> $$
> x^2=1\pmod1 \Rightarrow (x+1)(x-1)=0\pmod1\Rightarrow\\
> p|(x+1)(x-1) \Rightarrow x=p-1或1
> $$
> 

### 卡迈克尔数

对这类数,取任何基都能通过素性测试,但其本身却不是质数. 最小的三个数是561,1105,1729([OEIS:A002997](https://oeis.org/A002997))

**等价定义**:一个正合成数n是卡迈克尔数，当且仅当 n无平方数约数且对于所有 n的素因数 p，p-1|n-1

### 综合做法

如果n是大于2的质数,则由于n-1是偶数,对于任意的基$a~(a\in[2,n-1])$,有
$$
a^{n-1}=a^{2^sd}
$$
由费马素性测试,我们接下来要做$a^{n-1}\equiv 1 \pmod n$,结合上式可以得到
$$
a^{2^sd}=(a^{2^{(r-1)}d})^2\equiv 1\pmod n
$$
由二次探测定理可知，$a^{2^{r-1}}\equiv 1或-1$. 而如果等于1那么可以继续开根号。

现在考虑反过来用这个定理：如果存在这么一个a使得下面两个式子都满足：
$$
a^d\not\equiv1 \pmod n\\
a^{{2^r}d}\not\equiv1 \pmod n (0\le r\le s-1)\\
$$
那么n就一定是合数,a便是n是合数的一个凭据.

> 注：这里要么$a^d,a^{2^1d},\cdots,a^{2^rd}$全是1，要么会存在一个数是-1，然后就都是1
>
> 这是因为如果其中某个数是1那么可以开根号，得到前一个数是1或-1。如果是1那么可以继续开根号直到所有数都是1为止；如果是-1那么就此终止，因此这个-1之后的数都是1



## Pollard's Rho 质因数分解(待补充)





## 狄利克雷卷积，莫比乌斯反演

**引理1：**地板除法嵌套可下放
$$
\forall a,b,c\in \mathbb{Z},\lfloor\frac{a}{bc}\rfloor=\lfloor\frac{\lfloor\frac ab\rfloor}{c}\rfloor
$$
**引理2**：不同的n/d个数不会超过$\lfloor2\sqrt n\rfloor$

### 数论分块

（待补充）

### 积性函数

$f(n)$满足$f(1)=1 \&\& \forall x,y\in\mathbb{N_+},\gcd(x,y)=1$有$f(xy)=f(x)f(y)$,则f为积性函数。

如果没有gcd条件的限制，也即任意两个数都满足积性条件，则f为完全积性函数

**性质**：（待补充）



**常见积性函数**：

下文中$[condition]$表示当条件成立时为1，否则为0

- 单位函数：（完全积性函数）
  $$
  \epsilon(n)=[n=1]
  $$
  
- 幂函数和恒等函数：(完全积性函数)
  $$
  id_k(n)=n^k,id(n)=n
  $$
  
- 常数函数：（完全积性函数）
  $$
  1(n)=1
  $$

- 除数函数：
  $$
  \sigma_k(n)=\sum_{d|n}d^k\sigma_0(n),\sigma_1(n)=\sigma(n)
  $$

- 欧拉函数：

- 莫比乌斯函数：
  $$
  \mu(n)=\begin{cases}
  1 & n=1\\
  0 & \exist d>1:d^2|n\\
  (-1)^{\omega(n)} & else\\
  \end{cases}
  $$

- 本质不同质因子个数函数（也是积性函数）：
  $$
  \omega({n})=|\{p: (p|n \&\& \text{p is a prime})\}|
  $$
  

### 狄利克雷卷积（Dirichlet ）

两个数论函数fg的卷积，是所有整除n的因子d的f(d)，和该因子对应的商数n\d的g(n\d) 之积的求和 （结果是一个新的数论函数）
$$
(f*g)(n)=\sum_{d|n}f(d)g(\frac nd)
$$
**性质**（群论）：

- 交换律
- 结合律
- 分配率：$f*(g+h)=f*g+f*h$
- 单位元：$\epsilon$,也即单位函数



## 莫比乌斯容斥

如果要求某个值，这个值要求$\gcd(a_1,\cdots,c_n)=1$，那么
$$
ans=\sum_{d=1}^{n}\mu(d)\cdot D(a_1,a_2,\cdots,a_n,d)
$$
其中$D(a_1,a_2,\cdots,a_n,d)$表示满足$d|\gcd(a_1,a_2,\cdots,a_n)$约束时的函数值

> 例题：
>
> 2019 银川D
>
> 蓝书上的求sum of [gcd=1]题

## \*RSA

### 产生公钥和私钥的过程

1. 选择两个质数p、q，令N=pq
2. 求N的欧拉函数值r：$r=\phi(N)=\phi(p)\phi(q)=(p-1)(q-1)$
3. 选择**小于r**的正数e，使得$\gcd(e,r)=1$
4. 使用exgcd求得e关于r的逆元（由于其与r互质，因此e的逆元存在），记为d

至此，$(N,d)$为私钥、$(N,e)$为公钥。

### 加密

假设要加密的内容为n（n<N），则加密后的明文c为：
$$
c\equiv n^e \pmod N
$$

### 解密

拥有私钥d和明文c，就可以用下面的式子解密：
$$
n\equiv c^{d} \pmod N
$$
原理：
$$
c^d\equiv n^{ed} \pmod N
$$
而$ed\equiv 1 \pmod r$，则$ed=1+hr=1+h\phi(N)$，从而
$$
n^{ed}\equiv n\cdot (n^{\phi(N)})^h \pmod N
$$
由欧拉定理可知$n^{\phi(N)}\equiv 1 \pmod N$（如果n和N互质），那么可得


$$
n^{ed}\equiv n \pmod N
$$

如果不互质，设$n=kp<N,\gcd(n,q)=1$，则
$$
n^{ed}\equiv (kp)^{ed}\equiv 0\equiv n \pmod p\\
n^{ed}\equiv n^{ed-1}\cdot n=({n^{\phi(N)}})^hn\equiv1^hn=n\pmod q
$$
根据同余性质9，得到
$$
n^{ed}=n \pmod {pq}
$$

## Bertrand's postulate 伯特兰切比雪夫（假设）定理

对于任意整数$n>3$，存在质数p：
$$
n<p<2n-2
$$
弱化版本：
$$
n<p<2n
$$
强化版本：对于$n>25$，存在质数p：
$$
n<p<(1+\frac 15)n
$$



# 博弈论

## Nim、SG、Multi-SG

Nim**先手必胜**：异或和不为0

SG：$sg(x)=\text{mex}(sg(y_1),\cdots)$

Multi-SG: 若干个ICG的和等于每个ICG的sg值异或和

## Anti-SG、Anti-Nim

取得最后一个石头的输。

**先手必胜**：

1. sg≠0，且有单一游戏sg>1;
2. sg=0，且无单一游戏sg>1; （也即所有游戏sg=1）

## Bash、Anti-Bash

一堆（n个）石头，能拿1到m个，不能拿的输。

Bash先手必胜：$n\%(m+1)\neq0$

反Bash先手必胜：$(n-1)\%(m+1)\neq0$

## Lasker's Nim

n堆石头，对一堆石头可以用nim的取法或者分成两堆。

单一游戏的SG函数：
$$
sg(x)=\begin{cases}
x-1, & x\%4==0\\
x, & x\%4==1or2\\
x+1, & x\%4==3
\end{cases}
$$

## Every-SG

未结束的单一游戏必须动一步。

某个单一游戏的step：
$$
step(x)=\begin{cases}
0, & \text{end}\\
\max(step(y))+1, & sg(x)\neq0~and~sg(y)=0\\
\min(step(y))+1, & sg(x)=0\\
\end{cases}
$$
**先手必胜**：$\max(step(x))\%2==1$

## 阶梯博弈

阶梯上有若干石头，每次选一个阶梯上的至少一个石头移动到下一层，不能移动的输。

**方法**：**奇数台阶看做单个Nim游戏**

## 硬币反转

- N枚硬币排成一排，有的正面朝上，有的反面朝上。我们从左开
  始对硬币按1到N编号。
- Alice和Bob轮流根据某些约束翻硬币（如：每次只能翻一或两枚，或者每
  次只能翻连续的几枚），但他所翻动的硬币中，最右边的必须是
  从正面翻到反面。
- 谁不能翻谁输。

**方法**：**局面的SG值为局面中每个正面朝上的棋子单一存在时的SG值的异或和**

## 树上删边

叶子节点的sg值为0，非叶子节点的sg：
$$
sg(x)=\sum_{\oplus}(sg(y)+1)
$$
其他的待补充……

## 威佐夫博弈

两人轮流取两堆筹码，其中取法有两种：取走一堆中任意个筹码，或从两堆中取走相同数目的筹码。取完所有筹码的一方获胜。

前若干个奇异局势：（0，0）、（1，2）、（3，5）、（4，7）、（6，10）

- 任何一个自然数都包含在唯一一个奇异局势（先败）中
- x为前k个奇异局势中未出现的最小正整数，则（x，y）中的y=x+k

局面（x，y）（x<y）**先手必败**：
$$
(y-x)\frac{\sqrt5+1}{2}=x
$$

## 斐波那契博弈

一堆石子有n个，两人轮流取，先取者第1次可以取任意多个，但不能全部取完，以后每次取的石子数不能超过上次取子数的2倍。取完者胜。

**先手必败**：石头数量是斐波那契数

- 任何数可以分解成若干不连续斐波那契数的和

# 组合数学

## 组合数

**恒等式：**

- 斜线加和等于斜线末端正下方的数

$$
\sum_{r=0}^k \binom {n+r-1}r = \binom {n+k}k
$$

- 同行加和

$$
\sum_{r=0}^n \binom nr = 2^{n}
$$

- 同列相加
  $$
  \sum_{r=m}^{n} \binom{r}{m}=\binom{n+1}{m+1}
  $$
  

## 隔板法

n个球放入k个盒子中。（注意k是盒子数不是板子数）

https://zh.wikipedia.org/wiki/%E9%9A%94%E6%9D%BF%E6%B3%95

**不允许空盒：**这种放法不允许放将板子在一列球的最左端和最右端
$$
\binom{n-1}{k-1}
$$
**允许空盒**：也即允许多个板子插在同一个空隙之间（包括最左、右端）。等价于向n+k个球中插k-1个板子（k个盒子）（不允许空盒）。

可以这么考虑：向n+k个球中插k-1个板子（不允许空盒）后再从所有盒子中全部拿掉一个球，就等价于现在这个问题了。
$$
\binom{n+k-1}{k-1}
$$




## 不相邻组合数

从n个球中选r个，使得它们互不相邻，问取法总数。
$$
C_{n-r+1}^r~(n\ge2r-1)
$$
证明方法：{b1,...,br}是一个不相邻集合，构造c1=b1,c2=b2-2,...,cr=br-r+1，得到新的集合{c1,c2,...,cr}，由于b互不相邻，这种构造方法可以保证c集合没有重复元素，且对于不同的b集合，c集合也不重复。这就是说c和b是一一对应关系。而c集合的意义是从n-r+1个球中选r个。



## 斐波那契数列的组合数表示

$$
F_n=C_{n-1}^{0}+C_{n-2}^{1}+\dots+C_{n-i-1}^{i}\\
=\sum_{i=0}^{(n+1)/2}C_{n-i-1}^{i}
$$

证明方法：考虑上楼梯时+2的次数。每有1个+2，可选择的余地就减少1。
$$
1+1+1+1\\
1+1+2\\
1+2+1\\
2+1+1\\
2+2
$$

## 第一类（无符号）斯特林数

n个（不同）元素分成k个环的分法数目
$$
s(n,k)=\begin{bmatrix}
n\\k
\end{bmatrix}\\
s(0,0)=s(n,0)=s(0,n)=0\\
s(n+1,k)=ns(n,k)+s(n,k-1)
$$
**性质**：
$$
s(n,1)=(n-1)!\\
s(n,2)=(n-1)!\sum_{i=1}^{n-1}\frac 1i
$$

## 第二类斯特林数

n个（不同）元素分成k个非空子集的方法数目
$$
S(n,k)=\begin{Bmatrix}
n\\k
\end{Bmatrix}\\
\begin{Bmatrix}
0\\0
\end{Bmatrix}=1,
\begin{Bmatrix}
0\\n
\end{Bmatrix}=
\begin{Bmatrix}
n\\0
\end{Bmatrix}=0
\\
\begin{Bmatrix}
n\\k
\end{Bmatrix}=\begin{Bmatrix}
n-1\\k-1
\end{Bmatrix}+k\begin{Bmatrix}
n-1\\k
\end{Bmatrix}
$$
**性质**：（没什么卵用= =）
$$
\begin{Bmatrix}
n\\k
\end{Bmatrix}=0~(k>n)\\
\begin{Bmatrix}
n\\n
\end{Bmatrix}=1\\
\begin{Bmatrix}
n\\2
\end{Bmatrix}=2^{n-1}-1\\
\begin{Bmatrix}
n\\3
\end{Bmatrix}=\frac12(3^{n-1}+1)-2^{n-1}
$$
**通项公式**：
$$
\begin{Bmatrix}
n\\k
\end{Bmatrix}=\frac1{k!}\sum_{i=0}^k(-1)^i{k\choose i}(k-i)^n\\
=\begin{align*}\left\{\begin{matrix}n\\m\end{matrix}\right\}=\sum\limits_{k=0}^m\dfrac{(-1)^k}{k!}\dfrac{(m-k)^n}{(m-k)!}\end{align*}
$$
和递减阶乘的关系：
$$
\begin{align*}x^n=\sum\limits_{k=1}^n\left\{\begin{matrix}n\\k\end{matrix}\right\}x^\underline k\end{align*}
$$

> $$
> x^{\overline n}=x(x+1)\cdots(x+n-1)\\
> x^{\underline n}=x(x-1)\cdots(x-n+1)\\
> $$
>
> 

## 错排

**简化公式**：
$$
D_n=\lfloor\frac{n!}{e}+0.5\rfloor
$$
**递推公式**：
$$
D_n=nD_{n-1}+(-1)^n\\
D_n=(n-1)(D_{n-1}-D_{n-2})\\
(D_1=0,D_2=1)
$$


## Dilworth定理

**定理**：对于一个偏序集，其最少链划分数等于其最长反链的长度。

**对偶定理**：对于一个偏序集，其最少反链划分数等于其最长链的长度。

- 最少上升子序列划分数=最长不上升子序列长度



## ⭐放球问题


1、球同，盒同，盒不可以为空Pm（N）--这符号表示部分数为m的N-分拆的个数，m是P的下标，为了好看我将大写的M弄成小写

2、球同，盒同，盒可以为空 Pm（N+M）--为什么要加M，与4为什么要在3的基础上加M是一样的，就是为了保证不为空

3、球同，盒不同，盒不可以为空C(N-1, M-1)

4、球同，盒不同，盒可以为空   C(N+M-1, M-1)

5、球不同，盒同，盒不可以为空S(N, M) --第二类斯特林数

6、球不同，盒同，盒可以为空   S (N, 1) + S(N, 2) + S(N, 3) + ... + S(N, M)

7、球不同，盒不同，盒不可以为空M! * S(N, M)

8、球不同，盒不同，盒可以为空 M^N--表示M的N次方


1、8个相同的球放进4个相同的盒子里，每盒至少一个，有几种方法？
公式：球相同，盒相同，拆分公式。
P4(8)=P1(4)+P2(4)+P3(4)+P4(4)
=1+2+1+1
=5

2、8个相同的球放进4个不同的盒子里，每盒至少一个，有几种方法？
公式：球相同，盒不同，插板法。
C(8-1,4-1)
=C(7,3)
=7*6*5/6
=35

3、8个不同的球放进4个不同的盒子里，每盒至少一个，有几种方法？
公式：球不同，盒不同，不为空，阶乘和二类斯特林数，球是行号，盒子是列号。
M!*S(N,M)
=4! * S(8,4)
=24*1701
=40824


4、8个不同的球放进4个相同的盒子里，每盒至少一个，有几种方法 ？
公式：球不同，盒同，二类斯特林数，为空是累加，不为空是直接取数，球是行号，盒子是列号。
S(N,M)
=S(8,4)
=1701

5、8个相同的球放进4个相同的盒子里，有几种方法 ？
公式：球同，盒同，为空，拆分公式。
P4(8+4)=P4(12)
=P1(8)+P2(8)+P3(8)+P4(8)
=1+4+(P1(5)+P2(5)+P3(5))+(P1(4)+P2(4)+P3(4)+P4(4))
=1+4+(1+2+(P1(2)+P2(2))+(1+2+1+1)
=1+4+5+5
=15


6、8个相同的球放进4个不同的盒子里，有几种方法？
公式：球同，盒不同，插板法。
C(11,3)
=11*10*9/6=15*11=165

7、8个不同的球放进4个不同的盒子里，有几种方法 ？
公式：球不同，盒不同，为空，直接是M^N
4^8=4^4*4^4=2^8*2^8=256*256=65536

8、8个不同的球放进4个相同的盒子里，有几种方法？
公式：球不同，盒同，二类斯特林数，为空，是累加
S (N, 1) + S(N, 2) + S(N, 3) + ... + S(N, M)
=S（8,1)+S（8,2)+S（8,3)+S（8,4)
=1+127+966+1701
=2795


9、8个不同的球平均分给4个小朋友，有几种分法？
从8个球中取2个分给第1个小朋友，从剩下6个中取2个来分给第二个小朋友。。。
C(8,2)*C(6,2)*C(4,2)*C(2,2) = 2520


10、8个不同的球平均分成4堆，有几种分法？
C(8,2)*C(6,2)*C(4,2)*C(2,2) / 4!= 2520/24 =105



# 线性代数



## 高斯消元

> P2962 [USACO09NOV]Lights G
>
> 题意：给n个点m条边，n个点一开始全是0，按下一个点使得该点和其他与该点相连的点的状态改变（异或），求使得所有点全为1的最小按下次数。
>
> 解：根据点的关系列方程（只是加法改为乘法），然后做高斯消元，dfs求出各个x为1还是为0，更新答案

## 矩阵优化

> P2044 [NOI2012]随机数生成器
>
> 题意：求
> $$
> X_n\mod g
> $$
> 其中
> $$
> X_{n+1}=(aX_n+c)\mod m
> $$
> 解：设
> $$
> A=\begin{bmatrix}
> X_n & a_{11}\\
> a_{21} & a_{22}
> \end{bmatrix}\\
> B=\begin{bmatrix}
> b_{11} & b_{11}\\
> b_{21} & b_{22}
> \end{bmatrix}\\
> $$
> AB乘积应该仍是A的形式，只不过X_n变为X_{n+1}=aX_n+c，根据关系可以求出矩阵中的待定系数为
> $$
> A=\begin{bmatrix}
> X_0 & 1\\
> 0 & 0
> \end{bmatrix}\\
> B=\begin{bmatrix}
> a & 0\\
> c & 1
> \end{bmatrix}\\
> $$
> 套用矩阵快速幂（其中乘法用快速乘法以防止爆longlong）即可

## 异或

单位元：0

逆元：$x^{-1}=x$

零元：不存在

运算律：交换律、结合律

有关公式：

- $a\and b=a|b-a\oplus b$
- $a+b=((a\&b)<<1 )|(a\oplus b)$

## 线性基

可在对数时间内完成插入、查询集合最大值和最小异或和的操作。

完成重构操作后（花费对数平方的时间）可转换为形似于最简行阶梯型矩阵的形式，之后可在对数时间内查询集合内第k大异或和。

**最大值**

- （restructure前）从大到小枚举每个数异或进来会不会使得答案更大，如果会就异或进来。
- 如果需要在异或初值不为零的情况下取得最大值，只需要更改ans的初始值即可。

**最小值**：

- 直接贪心拿线性基中最小的数（当然，需要特判线性基是否可获得0）
- -

> **HDOJ 3949 第k大异或和**
>
> 模板题
>
> 
>
> **P4151 最大xor路径**
>
> 给定无向图（可能有重边和自环），求1到n号点的最大xor路径和。
>
> 解法：由于异或的性质，每条边最多走一次，且环上转一圈能回到原点，从原点再继续走就相当于白嫖了环上的权值和。因此随便找一条从1到n的路径，这时题目等价于选几个简单环和现有答案异或使得答案最大，再用dfs找到图中所有简单环并把简单环的异或和添加到线性基中即可。
>
> 
>
> **P4301 新nim游戏**
>
> 在第一个回合中，第一个游戏者可以直接拿走若干个整堆的火柴。可以一堆都不拿，但不可以全部拿走。第二回合也一样，第二个游戏者也有这样一次机会。从第三个回合（又轮到第一个游戏者）开始，规则和 Nim 游戏一样。
>
> 如果你先拿，怎样才能保证获胜？如果可以获胜的话，还要让第一回合拿的火柴总数尽量小。
>
> 解法：排序然后倒着（从大到小）插入线性基，如果插入失败就累加到答案中。（这题不可能输出-1）
>
> **P4869 albus就是要第一个出场**
>
> 给n个数ai和一个数q，n个数的（多重集的）子集异或值从小到大排序得序列b，求q在b中首次出现的下标。
>
> 解法：将a中的数全部插入线性基并restructure，利用二分在线性基中求得q的排名，显然q就是a去重后得到的b'的下标。
>
> 根据“不去重异或集合”的推论，不去重异或集合得到的序列b'，应该是去重异或集合b中的每个数重复$2^{n-lb.cnt}$次得到的。因此将刚刚得到的下标减去1，乘上这个数，再加1，即可得到答案。

# [生成函数](https://oi-wiki.org/math/gen-func/ogf/)

## 普通生成函数

**幂级数**：
$$
F(x)=\sum_{n=0}^\infty a_nx^n
$$

- 如果序列a有通项公式，那么它的普通生成函数的系数就是通项公式。例如$$a=<1,2,4,8,16,...>$$的生成函数是$$\sum_{n\ge 0} 2^nx^n$$

**加法**：
$$
F(x)\pm G(x)=\sum_n (a_n\pm b_n)x^n
$$

因此 $$F(x)\pm G(x)$$是序列$$<a_n\pm b_n>$$ 的普通生成函数。

**乘法（卷积）**：
$$
F(x)G(x)=\sum_n x^n \sum_{i=0}^na_ib_{n-i}
$$
因此 $$F(x)G(x)$$是序列$$<\sum_{i=0}^na_ib_{n-i}>$$ 的普通生成函数。

**常用展开**：

等比级数
$$
\frac1{1-x}=\sum_{n=0}^\infty x^n\\
\frac1{1-kx}=\sum_{n=0}^\infty k^nx^n
$$
<0,1,1,1,....>
$$
F(x)=\frac x{1-x}
$$
偶数<1,0,1,0,1,..>
$$
F(x)=\frac 1{1-x^2}
$$
等差<1,2,3,4,...>
$$
F(x)=\sum_{n\ge1}nx^{n-1}=(\sum_{n\ge1}x^n)'=(\frac{1}{1-x})'=\frac1{(1-x)^2}
$$
二项式$$a_n=\binom{m}{n}$$
$$
F(x)=\sum_{n\ge0}\binom{m}{n}x^n=(1+x)^m
$$
$$a_n=\binom{m+n}{n}$$
$$
F(x)=\frac{1}{(1-x)^{m+1}}
$$


## 斐波那契数列

两种封闭形式（系数其实就对应通项公式）：

当$$a_0=0,a_1=1$$时（使用$$\frac{1}{1-kx}$$的级数展开）
$$
F(x)=xF(x)+x^2F(x)-a_0x+a_1x+a_0\\
\Rightarrow F(x)=\frac{x}{1-x-x^2}=\frac{\frac1{\sqrt5}}{1-\frac{1+\sqrt5}{2}x}-\frac{\frac1{\sqrt5}}{1-\frac{1-\sqrt5}{2}x}\\
=\frac1{\sqrt5}\sum_{n=0}^\infty x^n((\frac{1+\sqrt5}{2})^n-(\frac{1-\sqrt5}{2})^n)
$$
当$$a_0=1,a_1=1$$时
$$
F(x)=\frac{1}{1-x-x^2}=\sum_{n\ge0}x^n\sum_{i=0}^n\binom{n-i}{i}
$$

## 卡特兰数

$$
H_n=\sum_{i=0}^{n-1}H_iH_{n-i-1}~(n\ge1)\\
H_0=H_1=1
$$

生成函数
$$
H(x)=\sum_{n\ge0}H_nx^n=1+x\sum_{n\ge1}\sum_{i=0}^{n-1}H_ix^iH_{n-i-1}x^{n-1-i}\\
=1+x\sum_{i\ge0}H_ix^i\sum_{n\ge0}H_nx^n=1+xH(x)^2
$$
得
$$
H(x)=\frac{1\pm\sqrt{1-4x}}{2x}=\frac2{1\mp\sqrt{1-4x}}
$$
又$$H(0)=1$$，因此取正根，则利用牛顿二项式定理展开$$\sqrt{1-4x}$$，然后再代回去经过一番运算可得
$$
H(x)=\frac1{2x}\sum_{n\ge1}\binom{2n-1}{n}\frac1{(2n-1)}2x^n=\cdots=\sum_{n\ge0}\binom{2n}{n}\frac1{n+1}x^n
$$

> BZOJ 
>
> 有 n 堆糖果。不同的堆里糖果的种类不同（即同一个堆里的糖果种类是相同的，不同的堆里的糖果的种类是不同的）。第i个堆里有 个糖果。现在要吃掉至少a个糖果，但不超过b个。求有多少种方案。
>
> $$n\le 10,0\le a\le b\le 10^7,m_i\le 10^6$$
>
> i堆吃j个的生成函数
> $$
> F_i(x)=\sum_{j=0}^{m_i}x^j=\frac{1-x^{m_i+1}}{1-x}
> $$
> 考虑所有堆就是乘起来，对应生成函数
> $$
>  G(x)=\prod_{i=1}^n F_i(x)=(1-x)^{-n}\prod_{i=1}^n(1-x^{m_i+1}) 
> $$
> 其中
> $$
> \begin{aligned} (1-x)^{-n} &=\sum_{i\ge 0}\binom{-n}{i}(-x)^i\\ &=\sum_{i\ge 0}\binom{n-1+i}{i}x^i \end{aligned}
> $$
> 右侧式子在n较小时能够暴力展开，设其展开后$$x^k$$对应系数为$$c_k$$，则1~b的答案为
> $$
> \sum_{k=0}^nc_k\sum_{i=0}^{b-k}\binom{n-1+i}{i}=\sum_{k=0}^nc_k\binom{n+b-k}{n}
> $$
> 



## 指数生成函数

$$
\hat F(x)=\sum_{n}a_n\frac{x^n}{n!}
$$

**加法**：对应项系数相加

**乘法（卷积）**：
$$
\hat F(x)\hat G(x)=\sum_{i\ge0}a_i\frac{x^i}{i!}\sum_{j\ge0}b_j\frac{x^j}{j!}\\
=\sum_{n\ge0}x^n\sum_{i=0}^na_ib_{n-i}\frac1{i!(n-i)!}\\
=\sum_{n\ge0}\frac{x^n}{n!}\sum_{i=0}^na_ib_{n-i}\binom{n}{i}
$$
因此其是序列$$<\sum_{i=0}^n\binom{n}{i}a_ib_{n-i}>$$的指数生成函数

**常见展开**：

$$<1,1,\cdots>$$
$$
\hat F(x)=\sum_{n\ge0}\frac{x^n}{n!}=e^x
$$
等比数列$$<1,p,p^2,\cdots>$$
$$
\hat F(x)=\sum_{n\ge0}p^n\frac{x^n}{n!}=e^{xp}
$$

## 圆排列

n个选r个的圆排列：（先用普通的排列，然后去除转1、2、...、r次的情况共r种，因此除以r）
$$
Q(n,r)=\frac{P(n,r)}{r}=\frac{n!}{r(n-r)!}
$$
特别地，n个数的全圆排列是(n-1)!种。

生成函数（其关于e的指数，就是一般排列的指数生成函数）
$$
\hat Q(x)=\sum_{n\ge1}\frac{(n-1)!x^n}{n!}=\sum_{n\ge1}\frac{x^n}{n}=-\ln(1-x)\\
\Rightarrow e^{\hat Q(x)}=\hat P(x)=\frac{1}{1-x}
$$
事实上，设排列数为p(n)，则其可以看成枚举其由k个换排列构成并求和得到，设$$q_k(n)$$表示排列由k个置换组成的，c(n)表示圆排列个数
$$
p(n)=\sum_{k=1}^nq_k(n)\\
\hat P(x)=\sum_{n\ge1}\sum_{k=1}^nq_k(n)\frac{x^n}{n!}=\sum_{k=1}^n\hat q_k(x)
$$
$$q_k(n)$$的生成函数和$$c(n)$$的生成函数有如下关系：[参考请见此](https://www.cnblogs.com/XiaoVsun/p/13054084.html)
$$
\hat q_k(x)=\frac1{k!}\hat c(x)^k
$$
则
$$
\hat P(x)=\sum_{k=1}^n \frac1{k!}\hat c(x)^k=e^{\hat c(x)}
$$

## 错排数

可以看成没有长度为1的环的排列，因此其生成函数
$$
\hat R(x)=\sum_{n\ge2}\frac{x^n}{n}=-\ln(1-x)-x
$$

## 五边形数、整数分拆

参考：https://www.zhihu.com/question/364818496

**递推公式**：
$$
f_1=1,f_n=f_{n-1}+3n-2
$$
**通项公式**：
$$
f_n=\frac{n(3n-1)}{2}
$$
特别地、对于广义五边形数，上式的n：n=0,1,-1,2,-2,...
$$
F_n=\frac{n(3n\pm 1)}{2}
$$


**欧拉函数（复变函数)的展开式**：
$$
\phi(q)=\prod_{k=1}^\infty(1-q^k)
$$


**整数分拆的生成函数**：
$$
G(x)=\sum_{n=0}^\infty p(n)x^n=\prod_{i=1}^\infty(\frac{1}{1-x^i})
$$
也即欧拉函数的倒数。

# [群论](https://oi-wiki.org/math/permutation-group/#_2)

## 群、子群

若集合S（非空） 和S 上的运算*构成的代数结构(S,\*)满足以下性质：

- 封闭性： 
  $$
  \forall a,b \in S, a\cdot b\in S
  $$
  
- 结合律： 
  $$
  \forall a,b,c \in S,(a\cdot b)\cdot c=a\cdot(b\cdot c)
  $$
  
- 单位元： 
  $$
  \exist e\in S,\forall a\in S,e\cdot a=a\cdot e=a
  $$
  
- 逆元：
  $$
  \forall a\in S,\exist b\in S,a\cdot b=b\cdot a=e
  $$
  称b为a的逆元，记为(a)^(-1)

则称(S,\*) 为一个 **群** 。例如，整数集和整数间的加法 构成一个群，单位元是 0，一个整数的逆元是它的相反数。

- 子群：(T,\*)，T是S的非空子集
- 由于有结合律，因此对于该运算可以使用快速幂

## 置换群

把集合中的元素打乱位置
$$
f=\begin{pmatrix}
a_1,\cdots,a_n\\
a_{p_1},\cdots,a_{p_n}\\
\end{pmatrix}
$$
置换运算也满足群的运算的性质：封闭性（置换后元素仍在集合内）、结合律（置换乘法）、单位元（恒等置换，置换后位置不变）、逆元（置换的反向操作）

- 置换的乘法：
  $$
  f=\begin{pmatrix}
  a_1,\cdots,a_n\\
  a_{p_1},\cdots,a_{p_n}\\
  \end{pmatrix}\\
  g=\begin{pmatrix}
  a_{p_1},\cdots,a_{p_n}\\
  a_{q_1},\cdots,a_{q_n}\\
  \end{pmatrix}\\
  f\circ g=\begin{pmatrix}
  a_1,\cdots,a_n\\
  a_{q_1},\cdots,a_{q_n}\\
  \end{pmatrix}
  $$

## 循环置换

$$
(a_1,\cdots,a_m)=\begin{pmatrix}
a_1,a_2,\cdots,a_n\\
a_n,a_1,\cdots,a_{n-1}
\end{pmatrix}
$$

- 两个循环置换不含有相同的元素，则称它们是 **不相交** 的。
- 任意一个置换都可以分解为若干不相交的循环置换的乘积。
  （如果把元素视为图的节点，映射关系视为有向边，则每个节点的入度和出度都为 1，因此形成的图形必定是若干个环的集合，而一个环即可用一个循环置换表示。）

## Burnside引理 和 Polya定理

**Burside引理：**
$$
|X/G|=\frac{1}{|G|}\sum_{g\in G}|X^g|\\
X^g=\{x|x\in X,g(x)=x\}
$$
**Polya定理：**
$$
|X/G|=\frac{1}{|G|}\sum_{g\in G}|B|^{c(g)}
$$
其中$c(g)$是置换g能拆分成的不相交循环置换数量。

- 对于旋转：假设旋转了k下，则循环置换数量是
  $$
  c(g)=\gcd(n,k)
  $$

- 对于翻转：
  $$
  c(g)=\frac{n}{2}+[n\%2==1]
  $$

> [P4980 Polya定理模版](https://www.luogu.com.cn/problem/P4980)
> $$
> ans=\frac{\sum_{k=1}^n\gcd(n,k)}{n}=\sum_{d|n}\phi(d)\cdot n^{n/d-1}
> $$
> （然而这题的欧拉函数居然能暴力求……真是见鬼）

# 多项式

**欧拉公式**：
$$
e^{\theta i}=\cos\theta+i\sin\theta
$$

**系数表示法、点值表示法**：

使用n+1个点表示多项式
$$
f(x)=\{(x_0,f(x_0)),\cdots,(x_n,f(x_n))\}
$$


## 快速傅立叶变换（FFT）

**离散傅里叶变换**(Discrete Fourier Transform)：系数表示法转为点值表示法。

**……逆变换**：点值->系数

### 卷积

$$
\vec c=\vec a \otimes \vec b
$$

其中向量表示多项式，且
$$
c_i=\sum_{j=0}^ia_jb_{i-j}
$$
利用点值表示法将点值相乘再转换为系数表示法也可以计算卷积（On的时间复杂度）。因此瓶颈在于点值表示法与系数表示法的转换。

将xi取特定的值，$x_i=\omega_i^{[n]}=\omega_n^i$，则可根据某些特殊性质在nlogn时间内进行快速傅立叶变换，对多项式进行求值和对点集进行插值。

### 单位复根

复数意义下，下面方程的解是n次复根
$$
x^n=1 \Rightarrow (\omega_n)^n=1
$$
（n次）单位复根：把复平面单位元分成n份，第一个角对应的复数向量
$$
\omega_n=e^{\frac{2\pi i}{n}}\\
\{\omega_n^k|k=0,1,\cdots,n-1\}
$$

- $w_n^0=1$

**性质**：
$$
w_n^n=1\\
w_n^k=w_{2n}^{2k}\\
w_{2n}^{k+n}=-w_{2n}^k
$$

**定理**1：

如果$a=b\pmod n$，则
$$
w_n^a=w_n^b
$$
证明：在复平面上看单位复根容易看出定理的叙述是正确的

**定理2**：（相消定理）
$$
\omega_{dn}^{dk}=\omega_{n}^k~(\text{for }d>0)
$$
在复平面上也容易看出其正确性。

**定理2的推论**：
$$
\omega_n^{n/2}=\omega_{2}^1=-1~(n>0,n\text{ is even})
$$
**定理3**：（折半定理）
$$
\omega_{n}^{n/2+k}=-\omega_n^k
$$

### 离散傅立叶变换

当$x_k=\omega_n^k$时（n是多项式A的次数，k=0~n-1）带入多项式A得到各项$y_k=A(\omega_n^k)=\sum_{i=0}^{n-1}a_i(\omega_n^k)^i$，多项式值向量（点值表示法）$\vec y=(y_0,\cdots,y_{n-1})$就被称为系数向量a（系数表示法）的离散傅立叶变换，记为
$$
\vec y=\text{DFT}_n(\vec a)
$$

### 离散傅立叶逆变换

根据值向量y求出系数向量：
$$
\vec a=\text{DFT}^{-1}_n(\vec y)
$$

### 卷积定理

a、b是系数向量（长度为n，最高次为n），则卷积c（也是系数向量）
$$
\vec c=DFT_{2n}^{-1}(DFT_{2n}(\vec a)\cdot DFT_{2n}(\vec b))
$$
其中𝑛为2的幂次，不足的用0补。中间的乘号表示两个向量的点乘。

### 快速傅立叶变换

快速将系数表示法转换为点值表示法。

一个n阶多项式可以被拆成两部分（偶数项系数拿出来，搭配新的$x^0\text~x^{n-1}$，成为一个新多项式；奇数项系数拿出来成为另一个新多项式）：
$$
A(x)=A^{[0]}(x^2)+xA^{[1]}(x^2)\\
A^{[0]}(x)=a_0+a_2x+\cdots+a_{n-2}x^{n/2-1}\\
A^{[1]}(x)=a_1+a_3x+\cdots+a_{n-1}x^{n/2-1}\\
$$
（注意这里得带入x^2，这样才齐次）

由这两个多项式得到的离散傅立叶变换的结果（点值表示法相量）是

$$
\vec y^{[0]}=DFT_{n/2}(a_0,\cdots,a_{n-2})\\
\vec y^{[1]}=DFT_{n/2}(a_1,\cdots,a_{n-1})\\
\vec y=DFT_{n}(a_0,a_1,\cdots,a_{n-1})
$$

则这两个相量的各项系数是

$$
y_k^{[0]}=A^{[0]}(\omega^k_{n/2})=A^{[0]}((\omega^{k}_{n})^2)\\
y_k^{[1]}=A^{[1]}(\omega^k_{n/2})=A^{[1]}((\omega^{k}_{n})^2)
$$

那么最终结果（之所以分成两部分是因为y相量的下标大小是$y^{[0]},y^{[1]}$的两倍，因此在分治完成后转移时只需要枚举k：0~n/2-1即可）：

$$
y_k=A(\omega_n^k)=A^{[0]}((\omega_n^k)^2)+A^{[1]}((\omega_n^k)^2)\omega^k_n=y^{[0]}_k+y^{[1]}_k\omega_n^k\\
y_{k+n/2}=A(\omega^{k+n/2}_n)=A^{[0]}((\omega_n^{k+n/2})^2)+A^{[1]}((\omega_n^{k+n/2})^2)\omega^{k+n/2}_n\\
=A^{[0]}((\omega_n^{k+n/2})^2)-A^{[1]}((\omega_n^{k+n/2})^2)\omega^{k}_n=y^{[0]}_k-y^{[1]}_k\omega_n^k
$$

实际实现时，需要先将多项式长度补全到2的幂次。

### 快速傅立叶逆变换（IFFT）

可以证明：
$$
a_i=\frac1n\sum_{i=0}^{n-1}y_i(\omega_n^{-k})^i
$$
其中ai是系数表示法的系数，yi是点值表示法的系数。这和处理FFT是一样的形式。（也即，矩阵$Y=\Omega A$中，Omega（系数矩阵）的逆矩阵是每一项取倒数，然后再除以n（也即多项式长度））

又因为
$$
\frac{1}{\omega_n}=\omega_n^{-1}
$$
将FFT过程中的pi改为-pi就是在做IFFT了。最后求得的系数全部除以n（多项式长度）。

### 蝴蝶变换

分治过程中系数的下标变换有如下规律：
$$
1:01234567\\
2:02461357\\
3:04261357\\
4:04261537
$$
也即下一步的下标在二进制下的反转值会成为这一步的下标。

递推实现：(https://oi-wiki.org/math/poly/fft/#_8)
$$
R(0)=0\\
R(x)=\lfloor\frac{R(\lfloor\frac{x}{2}\rfloor)}{2}\rfloor+(x\mod 2)\frac{len}{2}
$$

```c++
void pre_rev(int len)
{
    rev[0]=0;
    for(int i=1;i<=len;i++)
    {
        rev[i]=rev[i>>1]>>1;
        if(i&1)
        {
            rev[i]|=len>>1;
        }
    }
}
```

> [SP8372 TSUM - Triple Sums](https://www.luogu.com.cn/problem/SP8372)
>
> 题意：n个数，任取三个加起来（要求下标i<j<k），问每个可能的结果的方案数（值域绝对值20000，N在40000内）。
>
> https://www.cnblogs.com/SuuT/p/9584925.html
>
> 解法：设A(x)是不考虑顺序的选法的选一个的生成函数，则A(x^2)就是选两个一样的生成函数，A(x^3)就是选三个一样的生成函数（均是不考虑顺序的）
>
> 考虑容斥原理把选到相同的下标的情况给排除掉，则下面的式子就是筛掉之后的生成函数：减去有两种相同的选法的生成函数，再加回多减去的三种相同的选法的生成函数
> $$
> A^3(x)-3A(x^2)A(x)+2A(x^3)
> $$
> 由于i<j<k，因此还要除以6来筛掉其他重复的情况（显然有3\*2\*1种可能）
>
> 实现时由于有负数存在，需要整体将读入的序列移动2e4，最后要反向移动6e4（因为最终式子是3次式？）
>
> [UVA12633 Super Rooks on Chessboard](https://www.luogu.com.cn/problem/UVA12633)
>
> 题目大意：给你一张网格，上面有很多骑士，每个骑士能横着竖着斜着攻击一条直线上的格子，求没被攻击的格子的数量总和（R C D小于50000）
>
> https://blog.csdn.net/u013368721/article/details/45367249
>
> 解法：先计算出不考虑斜方向的情况，这时候答案就是没车的行乘以没车的列的数量。
>
> 考虑斜方向，我们就要剔除掉会被斜方向占据的情况。设多项式R(x)表示没被占据的行号的生成函数，C(x)表示没被占据的列号的生成函数。（行号从上到下增大，列号从左到右增大，从0开始计数）
> $$
> R(x)=\sum_{i=1}^{cnt}x^{r_i}\\
> C(x)=\sum_{i=1}^{cnt2}x^{-c_i}\\
> $$
> 这时就是要数$r_i-c_j=d_k$的i、j、k数量，也就是$(R\cdot C)(x)$中$x^{d_k}$的系数。（其中ri、cj、dk是没有车的行号与列号的集合以及有车的对角线的集合）
>
> 具体实现时，由于-ci是负数不好处理，于是整体偏移C（题目给出的列数），d这个集合的下标也整体偏移C那么多，最后计数的时候直接减去系数乘以[那一对角线是否有车]即可。
>
> [HDOJ4609 3-idiots](http://acm.hdu.edu.cn/showproblem.php?pid=4609)
>
> 题意：给出n个数（可能相同，值域1~1e5），任选3个，问能够构成三角形的概率。
>
> https://blog.csdn.net/Maxwei_wzj/article/details/79329154
>
> 解法：设A(x)为任意选1个得到的数的和的生成函数，则
> $$
> G(x)=\frac{A(x)^2-A(x^2)}{2}
> $$
> 这个是在排序后选两个有序数对构成的和的选法数量的生成函数。
>
> 直接去考虑能有多少个有序数对i、j、k满足$A_i+A_j>A_k$且$A_i\le A_j\le A_k$ 会存在困难（因为直接套用上面的生成函数的结果的后缀和是不能保证后面的条件满足的），于是我们反过来考虑抠掉$A_i+A_j\le A_k$，这样在排序后，后者的条件自然满足。
>
> 枚举Ak的值，然后分三类情况统计答案（tempsum是小于Ak的值的数量，tempsum2是小于等于Ak的生成函数的值的系数和，cnt_i是等于i的数的数量）：
>
> - $A_i\le A_j \lt A_k$ ：答案累加上`ans+=(tempsum*(tempsum-1)/2-tempsum2)*cnt[i];`
> - $A_i\lt A_j = A_k$：`ans+=tempsum*cnt[i]*(cnt[i]-1)/2;`
> - $A_i\lt A_j = A_k$：`ans+=cnt[i]*(cnt[i]-1)*(cnt[i]-2)/6;`



## 快速数论变换（Number theoretic transform）

FFT之所以要引入复数，是因为单位元根满足循环群的性质：
$$
\{1,\omega,\omega^2,\cdots,\omega^{2^K-1}\}
$$
这个群是$2^K$阶循环群。而在实数范围内找不到这样的循环群。

参考：https://zhuanlan.zhihu.com/p/166197713

在mod p意义下，p的原根就恰好和刚刚的单位元根一样具有相同的性质。

令g是p（质数）的原根，令$g_N=g^{\frac{p-1}{N}}~(N|p-1)$，则：
$$
g_N^N=g^{p-1}=1 \pmod p\\
g_{N}^{N/2}=g^\frac{p-1}{2}=-1 \pmod p
$$

> $g^{\frac{p-1}{2}}$是-1可由原根的性质+二次探测定理证明充分必要条件。

从这里可以知道，下面的群的阶数就是N-1：
$$
\{g,g^2,\cdots,g^{N-1}=1\}
$$


关键问题就在于$N|p-1$，而p=998244353时
$$
p-1=998244353-1=2^{23}\times17\times7
$$
由于在FFT中N（也就是扩展后的多项式长度）是2的幂，因此大多数时候（只要N小于等于2的23次幂）能够满足$N|p-1$。同样类似的数还有p=1004535809=479*2^{21}+1。**这两个数的原根都是3。**

## 快速沃尔什变换（Fast Walsh Transform）

https://oi-wiki.org/math/poly/fwt/#_3

用于解决与位运算有关的卷积问题：$\vec c=\vec a \oplus \vec b$
$$
C_i=\sum_{i=j|k}A_j B_k
$$
对或运算来说，构造：
$$
A'=FWT[A]:A'_i=\sum_{i=i|j}A_j
$$
也即A'_i表示二进制下的1是i的子集的Aj的总和，如此构造就有
$$
FWT[C]=FWT[A]\cdot FWT[B]\\
\sum_{i=i|j}C_i=\sum_{i=i|j}A_i\cdot \sum_{i=i|j}B_i
$$
特别地，多项式长度为1时，显然有
$$
A=FWT[A]=A'
$$
因为在或的情况下i自己就是i的子集。对于与来说也同理。

考虑递推公式的时候，主要考虑多出来的那一位的01和运算子集或超集的关系

### 或（OR）

merge表示串接，A0表示左半部分，A1表示右半部分。
$$
FWT[A]=merge(FWT[A_0],FWT[A_0]+FWT[A_1])\\
UFWT[A']=merge(UFWT[A'_0],UFWT[A'_1]-UFWT[A'_0])
$$

### 与（AND）

上文中子集变成超集
$$
FWT[A]=merge(FWT[A_0]+FWT[A_1],FWT[A_1])\\
UFWT[A']=merge(UFWT[A'_0]-UFWT[A'_1],UFWT[A'_1])
$$

### 异或（XOR）

设
$$
A'_k=FWT[A][k]=\sum_{i=0}^{2^n-1}A_i(-1)^{|i\and k|}
$$
其中$|i\and k|$ 表示i and k中1的数目的奇偶性。

则（因为指数那个地方只是表示奇偶性，所以可以直接从加号替换成异或符号）
$$
FWT[A][k]\cdot FWT[B][k]\\=
\sum_{i=0}^{2^n-1} A_i(-1)^{|i\and k|}\sum_{i=0}^{2^n-1} B_i(-1)^{|i\and k|}\\
=\sum_{i=0}^{2^n-1}\sum_{j=0}^{2^n-1}A_iB_j(-1)^{|i\and k|\oplus|j\and k|}\\
=\sum_{i=0}^{2^n-1}\sum_{j=0}^{2^n-1}A_iB_j(-1)^{|(i\oplus j)\and k|}\\
=\sum_{val=0}^{2^n-1}(\sum_{j=0}^{2^n-1}A_{val}B_{val\oplus j})(-1)^{|val\and k|}
$$
注意：最后一个等式后求和顺序已经变换。括号内就是$C_i$
$$
FWT[A]=merge(FWT[A_0]+FWT[A_1],FWT[A_0]-FWT[A_1])\\
UFWT[A']=merge(\frac{UFWT[A'_0]+UFWT[A'_1]}2,\frac{UFWT[A'_0]-UFWT[A'_1]}2)\\
$$
如果是同或就交换+-符号。





# 网络流相关

## Hall's marriage theorem

设二分图中G=<V1,V2,E>中，|V1|=m<=|V2|=n，G中存在V1到V2的完全匹配，**当且仅当**V1中任意k个(k=1,2,..,m)个顶点至少与V2中k个顶点相邻

（随便挑出任意个男生，如果他们心仪的女生的数量（重复不算）比这些挑出的男生数还少，那么必然不能实现完美配对）

## 二分图最大匹配数量

N(S)表示与S相邻（连接）的顶点数量
$$
|M|=|V_1|-max_{S\subset V_1}(|S|-|N(S)|)
$$

> ###### 有n个班级，每个班级有ai个人，一个班级可以做出bi杯奶茶，一个班级的人不能喝本班做的奶茶，可以喝别的班做的奶茶，问最多有多少人喝到奶茶
>
> 我们思考三种情况：
> 1.当这个S子集是个空子集的时候，那么|M|=|U|
> 2.当这个S子集就是U的本身的时候，那么N(S)就等于V（奶茶的数量），即|M|=|V|
> 3.当S是|U|的一个子集，我们一个班一个班的考虑，当S是一个班的学生的时候，那么：
> |M|=Sa-（ai-（Sb-bi））其中Sa，Sb分别代表学生总数量和奶茶总数量，ai，bi代表本班学生数量和本班做的奶茶数
> （为什么第三个情况不一个个学生考虑火或者两个班三个班的考虑呢，因为我们求的是max，也就是最大值，那么这些考虑都是无效的，大家画一下就知道了，很容易理解）
> 三个情况取最小值记为结果
>
> ---
>
> 为何不一个个考虑：若如此做，|S|小而|N(S)|大，将S扩大到同班级，结果会更好
>
> 为何不数个班级一起考虑：若如此做，|N(S)|=|V2|（也就是奶茶数量）为定值，则将S扩大到所有人，结果会更好，但这和情况2是重合的
>
> 三个情况取最小值：-max >>>> min
>
> 原文链接：https://blog.csdn.net/qq_41925919/java/article/details/99689223

## 最大权闭合子图

带权闭合子图：权在点上，子图中的点的出边均在子图中

最大权闭合子图：值等于 **“所有正点权值的和”减去“s-t最小割”**

https://www.cnblogs.com/dilthey/p/7565206.html

> 小M的作物
>
> （此题的另一个做法：A、B作为源点和汇点，作物作为中间节点，再构造连结源点（汇点）和作物节点的奖励节点，然后求最小割）
>
> https://blog.csdn.net/clover_hxy/article/details/55043578



## 最小割的一个问题模型[¶](https://oi-wiki.org/graph/flow/min-cut/#_11)

有 个物品和两个集合 ，如果将一个物品放入 集合会花费 ，放入 集合会花费 ；还有若干个形如 限制条件，表示如果 和 同时不在一个集合会花费 。每个物品必须且只能属于一个集合，求最小的代价。

这是一个经典的 **二者选其一** 的最小割题目。我们对于每个集合设置源点 和汇点 ，第 个点由 连一条容量为 的边、向 连一条容量为 的边。对于限制条件 ，我们在 之间连容量为 的双向边。

注意到当源点和汇点不相连时，代表这些点都选择了其中一个集合。如果将连向 或 的边割开，表示不放在 或 集合，如果把物品之间的边割开，表示这两个物品不放在同一个集合。

最小割就是最小花费。



# 计算几何(Computational Geometry)

计算几何全家桶：https://www.cnblogs.com/Xing-Ling/p/12102489.html

kuangbin的板子：https://kuangbin.github.io/2019/04/28/20190428/#more

Pecco的板子：https://zhuanlan.zhihu.com/p/338057154

这里主要用kuangbin的板子。

## 常识

- 向量a的左手边是正区域（包括180度线），右手边是负区域。因此两个相量a、b的夹角是正时，b在a左侧，否则在右侧。（这个约定在叉乘、半平面交中都有用）

- $$
  |\vec a\times\vec b|=|\vec a||\vec b|\sin<\vec a,\vec b>\\
  |\vec a\cdot \vec b|=|\vec a||\vec b|\cos<\vec a,\vec b>
  $$

  

## 原则

- 点和向量使用同一套结构Point，但是允许向量拥有别名Vec。点和向量特有的方法在定义时加以区分

## 点在任意多边形内

> HDU1756 cupids arrow
>
> ```c++
> #include <iostream>
> #include <cmath>
> #include <algorithm>
> using namespace std;
> const double eps=1e-8;
> inline int dcmp(double a){return a<-eps?-1:(a>eps?1:0);}//处理精度
> inline double Abs(double a){return a*dcmp(a);}//取绝对值
> struct Point
> {
>     double x,y;
>     Point(double X=0,double Y=0)
>     {
>         x=X,y=Y;
>     }
> };
> typedef Point Vector;
> 
> double Dot(Vector a,Vector b) {return a.x*b.x+a.y*b.y;}
> double Cro(Vector a,Vector b) {return a.x*b.y-a.y*b.x;}
> double Len(Vector a) {return sqrt(Dot(a,a));}
> Vector operator+(Vector a,Vector b){return Vector(a.x+b.x,a.y+b.y);}
> Vector operator-(Vector a,Vector b){return Vector(a.x-b.x,a.y-b.y);}
> Vector operator*(Vector a,double b){return Vector(a.x*b,a.y*b);}
> bool operator==(Point a,Point b){return !dcmp(a.x-b.x)&&!dcmp(a.y-b.y);}//两点坐标重合则相等
> 
> int n;
> Point a[110];
> 
> bool check_PL(Point p,Point a,Point b)
> {
>     return !dcmp(Cro(p-a,b-a)) && dcmp(Dot(p-a,p-b))<=0; //叉乘为0表三点共线，点积小于0表示PA、PB反向
> }
> 
> int PIP(Point *P,int n,Point a)
> {
>     int cnt=0;
>     double temp=0;
>     for(int i=1;i<=n;i++)
>     {
>         int j=i==n?1:i+1;
>         if(check_PL(a,P[i],P[j])) return 2;//点在边上
>         if(a.y>=min(P[i].y,P[j].y) && a.y<max(P[i].y,P[j].y))//纵坐标在该线段两端点之间
>         {//temp:相似三角形确定交点的x坐标
>             temp=P[i].x+(a.y-P[i].y)/(P[j].y-P[i].y)*(P[j].x-P[i].x);
>             cnt+=dcmp(temp-a.x)>0;//交点在A右方
>         }
>     }
>     return cnt&1;
> }
> 
> int main()
> {
>     ios::sync_with_stdio(false);
>     while (cin>>n)
>     {
>         for(int i=1;i<=n;i++)
>         {
>             int x,y;
>             cin>>x>>y;
>             a[i].x=x,a[i].y=y;
>         }
>         int m;
>         cin>>m;
>         for(int i=1;i<=m;i++)
>         {
>             int x,y;
>             cin>>x>>y;
>             Point b(x,y);
>             if(PIP(a,n,b))
>                 cout<<"Yes"<<endl;
>             else cout<<"No"<<endl;
>         }
>     }
>     return 0;
> }
> ```
>
> 





## 点在凸多边形内

可以使用二分法判断

> HRBUST 1429 凸多边形
>
> ```c++
> #include <iostream>
> #include <cmath>
> #include <algorithm>
> using namespace std;
> const double eps=1e-8;
> inline int dcmp(double a){return a<-eps?-1:(a>eps?1:0);}//处理精度
> inline double Abs(double a){return a*dcmp(a);}//取绝对值
> struct Point
> {
>     double x,y;
>     Point(double X=0,double Y=0)
>     {
>         x=X,y=Y;
>     }
> };
> typedef Point Vector;
> 
> double Dot(Vector a,Vector b) {return a.x*b.x+a.y*b.y;}
> double Cro(Vector a,Vector b) {return a.x*b.y-a.y*b.x;}
> double Len(Vector a) {return sqrt(Dot(a,a));}
> Vector operator+(Vector a,Vector b){return Vector(a.x+b.x,a.y+b.y);}
> Vector operator-(Vector a,Vector b){return Vector(a.x-b.x,a.y-b.y);}
> Vector operator*(Vector a,double b){return Vector(a.x*b,a.y*b);}
> bool operator==(Point a,Point b){return !dcmp(a.x-b.x)&&!dcmp(a.y-b.y);}//两点坐标重合则相等
> 
> const int N=1e5+10;
> int n;
> Point a[N],b[N];
> 
> bool check_PL(Point p,Point a,Point b)
> {
>     return !dcmp(Cro(p-a,b-a)) && dcmp(Dot(p-a,p-b))<=0; //叉乘为0表三点共线，点积小于0表示PA、PB反向
> }
> 
> bool judge(Point a,Point L,Point R)//判断AL是否在AR右边
> {
>     return dcmp(Cro(L-a,R-a))>0;
> }
> 
> int PIP_of_convex(Point *P,int n,Point a)
> {
>     if(judge(P[1],a,P[2]) || judge(P[1],P[n],a)) return 0;
>     if(check_PL(a,P[1],P[2]) || check_PL(a,P[1],P[n])) return 2;
>     int L=2,R=n-1;
>     while (L<R)
>     {
>         int mid=(L+R+1)/2; //OOO)XX
>         if(judge(P[1],P[mid],a)) L=mid;
>         else R=mid-1;
>     }
>     if(judge(P[L],a,P[L+1])) return 0;//严格在外侧
>     if(check_PL(a,P[L],P[L+1])) return 2;
>     return 1;
> }
> 
> int main()
> {
>     ios::sync_with_stdio(false);
>     while (cin>>n)
>     {
>         for(int i=n;i>=1;i--)
>         {
>             int x,y;
>             cin>>x>>y;
>             a[i].x=x,a[i].y=y;
>         }
>         int m;
>         cin>>m;
>         bool flag=true;
>         for(int i=1;i<=m;i++)
>         {
>             int x,y;
>             cin>>x>>y;
>             b[i].x=x,b[i].y=y;
>         }
>         for(int i=1;i<=m;i++)
>         {
>             if(PIP_of_convex(a,n,b[i])!=1)
>                 {flag=false;break;}
>         }
>         if(flag)
>             cout<<"YES"<<endl;
>         else cout<<"NO"<<endl;
>     }
>     
>     return 0;
> }
> 
> //http://acm.hrbust.edu.cn/vj/index.php?c=problem-problem&id=55043
> ```
>
> 



## 凸包(Convex Hull)

### Graham扫描法

需要极角排序

https://blog.csdn.net/qq_39826163/article/details/83861353

### Andrew 算法

需要按照坐标x、y依次排序并正反跑共两次

https://oi-wiki.org/geometry/convex-hull/

https://www.cnblogs.com/yjbjingcha/p/7255457.html

> P1452 旋转卡壳模版
>
> ```c++
> #include <cstdio>
> #include <cmath>
> #include <algorithm>
> using namespace std;
> const double eps=1e-8;
> inline int dcmp(double a){return a<-eps?-1:(a>eps?1:0);}//处理精度
> inline double Abs(double a){return a*dcmp(a);}//取绝对值
> struct Point
> {
>     double x,y;
>     Point(double X=0,double Y=0)
>     {
>         x=X,y=Y;
>     }
> };
> typedef Point Vector;
> 
> double Dot(Vector a,Vector b) {return a.x*b.x+a.y*b.y;}
> double Cro(Vector a,Vector b) {return a.x*b.y-a.y*b.x;}
> double Len(Vector a) {return sqrt(Dot(a,a));}
> Vector operator+(Vector a,Vector b){return Vector(a.x+b.x,a.y+b.y);}
> Vector operator-(Vector a,Vector b){return Vector(a.x-b.x,a.y-b.y);}
> Vector operator*(Vector a,double b){return Vector(a.x*b,a.y*b);}
> bool operator==(Point a,Point b){return !dcmp(a.x-b.x)&&!dcmp(a.y-b.y);}//两点坐标重合则相等
> 
> bool cmp1(Vector a,Vector b)
> {
>     if(a.x==b.x) return a.y<b.y;
>     return a.x<b.x;
> }
> 
> int n,conv_p;
> Point a[50010],conv[50010];
> 
> int Convex_Hull(Point *P,int n, Point *st)
> {
>     int t=0;
>     if(n==1)
>     {
>         st[1]=st[2]=P[1];
>         return t=1;
>     }
>     if(n==2)
>     {
>         st[1]=P[1];
>         st[2]=st[3]=P[2];
>         return t=2;
>     }
>     sort(P+1,P+1+n,cmp1);
>     for(int i=1;i<=n;i++)
>     {
>         while (t>1 && dcmp( Cro(st[t]-st[t-1],P[i]-st[t-1])<=0 ))
>         {
>             t--;
>         }
>         st[++t]=P[i];
>     }
>     int temp=t;
>     for(int i=n-1;i>=1;i--)
>     {
>         while (t>temp && dcmp( Cro(st[t]-st[t-1],P[i]-st[t-1])<=0 ))
>         {
>             t--;
>         }
>         st[++t]=P[i];
>     }
>     return --t;
> }
> 
> int dis2(Vector a,Vector b)
> {
>     return (a.x-b.x)*(a.x-b.x)+(a.y-b.y)*(a.y-b.y);
> }
> 
> int Rotating_calipers(Point *conv,int t)
> {
>     int ans=0,j=2;
>     for(int i=1;i<=t;i++)
>     {
>         while (fabs(Cro(conv[i]-conv[j],conv[i+1]-conv[j])) < fabs(Cro(conv[i]-conv[j+1],conv[i+1]-conv[j+1])))
>         {
>             j++;
>             if(j>t) j=1;
>         }
>         ans=max(ans,dis2(conv[i],conv[j]));
>     }
>     return ans;
> }
> 
> int main()
> {
>     scanf("%d",&n);
>     for(int i=1;i<=n;i++)
>     {
>         int x,y;
>         scanf("%d %d",&x,&y);
>         a[i].x=x,a[i].y=y;
>     }
>     conv_p=Convex_Hull(a,n,conv);
>     printf("%d",Rotating_calipers(conv,conv_p));
> 
>     return 0;
> }
> ```
>
> 





## 旋转卡壳(Rotating calipers)

https://www.jvruo.com/archives/79/

用于求平面最远点对



## 半平面交

https://www.jvruo.com/archives/67/

这玩意也太复杂了……



# 图论

## [树链剖分(HLD)](https://oi-wiki.org/graph/hld/)

用于解决树上路径修改、查询等问题。将树划分成若干重链后，采用重链dfs优先方式获得dfn，这样一来树上节点的dfn是连续的。维护dfn和点的关系，同时使用数据结构（线段树等）维护连续dfn上的节点信息以做到快速树链上查询和修改。

### 重链剖分

**重子节点**：子节点中**子树最大**（子树中节点最多，包含树根）的一个子节点。

**轻子节点**：除了重子节点外的子节点

**重边**：根到重子节点的边

**轻边**：除了根到重子节点的边外的其他到子节点的边

**重链**：若干由重边连成的链（其余落单节点也视为一条重链）

通过两次dfs实现，第一次dfs标记重子节点、深度、父亲、子树大小；第二次标记dfn（重边优先遍历）、rnk（dfn的反函数，也即通过dfn获得节点原标号）、top（所在重链链顶）。

**性质**：

- 树上每个节点都属于且仅属于一条重链，所有的重链将整棵树 完全剖分。
- 重边优先遍历，则最后每条重链的dfn（top的dfn）是上升的，每个节点按照dfn排序即可得到连续的剖分链。
- 子树内、链内的dfn都是连续的。
- 向下经过轻边，则子树大小至少除以2。
- 重链数量不超过$$O(\log n)$$

**路径上维护**：每次选择深度较大的链往上跳，直到两点在同一条链上。

**子树维护**：记录所在子树连续区间末端的结点（他喵的不是直接记录子树dfn最大是谁就行了吗（））

**LCA**：和路径上维护相似，不过当两个节点跳到同一链时，取深度较小节点作为LCA





> [P2590 [ZJOI2008\]树的统计](https://www.luogu.com.cn/problem/P2590)
>
> 基本上也是模板题了
>
> [P3379 【模板】最近公共祖先（LCA）](https://www.luogu.com.cn/problem/P3379)
>
> 传统应用
>
> [P3384 【模板】轻重链剖分](https://www.luogu.com.cn/problem/P3384)
>
> 模板题
>
> [P3976 [TJOI2015\]旅游](https://www.luogu.com.cn/problem/P3976)
>
> 从a走到b，路径上找两个点买卖（买点和卖点要一个在前一个在后）（亏钱则输出0），完事后修改路径上的点权值。
>
> 解法：树剖后，线段树维护路径权值最大值、最小值、从根到叶子的最大最小值差(RTL)、从叶子到根的最大最小值差(LTR)。维护这几个值即可。

## 树上启发式合并（DSU on Tree）

参考：https://www.cnblogs.com/zwfymqz/p/9683124.html#_label1_4

用于解决一类符合下述特征的题：

- 对任意的子树询问（也即统计答案通常只和子树有关）
- 无修改

**流程**：

对于每个节点x

- 递归解决所有轻子树，不保留影响
- 递归解决重子树，保留影响 （通过传递参数表明）
- 再扫描一遍轻子树，同时统计答案
- 将答案保存到ans[x]
- 根据参数，如果需要删除子树的影响，就尽数删除之（再遍历一遍整个子树）（注意这里区分答案和影响！）

这里的影响是用来辅助统计当前子树根节点答案的东西。例如如果要数子树有多少种颜色，那么cnt数组表示第i种颜色出现了多少次，这就是需要在扫描子树的时候需要统计的影响，答案更新依赖于此。

**复杂度**：（来源于马前卒）

一个点被访问到，只有两种情况

1、在暴力统计轻边的时候访问到。

根据前面的性质，该次数<logn

2、通过重边 / 在遍历的时候被访问到

显然只有一次

> [cf600E. Lomsat gelral](http://codeforces.com/contest/600/problem/E)
>
>  题意：给出一个树，求出每个节点的子树中出现次数最多的颜色的编号和（可能有多种颜色出现次数都是最多的）
>
> 解法：影响就是上文中说的cnt（子树中某种颜色出现了多少次），同时开一个辅助变量maxval来记录出现次数最多的颜色的出现次数是多少。

## 有向图Tarjan （待补充）



## (可能是)边分治

HDU2376 Average Distance

分成 子树 乘以 子树外的点，再乘以边的权值，即可得到边的贡献。

严格来说应该是贪心而不是边分治……



# 字符串

## 扩展kmp

(by hqh 20201018)

https://oi-wiki.org/string/z-func/

下文中字符串从1开始计数。

**LCP**：最长公共前缀

定义字符串a(长为m)关于模式串b的**extend数组**：extend[i]表示a的子串a[i~m]和b的LCP长度。如a='abcde'，b='bcd', 则a的extend[2]=3（最长公共前缀为bcd）

定义字符串b(长为n)的**z函数**：z[i]表示b[i~n]与b自身的LCP长度。如b='ababc'，z函数为[5 0 2 0 0]。z[1]实际上是无意义的，一般定义其为b的长度。

**求z函数的算法**：

核心思想是在采用暴力匹配的基础上，求z[i]前先初始化z[i]为之前某个已经求出的值，然后再暴力匹配。

假设现在求到某个位置i，记录之前能访问到的最右位置为r，对应访问到这个位置的下标为l。则b中，l\~r与1\~r-l+1对应子串相等。这意味着i开始处某一部分和1\~r-l+1是一样的，**见下图**。因此至少可以保证z[i]有z[i-l+1]那么多。当然也有可能z[i-l+1]作为i处的初始长度会超出目前已经知道的最右位置，因此应该限制其不能超过r（因为r的右边我们是不知道的，需要暴力匹配得知）。

![](G:\code\LSF_project\pic\z_function1.png)

初始化之后直接暴力匹配即可。可以证明此种做法复杂度是On的。（证明参见链接）

```c++
void get_z(int n=b_l)
{
    int l=1,r=1;
    z[1]=b_l; //undefined
    for(int i=2;i<=n;i++)
    {
        if(i<=r)
            z[i]=min(z[i-l+1],r-i+1);//init        
        while (i+z[i]<=n && b[z[i]+1]==b[i+z[i]]) //i+z[i] is next position, so accessible pos is i+z[i]-1
            z[i]++;
        if(i+z[i]-1>r)
            l=i,r=i+z[i]-1;
    }
}
```

**求extend的算法：**和z函数差不多

```c++
void exkmp(int m=a_l,int n=b_l) //get extend array
{
    while(extend[1]<=m && a[1+extend[1]]==b[1+extend[1]]) extend[1]++;
    int l=1,r=1;
    for(int i=2;i<=m;i++)
    {
        if(i<=r)
            extend[i]=min(z[i-l+1],r-i+1);
        while (i+extend[i]<=m && b[extend[i]+1]==a[i+extend[i]]) 
            extend[i]++;
        if(i+extend[i]-1>r)
            l=i,r=i+extend[i]-1;
    }
}
```

**模式匹配**：显然求出extend数组后，extend[i]=n处意味着匹配成功（从i开始的一段子串与b完全重合）。

**本质不同子串数目**：假设对于字符串a（长为n），后i+1字符构成的子串中本质不同子串数目已经求出（设为ans），现在我们来求后i个字符中本质不同子串数目。这个问题相当于在字符串头添加了一个字符c,使得a:=c+a。再用On的时间求出新字符串的z函数，然后遍历z[2\~n]，这之中最大的值z[j]即为新字符串的最长重合前缀，显然小于此长度的新字符串前缀也会重合，因此ans+=n-max(z[2\~n])

**字符串压缩**：（待补充）

> [P5410 【模板】扩展 KMP（Z 函数）](https://www.luogu.com.cn/problem/P5410)
>
> 模板题

> [P2375 [NOI2014\]动物园](https://www.luogu.com.cn/problem/P2375)
>
> 题意：求字符串a的num数组，num[i]表示子串a[1\~i]后缀与前缀相同且前后缀互不重叠的数目。如"abababa",num[7]=2，因为"a", "aba"为相同前后缀，但"ababa"不是，因为重叠了。
>
> 解法：考虑到a的z函数的意义为i开始的子串与自身的LCP长度，那么对于更短的长度（设此时终点为j），a[i\~j]也是a的前缀。比如a="bbbbb",z[3]=3，也即a[1\~3]与a[3\~5]相同，那么a[1\~2]与a[3\~4], a[1]与a[3]也相同。
>
> 因此假如没有不能重叠的限制，那么对于位置i，其z[i]会对i\~i+z[i]-1处的num都有1的贡献。考虑上不能重叠的限制，因为z[i]可能很大而导致公共前缀重叠，因此对于z[i]>=i的部分的贡献不予考虑即可。也即z[i]对i\~min(i+z[i]-1,i+(i-1)-1)的numm有贡献。
>
> 最后差分一下再累加差分贡献即可。（当然这题你也可以用一般kmp做）

>### [CF1051E Vasya and Big Integers](https://www.luogu.com.cn/problem/CF1051E)
>
>题意：给三个大数a,L,R, 求将a划分成每段构成的数无前导零且大小介于[L,R]之间的方案数（Mod 998244353）
>
>$1\le a\le 1e(1e6), 0\le L\le R\le 1e(1e6)$，也即上限有十万位。
>
>解法：设$dp[j]$表示以j\~j+1之间作为一段的边界，划分的方案数。则
>$$
>dp[j]=\sum(dp[i])
>$$
>其中i为同时满足下面3个条件的所有正整数：
>
>1. $0\le i \le j-1$
>2. $a[i+1]\neq 0$ （除非L仅有一位，参见下文讨论）
>3. $a[i+1 \text{~} j]$构成的数在[L,R]之内
>
>条件2是为了限制有前导零的划分不能被转移。只有一种情况下这段能以0开头，就是这段构成的数本身就是0。这种情况下L=0，因此i=j-1这种情况的划分需要做特判。
>
>除此之外，易发现满足条件的i是连续的。设a[i+1\~j]构成的数位M，|M|为一个数的数位长度，则显然|L|<|M|<|R|的情况都满足条件3，唯独取等号处需要比较L或者R与M的大小方能确定对应位置的i是否满足条件3。而数的比较可通过比较两数的LCP后一位解决。求出L、R的z函数，再求出a关于L、R的extend数组即可。当然最后要保证i满足条件1.
>
>连续的一段i的转移可通过保存dp的前缀和dpsum实现，但当a[j+1]=0时,其不应该被计入dpsum中，以免破坏条件2.
>
>具体实现参见代码。



## AC自动机

（By hqh 20201031）

https://oi-wiki.org/string/ac-automaton/

自动机是一种有向图，图上的结点表示处于某种状态，而边表示状态的转移。

AC自动机的主要用途是“多模式匹配”，也即给定若干个模式串，再给定一个主串，要求出每个模式串在主串中的出现次数。

主要的思想是将多个模式串构建trie树，然后通过修改trie树的一些边，来实现多模式快速匹配。因此你可以理解为在trie树上跑kmp。

对AC自动机而言，每个结点的状态表示当前多模式匹配的进度（在匹配结束处，对应结点拥有一个匹配完成标识符end[u]表示成功匹配一个模式串）；边是字符，引导转移到下一个状态。

 ### fail指针

构建自动机的关键在于fail指针。构建完成trie树后，我们利用bfs构建fail指针。

fail指针代表的意义是指向trie树中的另一个结点，这个结点满足从根走到对应结点所对应的字符串，是当前结点对应的字符串的**最长后缀**。比如当前结点对应字符串是abaaa，trie树上还有另一条路径表示aaa，则其fail指向此结点。

当我们匹配过程中失配时（走到某一个结点后，trie树上对应下一个字符的结点不存在），那么我们就跳到fail，再继续尝试匹配。这相当于抛弃一部分当前模式串前缀使得匹配能够继续进行。

具体构建方式是用bfs，取出队头结点u，然后遍历此节点的接下来的所有可能字符i（0\~25），按照如下代码递推地更新下一个结点的fail指针：

```
fail[trie[u][i]]=trie[fail[u]][i];
```

也即让下一个结点的fail指针，指向当前结点fail指针的下一个字符i对应的结点。因为是用bfs，可以保证下一个结点更浅的结点已经被更新，所以可以这么做。

其他详细信息请参见文头所给链接。

### 字典图

使用fail指针在失配时跳跃很蛋疼，因为如果跳了之后还是失配那么你还是得继续从fail[u]继续跳到fail[fail[u]]，那么有没有更好的方法呢？

注意到如果在u处前往i字符时失配，那么trie\[u\]\[i\] 必然未定义，我们通过改写trie\[u\]\[i\] 使得其直接指向下一个不失配的fail即可。

比如：

```
。。。
（前面是BFS取出队头u结点）

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
。。。
```

使用`trie[u][i]=trie[fail[u]][i];` 在求fail的同时改写未定义trie指针即可。这样，在匹配时可以直接令`u=trie[u][i]`（不论是否失配）。

这样之后trie其实已经不再是一棵树，而是一个会自指的图，我们称之为字典图。

### last优化

事实上，求每个模式串在主串中的出现次数，不仅需要在字典图上遍历，而且每遍历一个结点还得跳fail（不论失配与否），因为你并不知道当前匹配位置有多少个模式串能匹配。

比如：主串abaaa，模式串aaa、baaa；匹配到主串的最后一个a时，会在trie图上的baaa处，但还是得跳fail，跳到trie图上的aaa上，才能知道这个字符串也在主串最后一个a处成功匹配。

这个做法带来的代价其实可以换成另一种做法以彻底改善（见下文fail树），但这里先说明另一种优化方法。

在跳fail的过程中，并不是每一个结点都是某一模式串的终点。而在字典图构建完毕后，fail树其实对匹配而言意义就不大了，因此我们希望fail能直接跳到“某个模式串”的结尾。我们引入last数组，其可以直接跳到“某个模式串”的结尾。

我们在构建fail指针时添加这么一句话：

```
...
                fail[trie[u][i]]=trie[fail[u]][i];
                last[trie[u][i]]=(id[fail[trie[u][i]]]==0)?(last[fail[trie[u][i]]]):(fail[trie[u][i]]); //有效字符串结尾
                q.push(trie[u][i]);

...
```

id[u] 表示结点u作为某一模式串的结尾，对应的字符串编号（如果u不是某一串的结尾节点，那么其为0）。通过last的递推即可求得last数组。在匹配求解每个模式串在主串中的出现次数的过程中，改跳last而非fail即可。

但这么做的优化仅仅只是常数上的，如果给若干个模式串a、aa、aaa、aaaa……，那还是会被卡，因此我们引入fail树。

### fail树

将所有结点的fail指针反过来接，将构成一个以trie图0结点为根的树，成为fail树。从fail树向下走，相当于在当前字符串前添加前缀。比如abb，从fail树向下走到fuabb（当然可能会有好几个孩子，这里只是举个例子）。

构建此树也很简单，在fail指针构建完成后：

```
    for(int i=1;i<=total;i++)
    {
        adde(fail[i],i);
    }
```

回到上面的问题，之前我们通过last数组进行了优化。现在利用fail树，我们可以利用树上差分的方法，指在当前结点u处添加一个标记1，表示自u到根的路径都会受此标记影响。匹配结束之后对fail树做dfs以求出差分的相加值，最后看每一个结点的值，此值就是对应的字符串的出现次数。

当然fail树还有很多用途，除了差分之外，还可以对其dfn+线段树、树状数组、树链剖分……嗯。



> [P3808 【模板】AC自动机（简单版）](https://www.luogu.com.cn/problem/P3808)
>
> 板子题，只用求出出现的模式串而不需要统计个数，直接跳fail指针完成匹配。
>
> [P3796 【模板】AC自动机（加强版）](https://www.luogu.com.cn/problem/P3796)
>
> 同样是板子题，求出出现的模式串的次数，改用last指针跳跃即可。
>
> [P5357 【模板】AC自动机（二次加强版）](https://www.luogu.com.cn/problem/P5357)
>
> 题意和加强版相同，但需要用fail树+差分才能过（规避掉匹配时跳任何指针），具体做法上文已有描述
>
> [Educational Codeforces Round 97 (Rated for Div. 2)](https://codeforces.com/contest/1437)[G：Death DBMS](https://codeforces.com/contest/1437/problem/G)
>
> 题意：给一堆模式串（可能本质相同，但他们的编号不同），每个编号对应的字符串初始权值为0，然后给出两种操作：第一种操作给出一个字符串，求出其中出现的模式串的最大权值；第二种操作给出两个数x y，要将第x个模式串的权值改为y
>
> 解法：注意此题中，两个本质相同的字符串可能会有不同的权值。
>
> 使用ac自动机构建字典图，那么“两个本质相同的字符串可能会有不同的权值”就相当于在字典图的结点上维护多个权值（修改和查询最大值）。
>
> 可以在每个结点上开个动态开点的线段树。但为了实现方便，这里使用N个（N为字典图中结点个数）multiset来维护每个结点上的多个权值。
>
> 剩下的就简单了。对于操作一，用ac自动机匹配，每走到一个结点跳last（这题的数据跳fail会t），然后求对应结点multiset中值的最大值。对于操作2，在字典图上找到对应串后修改对应multiset（删除旧的权值并插入新权值）即可

## 后缀数组

**后缀数组SA**：字符串各个后缀的排名到下标的映射。如aabab，按字典序从小到大排，后缀分别为aabab、ab、abab、b、bab，sa\[1\~5 \] =1 4 2 5 3

**排名数组RK**：从某一位置i开始到字符串末尾所构成的后缀，在所有后缀中的排名。相当于SA的逆映射。

**求法**：倍增法。每次考虑长度w为1、2、4、8……（到小于等于n的最大2的幂次为止）的子串，对于某个位置i，用其位置i的sa对应值作为第一关键字，位置为i+w的sa对应值作为第二关键字（如果i+w大于n就看成无穷小），进行排序。由于只有两个关键字，因此排序使用线性排序法 （比如计数排序或者基数排序，下文中主要采用计数排序，因其复杂度只和值域有关），可将求SA的整体时间复杂度下降至O（nlogn）。具体参见代码。

另一种求法是用后缀自动机。参见“后缀自动机”一节。

### Height 数组

含义：第i名和第i-1名(注意是排名)的最长公共前缀长度

```
height[i]=lcp(sa[i],sa[i-1])
```

引理：第i个位置的后缀对应的height值，大于等于上一个位置（i-1）的height值-1

```
height[rk[i]]>=height[rk[i-1]]-1
```

求解：借助上面的引理可在On时间内求出height数组。

```c++
for (i = 1, k = 0; i <= n; ++i) {
  if (k) --k;
  while (s[i + k] == s[sa[rk[i] - 1] + k]) ++k;
  ht[rk[i]] = k;  // height太长了缩写为ht
}
```

### Height 数组的应用

## 后缀自动机（Suffix Automator，SAM）

后缀自动机是能够匹配某个字符串S的所有后缀的自动机。就是说取S的任何一个后缀（包括S自身），在SAM中从开始节点t0走，肯定都能走到某一个终止状态。

符合这个性质的图是有向无环图，并且有很多图都符合上面的要求。但SAM是其中节点数量最少的一个（**极小性**）。

### endpos等价类、SAM中的节点意义

字符串S=abcbc。考虑子串bc在S的终止位置（endpos集合），为3、5。子串c在S中的终止位置也为3、5。像这样终止位置集合完全相同的S的子串称为endpos等价类。

在SAM中，**SAM的一个节点就代表一个endpos等价类**。因此如果有一个节点代表子串bc，则这个节点也代表子串c，反之亦然。

### 节点的最长串、节点的len

显然一个等价类中可能包含多个S的子串，我们取其中最长的一个，作为这个等价类对应节点v的最长串，并记len（v）为这个最长串的长度。

### 节点的link（father）

节点的最长串的前几个（按照长度递减来数）后缀都会在这个节点所在等价类中。但可能从之后某个位置开始的后缀就都不在这个等价类中了。我们记不在当前节点等价类中最长的串为B，则B对应另一个等价类u（如果B不存在，就令u为SAM的起始节点）。我们令link（v）=u，称为**后缀链接**。

所有SAM中的节点（除了起始节点）都有后缀连接。这些后缀连接构成一颗（有向）树，从叶子指到树根。称之为**后缀链接树**。

### 后缀链接树、终点节点

![](https://oi-wiki.org/string/images/SAM/SA_suffix_links.svg)

左图是字符串S=abcbc的后缀自动机（SAM），右图为其后缀链接树。

字符串S的所有前缀在自动机中也对应着不同的节点。从初始状态t0开始按照字符串S走，所经过的节点被称为**终点节点**。如右图中的a、ab、abc、abcb、abcbc。（注意和终止状态区分）

定义每个节点的**终点集合**：每个节点的终点集合等于其 **子树** 内所有终点节点对应的终点的集合。比如b节点的子树中有ab、abcb节点，b的终点集合就是ab、abcb节点。

- 如果节点 A 是 B 的祖先，则节点 A 对应的字符串是节点 B 对应的字符串的 **后缀** 。
- S的前缀子串s1(1,p) s2(1,q)对应的最长公共后缀，是两串对应节点u、v的LCA对应字符串。
- 节点len的大小随着深度递增而不下降。
- 后缀链接树中的非叶子节点对应的最长串，这个最长串相当于从其终点集合中的任意一个终点的下标往前取这个非叶子节点的len个长度（包含这个开始取的位置）的字符组成。

- 后缀链接树中的叶子节点就是终点节点。但反过来不一定成立（如S=ababa，aba是前缀，但aba出现了两次，有更长的ababa作为其儿子）。这是由于其他出现只有一次的非前缀串都被包含在某一只出现一次的前缀串中了。换句话说如果bab只出现了一次，那么（从头到）...bab也肯定只出现一次。
- 设所有终点节点的siz为1。从叶子节点开始bfs，累加siz，非叶子节点的siz值就是这个节点的终点集合大小，也即这个节点对应最长串出现的次数。（后面模板题会用到）

### 构建方法

步骤有点麻烦（其实还好），参见https://oi-wiki.org/string/sam/

- 状态数不会超过2n-1
- 转移数不会超过3n-4
- 构建的时间复杂度为线性级别

### 应用

1. 给定的字符串P是否在S中出现：
   直接从起点走，中途遇到某个字母走不下去了就说明没出现
2. 不同子串个数：
   有向无环图动态规划。或者用len（i）-len（link（i））对每个结点求和。（这个式子是相当于这个状态对整个串S的不重复子串贡献）
3. 求所有不同子串总长度

# 杂项、奇技淫巧

## vector、set的合并

1）在a后面插入b，方法为

a.insert(a.end(),b,begin(),b.end());

2）在a的前面插入b，方法为

a.insert(a.begin(),b,begin(),b.end());

合并set:

a.insert(b,begin(),b.end());

## 交互题

> CF Global Round 9 [**F. Integer Game**](https://codeforces.com/contest/1375/problem/F)
>
> ```c++
> #include <iostream>
> using namespace std;
> long long a[4],p1,p2;
> int main()
> {
>     ios::sync_with_stdio(false);
>     for(int i=1;i<=3;i++) cin>>a[i];
>     cout<<"First"<<endl;
>     cout<<(long long)1e11<<endl;
>     cout.flush();
> 
>     cin>>p1;
>     a[p1]+=(long long)1e11;
>     long long temp=2*a[p1];
>     for(int i=1;i<=3;i++)
>     {
>         if(i!=p1) temp-=a[i];
>     }
>     cout<<temp<<endl;
>     cout.flush();
> 
>     cin>>p2;
>     a[p2]+=temp;
>     cout<<a[p2]-a[p1]<<endl;//d=c-a=(2c-a)-c=a[p2]-a[p1]
>     cout.flush();
>     return 0;
> }
> ```
>
> CF 669 [1407C — Chocolate Bunny](https://codeforces.com/contest/1407/problem/C)
>
> ```c++
> #include <iostream>
> #include <cstdio>
> using namespace std;
> int n,a[10100];
> 
> int main()
> {
>     ios::sync_with_stdio(false);
>     cin.tie(0);
>     cout.tie(0);
>     cin>>n;
>     int pos=1;
>     for(int i=2;i<=n;i++)
>     {
>         int x,y;
>         cout<<"? "<<pos<<" "<<i<<endl;
>         cout.flush();
>         cin>>x;
> 
>         cout<<"? "<<i<<" "<<pos<<endl;
>         cout.flush();
>         cin>>y;
> 
>         if(x<y)
>         {
>             a[i]=y;
>         }
>         else
>         {
>             a[pos]=x;
>             pos=i;
>         }
>     }
>     a[pos]=n;
>     cout<<"! ";
>     for(int i=1;i<=n;i++)
>     {
>         cout<<a[i]<<" ";
>     }
>     cout<<endl;
>     cout.flush();
>     return 0;
> }
> ```
>
> 

## priority_queue 

当然也能重载运算符搞定

```
//升序队列，小顶堆
priority_queue <int,vector<int>,greater<int> > q;
//降序队列，大顶堆
priority_queue <int,vector<int>,less<int> >q;
//对于longlong
greater<long long>()

```



## 动态分配二维数组内存

````c++
#include <stdio.h>
#include <stdlib.h>
int allsum(int *a,int n,int m)
{
    int (*b)[m]=(int(*)[m]) a;//转换为数组指针
    int sum=0;
    for(int i=0;i<n;i++)
        for(int j=0;j<m;j++)
            sum+=b[i][j];//统计答案
    return sum;
}

int n,m;
int main()
{
    scanf("%d%d",&n,&m);
    int (*a)[m]=(int(*)[m]) calloc(n*m,sizeof(int));//按照大小分配数组内存
    for(int i=0;i<n;i++)
        for(int j=0;j<m;j++)
            scanf("%d",&a[i][j]);//输入

    printf("%d",allsum(&a[0][0],n,m));
    free(a);
    return 0;
}
````

## 自然数幂和

https://zh.wikipedia.org/wiki/%E7%AD%89%E5%B9%82%E6%B1%82%E5%92%8C
$$
\sum_{i=1}^{n} i^{0} =n\\

 \sum_{i=1}^{n} i^{1} = \frac{n(n+1)}{2} = \frac{1}{2}n^2 +\frac{1}{2} n \\

 \sum_{i=1}^{n} i^{2} = \frac{n(n+1)(2n+1)}{6} = \frac{1}{3}n^3 + \frac{1}{2}n^2 + \frac{1}{6}n\\

 \sum_{i=1}^{n} i^{3} = \left[\frac{n(n+1)}{2}\right]^{2} = \frac{1}{4}n^4 + \frac{1}{2}n^3 + \frac{1}{4}n^2\\

 \sum_{i=1}^{n} i^{4} = \frac{n(n+1)(2n+1)(3n^2+3n-1)}{30} = \frac{1}{5}n^5 + \frac{1}{2}n^4 + \frac{1}{3}n^3 - \frac{1}{30}n\\

 \sum_{i=1}^{n} i^{5} = \frac{n^{2}(n+1)^{2}(2n^2+2n-1)}{12} = \frac{1}{6}n^6 + \frac{1}{2}n^5 + \frac{5}{12}n^4 - \frac{1}{12}n^2\\

 \sum_{i=1}^{n} i^{6} = \frac{n(n+1)(2n+1)(3n^4+6n^3-3n+1)}{42} = \frac{1}{7}n^7 + \frac{1}{2}n^6 + \frac{1}{2}n^5 - \frac{1}{6}n^3 + \frac{1}{42}n\\

 \sum_{i=1}^{n} i^{7} = \frac{n^2(n+1)^2(3n^4+6n^3-n^2-4n+2)}{24} = \frac{1}{8}n^8 + \frac{1}{2}n^7 + \frac{7}{12}n^6 - \frac{7}{24}n^4 + \frac{1}{12}n^2\\

 \sum_{i=1}^{n} i^{8} = \frac{n(n+1)(2n+1)(5n^6+15n^5+5n^4-15n^3-n^2+9n-3)}{90}=\frac{1}{9}n^9 + \frac{1}{2}n^8 + \frac{2}{3}n^7 - \frac{7}{15}n^5 + \frac{2}{9}n^3 - \frac{1}{30}n\\

 \sum_{i=1}^{n} i^{9} = \frac{n^2(n+1)^2(n^2+n-1)(2n^4+4n^3-n^2-3n+3)}{20}=\frac{1}{10}n^{10}+\frac{1}{2}n^9+\frac{3}{4}n^8-\frac{7}{10}n^6+\frac{1}{2}n^4-\frac{3}{20}n^2\\

 \sum_{i=1}^{n} i^{10} = \frac{n(n+1)(2n+1)(n^2+n-1)(3n^6+9n^5+2n^4-11n^3+3n^2+10n-5)}{66}=\frac{1}{11}n^{11}+\frac{1}{2}n^{10}+\frac{5}{6}n^9-n^7+n^5-\frac{1}{2}n^3+\frac{5}{66}n\\

\sum_{i=0}^{n} i^{m-1} = \sum_{k=0}^m  S_k^m n^k，其中S_0^m = 0，S_m^m = \frac{1}{m}，當m−k為大於1的奇數時，S_k^m = 0\\

\sum_{i=0}^{n} i^m = {1\over{m+1}}\sum_{i=0}^m{m+1\choose{i}} B_i (n+1)^{m+1-i},(B_i为伯努利数)\\

\displaystyle \sum_{i=1}^n i^{m+1} = \sum_{k=0}^m  L_k^m \binom{n+k+1}{m+2},\left(L_k^m = \sum_{r=0}^k (-1)^r \binom{m+2}{r} (k+1-r)^{m+1}\right)\\
$$

## Python 重定向输出输入

```python
import sys
sys.stdin=open("l_in.txt","r")
sys.stdout=open("l_out2.txt","w")
```

## atan2(y,x)

这个函数定义在cmath里，返回一个点(x,y)的极角，值域在$(-\pi,\pi]$之间。

主要用于极角排序等。

## 输入一行

### cin.getline(char *m, int size)

输入最多m个字符到地址m处开始的字符串。注意如果之前用cin读入的话要通过特殊手段消除之前的换行符，下同。

### getline(cin,*str)

从流中读入到str中

### char* fgets(char* str, int cnt, std::FILE* stream)

从流中（文件流，或stdin）读入最多cnt个字符的字符串到str中。



ps：下面的操作可以移除多余的空白符

```c++
cin>>ws
```



## 快速计算二进制中1的个数

（https://www.cnblogs.com/graphics/archive/2010/06/21/1752421.html）


```c++
int BitCount5(unsigned int n)
{
    unsigned int tmp = n - ((n >>1) &033333333333) - ((n >>2) &011111111111);
    return ((tmp + (tmp >>3)) &030707070707) %63;
}
```