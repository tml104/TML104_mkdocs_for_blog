## pbds和其他

参考：https://oi-wiki.org/lang/pb-ds/

### 平衡树

参考：

- https://blog.csdn.net/weixin_30593443/article/details/99322952

- https://www.luogu.com.cn/blog/Chanis/gnu-pbds

pbds中的平衡树与STL中的set类似，但支持查询下标等操作。

#### 头文件、命名空间

```c++
#include <ext/pb_ds/assoc_container.hpp>
#include <ext/pb_ds/tree_policy.hpp>
//或者直接用
#include <bits/extc++.h>
using namespace __gnu_pbds;
```

#### 定义

```c++
tree<pair<int ,int>,null_type,less<pair<int ,int> >,rb_tree_tag,tree_order_statistics_node_update> t;//红黑树


```

**模版参数**：

1. **数据类型**
2. 固定是null_type,无映射
3. **比较函数**：一般用模板类less或者greater, greater是小根堆（升序队列），可能需要重载结构体的大于号
4. **树的类型**：一般用rb_tree_tag(红黑树)，其他还有splay_tree_tag（但据说容易T）
5. **更新方式**：固定是tree_order_statistics_node_update

#### 常用方法

##### t.size()

返回大小

##### t.begin() t.end()

返回起始和末尾迭代器

##### t.node_begin() t.node_end()

返回指向树根/树末尾的迭代器

##### t.insert(x)

插入值，但插入重复的值不会造成任何影响。要想插入重复值，请将数据类型定义为pair并改变第二下标（推荐做法是第二下标就用i之类的独一无二的数来表示）。（似乎会返回一个很诡异的对象）

##### t.erase(x) / t.erase(it)

删除值/指针指向的值。

##### t.order_of_key(x)

由值找下标（下标从0计数，下同），返回下标。如果x本来不存在于平衡树之中，那么返回第一个大于等于该值的元素的下标，特别地，如果x比任何元素都大，返回值与t.size()相等

##### t.find_by_order(x)

由值找对象，返回指向改值的指针。

##### t.join(t2)

将同类型的树t2合并到t中，但如果t2和t有相同元素会甩出异常。

##### t.split(v,t2)

将t中小于等于v的元素塞入b中，剩余元素留在t中。

##### t.lower_bound(x)

查找第一个大于等于x的元素，并返回指向它的指针。

##### t.upper_bound(x)

查找第一个大于x的元素，并返回指向它的指针。



#### 使用例子

> 2020CCPC Qinhuangdao E
>
> ```c++
> #include <bits/stdc++.h>
> #include <ext/pb_ds/tree_policy.hpp>
> #include <ext/pb_ds/assoc_container.hpp>
> using namespace __gnu_pbds;
> using namespace std;
> typedef long long ll;
> const int N=2e5+10;
> int T;
> pair<int ,int> li[N];
> int p;
> 
> tree<pair<int ,int>,null_type,less<pair<int ,int> >,rb_tree_tag,tree_order_statistics_node_update> t;//红黑树
> 
> int n,rat;
> 
> bool check()
> {
>     auto it=--t.end();
>     if(li[it->second].second==it->first) return false;
>     return true;
> }
> 
> int main()
> {
>     ios::sync_with_stdio(false);
>     cin.tie(0);
>     cout.tie(0);
> 
>     cin>>T;
>     for(int _=1;_<=T;_++)
>     {
>         t.clear();
>         p=0;
>         cin>>n>>rat;
>         for(int i=1;i<=n;i++)
>         {
>             cin>>li[i].first>>li[i].second;
>             t.insert({li[i].first,i});
>         }
> 
>         int ans=0;
>         while (check())
>         {
>             auto it=--t.end();
>             int pass_sc=ceil(it->first*(0.01*rat));
>             auto findit=t.lower_bound({pass_sc,0});
>             int tempans=t.order_of_key(*findit); //小于pass_sc的数数量
> 
>             ans=max(ans,n-tempans);
> 
>             pair<int,int> newval{li[it->second].second,it->second};
>             t.erase(it);
>             t.insert(newval);
>         }
>         auto it=--t.end();
>         int pass_sc=ceil(it->first*(0.01*rat));
>         auto findit=t.lower_bound({pass_sc,0});
>         int tempans=t.order_of_key(*findit); //小于pass_sc的数数量
> 
>         ans=max(ans,n-tempans);
> 
>         pair<int,int> newval{li[it->second].second,it->second};
>         t.erase(it);
>         t.insert(newval);
> 
> 
>         cout<<"Case #"<<_<<": ";
>         cout<<ans<<endl;
>     }
>     return 0;
> }
> ```
>
> 



### 优先队列

#### 头文件、命名空间

```c++
#include<ext/pb_ds/priority_queue.hpp>
或者
#include<bits/extc++.h>
using namespace __gnu_pbds;
```

#### 定义

```c++
priority_queue<int,greater<int>,pairing_heap_tag> q;
```

**模版参数**：

1. **数据类型**：

2. **比较函数**：greater是小根堆，默认大根堆

3. **堆类型**：
   pairing_heap_tag：配对堆（常用）

   binary_heap_tag：二叉堆

   binomial_heap_tag：二项堆

   rc_binomial_heap_tag：冗余计数二项堆

   thin_heap_tag： 除了合并的复杂度都和 Fibonacci 堆一样的一个 tag

   

4. **Allocator**：空间配置器

#### 常用方法

##### q.push(x)
插入元素

##### q.pop()
弹出堆顶元素

##### q.top()
堆顶元素

##### q.size()
大小

##### q.empty()
判断是否为空

##### q.modify(it,x)
修改迭代器指向位置的值为x

##### q.erase(it)
删除迭代器it指向位置的值

##### q.join(q2)
将q2堆中的内容合并到q中，并清空q2



#### 使用例子

HDU5575 Discover Water Tank 2015上海现场赛D题 （树形dp，并查集，左偏树）

```c++
// shuigang

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

#include <ext/pb_ds/priority_queue.hpp>
// using namespace __gnu_pbds;

typedef long long ll;
const int N = 3e5 + 10;
const int Inf = 1e9;
int T;

int n, m;
int h[N];
struct B
{
    int h, id;
    int l, r;
    bool operator<(const B &b) const
    {
        if (h != b.h)
            return h < b.h;
        return id < b.id;
    }
} baffle[N];

struct Q
{
    int x, y, z;

    bool operator<(const Q &b) const
    {
        if (y != b.y)
            return y < b.y;
        return z > b.z; //  1 1 1 0 0
    }

    bool operator>(const Q &b) const
    {
        if (y != b.y)
            return y > b.y;
        return z < b.z; //  1 1 1 0 0
    }
};

int dp[N][2];

using PQ = __gnu_pbds::priority_queue<Q, std::greater<Q>, __gnu_pbds::pairing_heap_tag>;

PQ qq[N];

struct Unionfindset
{
    // local
    int fa[N]; //fa,sz：父亲和秩的大小

    //初始化并查集
    void init(int n)
    {
        for (int i = 1; i <= n; i++)
        {
            fa[i] = i;
        }
    }

    //取得父亲并路径压缩
    int getfa(int x)
    {
        if (fa[x] == x)
            return x;
        return fa[x] = getfa(fa[x]);
    }

    //合并x、y
    int merge(int x, int y)
    {
        int x_fa = getfa(x), y_fa = getfa(y);
        if (x_fa == y_fa)
            return -1;
        fa[x_fa] = y_fa;

        return y_fa;
    }

    //检查是否在同一集合
    bool check(int x, int y) { return getfa(x) == getfa(y); }
} ufs;

void clear()
{
    for (int i = 1; i <= n; i++)
    {
        while (qq[i].size())
            qq[i].pop();
        dp[i][0] = dp[i][1] = 0;
        baffle[i]=baffle[0];
    }
    h[0] = h[n] = Inf;
    ufs.init(n);
}

std::pair<int, std::pair<int,int_least16_t>> cal(int id, PQ &pq, int limit) // dp[.][0] dp[.][1]
{
    std::vector<Q> vec; //stack
    int no_water = 0, yes_water = 0;
    while (pq.size() && pq.top().y + 0.5 <= limit)
    {
        vec.emplace_back(pq.top());
        if (pq.top().z == 0)
            no_water++;
        else
            yes_water++;
        pq.pop();
    }

    std::reverse(vec.begin(),vec.end());

    int ans = no_water;
    int yes_water2 = 0, no_water2=no_water;
    while (vec.size())
    {
        auto top = vec.back();
        vec.pop_back();
        yes_water2 += (top.z == 1);
        no_water2 -= (top.z == 0);
        while (vec.size() && vec.back().y == top.y)
        {
            yes_water2 += (vec.back().z == 1);
            no_water2 -= (vec.back().z == 0);
            vec.pop_back();
        }
        ans = std::max(ans, yes_water2 + no_water2);
    }
    return {ans, {yes_water,no_water}};
}

void pre1()
{
    std::vector<int> vec;
    vec.emplace_back(Inf);
    for (int i = 1; i <= n - 1; i++)
    {
        while (vec.size() && vec.back() <= baffle[i].h)
        {
            vec.pop_back();
        }
        baffle[i].l = vec.back();
        vec.emplace_back(baffle[i].h);
    }
}

void pre2()
{
    std::vector<int> vec;
    vec.emplace_back(Inf);
    for (int i = n - 1; i >= 1; i--)
    {
        while (vec.size() && vec.back() < baffle[i].h)
        {
            vec.pop_back();
        }
        baffle[i].r = vec.back();
        vec.emplace_back(baffle[i].h);
    }
}

void pre3()
{
    for (int i = 1; i <= n; i++)
    {
        int lim = std::min(h[i - 1], h[i]);
        auto calval = cal(i, qq[i], lim);
        dp[i][0] = calval.first;
        dp[i][1] = calval.second.first;
    }
}

void dpdp()
{
    std::sort(baffle + 1, baffle + n);

    for (int i = 1; i <= n - 1; i++) //baffle
    {
        int lfa = ufs.getfa(baffle[i].id);
        int rfa = ufs.getfa(baffle[i].id + 1);
        int ans0, ans1;

        qq[lfa].join(qq[rfa]);
        ufs.merge(rfa,lfa);

        auto calval = cal(lfa, qq[lfa], std::min(baffle[i].l, baffle[i].r));

        ans1=calval.second.first+dp[lfa][1]+dp[rfa][1];
        ans0=dp[lfa][0]+dp[rfa][0]+calval.second.second;
        ans0=std::max(ans0,dp[lfa][1]+dp[rfa][1]+calval.first);

        dp[lfa][0]=ans0;
        dp[lfa][1]=ans1;
    }
}

int main()
{
    scanf("%d", &T);
    for (int _ = 1; _ <= T; _++)
    {
        scanf("%d%d", &n, &m);
        clear();
        for (int i = 1; i <= n - 1; i++)
        {
            scanf("%d", &h[i]);
            baffle[i] = {h[i], i};
        }

        for (int i = 1; i <= m; i++)
        {
            int x, y, z;
            scanf("%d", &x);
            scanf("%d", &y);
            scanf("%d", &z);
            qq[x].push({x, y, z});
        }

        pre1();
        pre2();
        pre3();
        dpdp();

        // std::cout<<std::max(dp[1][0],dp[1][1])<<'\n';
        printf("Case #%d: %d\n",_,std::max(dp[1][0],dp[1][1]));
    }
    return 0;
}
```






### rope

块状数组，时间复杂度$O(n\sqrt n)$

#### 头文件、命名空间

```c++
#include <ext/rope>
using namespace __gnu_cxx;
```

#### 定义

```c++
rope<char> r;
```

**模版参数**：

1. **数据类型**

#### 常用方法

##### r.insert(pos, s[], len)

在r的pos处插入长度为len的s

##### r.append(s[], len)

在r的末尾处插入s长度为len的部分

##### r.substr(pos, len)

取得r的自pos开始长度为len的子序列（返回值仍为rope）

##### r.at(index)

访问下标index处的值

##### r.erase(pos, len)

删除pos开始长度为len的部分

##### r.copy(pos, len, s[])

从rope的下标pos开始的len个数用数组s代替，如果pos后的位数不够就补足

##### r.replace(pos, s[])

从rope的下标pos开始替换成数组s，s的长度为从pos开始替换的位数，如果pos后的位数不够就补足

##### 使用例子


> P4008 [NOI2003] 文本编辑器
> ```c++
> #include <iostream>
> #include <cstdio>
> #include <cstring>
> #include <memory.h>
> #include <algorithm>
> #include <map>
> #include <vector>
> #include <queue>
> #include <cmath>
> #include <ext/rope>
> using namespace std;
> using namespace __gnu_cxx;
> typedef long long ll;
> const int N=3e7+10;
> rope<char> r;
> int T;
> char op[20],s[N];
> 
> template <typename T> inline void read(T &x)
> {
>     char c;
>     int sgn=1;
>     x=0;
>     c=getchar();
>     while (c>'9' && c<'0')
>     {
>         if(c=='-') sgn=-1;
>         c=getchar();
>     }
>     while (c<='9' && c>='0')
>     {
>         x=(x<<3)+(x<<1)+(c-'0');
>         c=getchar();
>     }
>     x*=sgn;
> }
> 
> template <typename T> inline void print(T x)
> {
>     if(x<0) {putchar('-');x=-x;}
>     if (x>9)
>     {
>         print(x/10);
>     }
>     putchar(x%10+'0');
> }
> 
> void readstring(char s[],int len)
> {
>     for(int i=0;i<len;i++)
>     {
>         s[i] = '\0';
>         while (s[i] < 32 || 126 < s[i])
>             s[i] = getchar();
>     }
>     s[len]='\0';
> }
> 
> int main()
> {
>     scanf("%d",&T);
>     int pos=0;
>     while (T--)
>     {
>         while(!isalpha(op[0]=getchar()));
>         while(isalpha(getchar()));
>         
>         int len;
>         if(op[0]=='I')
>         {
>             read(len);
>             readstring(s,len);
>             r.insert(pos,s);
>         }
>         else if(op[0]=='M')
>         {
>             read(pos);
>         }
>         else if(op[0]=='D')
>         {
>             read(len);
>             r.erase(pos,len);
>         }
>         else if(op[0]=='G')
>         {
>             read(len);
>             for(int i=pos;i<pos+len;i++)
>                 putchar(r.at(i));
>             putchar('\n');
>         }
>         else if(op[0]=='P')
>         {
>             pos--;
>         }
>         else if(op[0]=='N')
>         {
>             pos++;
>         }
>     }
>     
> 
>     return 0;
> }
> ```
>
> 


## STL

### bitset

#### 头文件、定义

```c++
#include <bitset>

bitset<1000000> bs(12);
string s="1010"; // or char s[]="1010"
bitset<1000000> bs(s);
```

**模版参数**：
1. bitset的位数大小

**构造方法**：

接受数字、01字符串作为初始化参数。前面全用0补充。

#### 常用方法

##### 位运算操作符、相等与不等判断符

包括`& | ~ ^ << >> == !=`，以及对应位运算操作符的自身操作符。

`<< >>`后接整型数字。

```c++
bitset<4> foo (string("1001"));
bitset<4> bar (string("0011"));

cout << (foo^=bar) << endl;       // 1010 (foo对bar按位异或后赋值给foo)
cout << (foo&=bar) << endl;       // 0010 (按位与后赋值给foo)
cout << (foo|=bar) << endl;       // 0011 (按位或后赋值给foo)

cout << (foo<<=2) << endl;        // 1100 (左移２位，低位补０，有自身赋值)
cout << (foo>>=1) << endl;        // 0110 (右移１位，高位补０，有自身赋值)

cout << (~bar) << endl;           // 1100 (按位取反)
cout << (bar<<1) << endl;         // 0110 (左移，不赋值)
cout << (bar>>1) << endl;         // 0001 (右移，不赋值)

cout << (foo==bar) << endl;       // false (0110==0011为false)
cout << (foo!=bar) << endl;       // true  (0110!=0011为true)

cout << (foo&bar) << endl;        // 0010 (按位与，不赋值)
cout << (foo|bar) << endl;        // 0111 (按位或，不赋值)
cout << (foo^bar) << endl;        // 0101 (按位异或，不赋值)
```

##### 索引运算符

可访问bitset中的位。位是从低到高存的。

```c++
    bitset<4> foo ("1011");
    
    cout << foo[0] << endl;　　//1
    cout << foo[1] << endl;　　//1
    cout << foo[2] << endl;　　//0
```


##### bs.count()

统计bs中1的个数

##### bs.size()

bs的大小。

##### bs.test(int x)

等价于 `bs[x] == 1`

##### bs.any()

bs中有1则为真

##### bs.none()

bs中全0则为真

##### bs.all()

bs中全1则为真

##### bs.flip(int x)

反转bs的第x位（从0计数）

##### bs.set()、bs.set(int x,[int val])

将x处的值设为val。val缺省时设为1。参数全缺省时则全部置为一。

##### bs.reset([int x])

将x处的值设为0。x缺省时全设为0。

##### bs.to_string(), bs.to_ulong(), bs.to_ullong()

返回bs对应的string、unsigned long、unsigned longlong数。


### list

线性双向链表。
https://blog.csdn.net/weixin_39115615/article/details/88768386

#### 头文件、定义

```c++
#include <list>

int ar[] = {75,23,65,42,13};
list<int> l1(ar, ar+5); //使用数组初始化，指定起始与结束指针
list<int> l2(2,100);
```

**模版参数**：
1. list中节点类型

**构造方法：**
可用已存在数组或者指定初始值进行初始化。

#### 常用方法

##### l.begin(), l.end(), l.rbegin(), l.rend()

返回对应的起始节点、结尾节点迭代器。

当l为空时返回0.

##### l.empty()

l为空时为真

##### l.size(), l.max_size()

l的大小，最大可容纳大小

##### l.front(), l.back()

返回l的首尾元素的值的引用

##### l.assign(InputIterator first, InputIterator last)

使用起始、结束迭代器为l赋值。

##### l.push_front(), l.push_back(), l.pop_front(), l.pop_back()

显然

##### l.insert (iterator position, [size_type n] ,const value_type& val), l.insert (iterator position, InputIterator first, InputIterator last); 

在position（l的迭代器）后插入值。指定了n时，指定插入多少个值。

指定的是其他容器的首尾迭代器时，将这部分所有内容插入。

##### l.erase(iterator position), l.erase (iterator first, iterator last)

移除l中对应迭代器处的元素

##### l.swap(list &y)

交换l、y两个list

##### l.clear()

##### l.splice (iterator position, list& x, [iterator first, [iterator last]])

将列表x中的所有元素移到当前list中，从当前列表的position指向的位置之后开始。x中对应内容被移除。

##### l.remove(const value_type& val), l.remove_if(Predicate pred)

从list中删除所有值为val的元素。pred可以是一个函数，也可以是一个class，但它需要有一个参数，且参数类型跟list中存储元素类型相同，满足条件就返回true


##### l.unique([BinaryPredicate binary_pred])

只能删除相邻的重复元素，然后保留第一个值，因此这个函数只对排好序的list有用。

binary_pred可以是函数，也可以是class，但它需要有两个参数，且类型跟list中存储的值类型相同，满足某个条件就返回true


##### l.merge(list &x, [Compare comp])

有序归并：将列表x中的元素按默认的顺序移入当前列表当中，此时列表x为空，当前列表仍为有序列表。注意调用时必须保证l,x的有序性。


##### l.sort([Compare comp])

排序

##### l.reverse()

逆序


### vector

#### 定义与初始化

```c++
vector <int> a(10); //指定大小10，全初始化为0
vector <int> a(10,100) //指定10个值是100的vec
vector <int> a(b.begin(),b.end()) //迭代器初始化
vector <int> a(b) //使用其他

```

#### 常用方法

##### a.front(), a.back()
返回对应引用

##### a.insert(VectorIterator it,value_type val), a.insert(VectorIterator it,int cnt,value_type val)
在it之后插入val，指定cnt时插入cnt个val

##### a.capacity()
返回a在内存中总共可以容纳的元素个数

##### a.resize(uint size,[value_type val])
调整元素尺寸，如果多了则用val补充

##### a.swap(vector b)
将a与b交换：(2021 牛客多校)
```c++
void vec_del(vector<int>&x) {vector<int>t; t.clear();x.swap(t);}

//释放内存
vec_del(f);
```


### string

https://blog.csdn.net/yzl_rex/article/details/7839379

```c++
//string函数用法详解！附代码，写具体的用法！ 
#include <iostream>
#include <string>
#include <sstream> 
using namespace std;
 
 
int main()
{
    //1.string类重载运算符operator>>用于输入，同样重载运算符operator<<用于输出操作
    string str1;
    cin >> str1;//当用cin>>进行字符串的输入的时候，遇到空格的地方就停止字符串的读取输入 
    cout << str1 << endl;
    cin.get();//这个的作用就是读取cin>>输入的结束符，不用对getline的输入产生影响！ 
    getline(cin, str1);//字符串的行输入
    cout << str1 << endl; 
    
    
    //2.string类的构造函数 
    string str2 = "aaaaa";//最简单的字符串初始化 
    cout << str2 << endl; 
    
    char *s = "bbbbb";
    string str3(s);//用c字符串s初始化 
    cout << str3 << endl;
    
    char ch = 'c';
    string str4(5, ch);//用n个字符ch初始化 
    cout << str4 << endl; 
    
    //3.string类的字符操作
    string str5 = "abcde"; 
    ch = str5[3];//operator[]返回当前字符串中第n个字符的位置 
    cout << ch << endl; 
    
    string str6 = "abcde";
    ch = str6.at(4);//at()返回当前字符串中第n个字符的位置,并且提供范围检查，当越界时会抛出异常！  
    cout << ch << endl; 
    
    //4.string的特性描述
    string str7 = "abcdefgh";
    int size;
    size = str7.capacity();//返回当前容量 
    cout << size << endl; 
    size = str7.max_size();//返回string对象中可存放的最大字符串的长度 
    cout << size << endl; 
    size = str7.size();//返回当前字符串的大小 
    cout << size << endl; 
    size = str7.length();//返回当前字符串的长度 
    cout << size << endl; 
    bool flag;
    flag = str7.empty();//判断当前字符串是否为空 
    cout << flag << endl;
    int len = 10; 
    str7.resize(len, ch);//把字符串当前大小置为len，并用字符ch填充不足的部分 
    cout << str7 << endl; 
    
    //5.string的赋值
    string str8;
    str8 = str7;//把字符串str7赋给当前字符串
    cout << str8 << endl;
    str8.assign(str7);//把字符串str7赋给当前字符串 
    cout << str8 << endl; 
    str8.assign(s);//用c类型字符串s赋值 
    cout << str8 << endl; 
    str8.assign(s, 2);//用c类型字符串s开始的n个字符赋值 
    cout << str8 << endl; 
    str8.assign(len, ch);//用len个字符ch赋值给当前字符串 
    cout << str8 << endl; 
    str8.assign(str7, 0, 3);//把字符串str7中从0开始的3个字符赋给当前字符串 
    cout << str8 << endl; 
    string str9 = "0123456789";
    str8.assign(str9.begin(), str9.end());//把迭代器之间的字符赋给字符串 
    cout << str8 << endl; 
    
    //6.string的连接
    string str10;
    str10 += str9;//把字符串str9连接到当前字符串的结尾 
    cout << str10 << endl;
    str10.append(s);//把c类型字符串s连接到当前字符串的结尾 
    cout << str10 << endl; 
    str10.append(s, 2);//把c类型字符串s的前2个字符连接到当前字符串的结尾 
    cout << str10 << endl; 
    str10.append(str9.begin(), str9.end());//把迭代器之间的一段字符连接到当前字符串的结尾 
    cout << str10 << endl; 
    str10.push_back('k');//把一个字符连接到当前字符串的结尾 
    cout << str10 << endl; 
    
    //7.string的比较
    flag = (str9 == str10);//判断两个字符串是否相等 
    cout << flag << endl;
    flag = (str9 != str10);//判断两个字符串是否不相等 
    cout << flag << endl; 
    flag = (str9 > str10);//判断两个字符串是否大于关系 
    cout << flag << endl;
    flag = (str9 < str10);//判断两个字符串是否为小于关系 
    cout << flag << endl;
    flag = (str9 >= str10);//判断两个字符串是否为大于等于关系 
    cout << flag << endl;
    flag = (str9 <= str10);//判断两个字符串否为小于等于关系 
    cout << flag << endl; 
    
    //以下的3个函数同样适用于c类型的字符串，在compare函数中>时返回1，<时返回-1，=时返回0 
    flag = str10.compare(str9);//比较两个字符串的大小，通过ASCII的相减得出！ 
    cout << flag << endl; 
    flag = str10.compare(6, 12, str9);//比较str10字符串从6开始的12个字符组成的字符串与str9的大小 
    cout << flag << endl;
    flag = str10.compare(6, 12, str9, 3, 5);//比较str10字符串从6开始的12个字符组成的字符串与str9字符串从3开始的5个字符组成的字符串的大小 
    cout << flag << endl; 
    
    //8.string的字串
    string str11;
    str11 = str10.substr(10, 15);//返回从下标10开始的15个字符组成的字符串 
    cout << str11 << endl; 
    
    //9.string的交换
    str11.swap(str10);//交换str11与str10的值 
    cout << str11 << endl; 
    
    //10.string的查找，查找成功时返回所在位置，失败时返回string::npos的值，即是-1 
    string str12 = "abcdefghijklmnopqrstuvwxyz";
    int pos;
    pos = str12.find('i', 0);//从位置0开始查找字符i在当前字符串的位置 
    cout << pos << endl;
    pos = str12.find("ghijk", 0);//从位置0开始查找字符串“ghijk”在当前字符串的位置 
    cout << pos << endl; 
    pos = str12.find("opqrstuvw", 0, 4);//从位置0开始查找字符串“opqrstuvw”前4个字符组成的字符串在当前字符串中的位置 
    cout << pos << endl; 
    pos = str12.rfind('s', string::npos);//从字符串str12反向开始查找字符s在字符串中的位置 
    cout << pos << endl; 
    pos = str12.rfind("klmn", string::npos);//从字符串str12反向开始查找字符串“klmn”在字符串中的位置 
    cout << pos << endl;
    pos = str12.rfind("opqrstuvw", string::npos, 3);//从string::pos开始从后向前查找字符串s中前n个字符组成的字符串在当前串中的位置 
    cout << pos << endl; 
    
    string str13 = "aaaabbbbccccdddeeefffggghhhiiijjjkkllmmmandjfaklsdfpopdtwptioczx";
    pos = str13.find_first_of('d', 0);//从位置0开始查找字符d在当前字符串第一次出现的位置 
    cout << pos << endl; 
    pos = str13.find_first_of("eefff", 0);//从位置0开始查找字符串“eeefff“在当前字符串中第一次出现的位置 
    cout << pos << endl; 
    pos = str13.find_first_of("efff", 0, 3);//从位置0开始查找当前串中第一个在字符串”efff“的前3个字符组成的数组里的字符的位置 
    cout << pos << endl;
    pos = str13.find_first_not_of('b', 0);//从当前串中查找第一个不在串s中的字符出现的位置 
    cout << pos << endl; 
    pos = str13.find_first_not_of("abcdefghij", 0);//从当前串中查找第一个不在串s中的字符出现的位置 
    cout << pos << endl; 
    pos = str13.find_first_not_of("abcdefghij", 0, 3);//从当前串中查找第一个不在由字符串”abcdefghij”的前3个字符所组成的字符串中的字符出现的位置 
    cout << pos << endl; 
    //下面的last的格式和first的一致，只是它从后面检索！ 
    pos = str13.find_last_of('b', string::npos);
    cout << pos << endl;
    pos = str13.find_last_of("abcdef", string::npos);
    cout << pos << endl;
    pos = str13.find_last_of("abcdef", string::npos, 2);
    cout << pos << endl; 
    pos = str13.find_last_not_of('a', string::npos);
    cout << pos << endl; 
    pos = str13.find_last_not_of("abcdef", string::npos);
    cout << pos << endl;
    pos = str13.find_last_not_of("abcdef", string::npos, 3);
    cout << pos << endl;
     
    //11.string的替换 
    string str14 = "abcdefghijklmn";
    str14.replace(0, 3, "qqqq");//删除从0开始的3个字符，然后在0处插入字符串“qqqq” 
    cout << str14 << endl; 
    str14.replace(0, 3, "vvvv", 2);//删除从0开始的3个字符，然后在0处插入字符串“vvvv”的前2个字符 
    cout << str14 << endl; 
    str14.replace(0, 3, "opqrstuvw", 2, 4);//删除从0开始的3个字符，然后在0处插入字符串“opqrstuvw”从位置2开始的4个字符 
    cout << str14 << endl; 
    str14.replace(0, 3, 8, 'c');//删除从0开始的3个字符，然后在0处插入8个字符 c 
    cout << str14 << endl; 
    //上面的位置可以换为迭代器的位置，操作是一样的，在这里就不再重复了！ 
    
    //12.string的插入，下面的位置处亦可以用迭代器的指针表示，操作是一样的 
    string str15 = "abcdefg";
    str15.insert(0, "mnop");//在字符串的0位置开始处，插入字符串“mnop” 
    cout << str15 << endl; 
    str15.insert(0, 2, 'm');//在字符串的0位置开始处，插入2个字符m 
    cout << str15 << endl; 
    str15.insert(0, "uvwxy", 3);//在字符串的0位置开始处，插入字符串“uvwxy”中的前3个字符 
    cout << str15 << endl;
    str15.insert(0, "uvwxy", 1, 2);//在字符串的0位置开始处，插入从字符串“uvwxy”的1位置开始的2个字符 
    cout << str15 << endl; 
    
    //13.string的删除
    string str16 = "gfedcba";
    string::iterator it;
    it = str16.begin();
    it++;
    str16.erase(it);//删除it指向的字符，返回删除后迭代器的位置 
    cout << str16 << endl;
    str16.erase(it, it+3);//删除it和it+3之间的所有字符，返回删除后迭代器的位置 
    cout << str16 << endl; 
    str16.erase(2);//删除从字符串位置3以后的所有字符，返回位置3前面的字符 
    cout << str16 << endl; 
    
    //14.字符串的流处理
    string str17("hello,this is a test");
    istringstream is(str17);
    string s1,s2,s3,s4;
    is>>s1>>s2>>s3>>s4;//s1="hello,this",s2="is",s3="a",s4="test"
    ostringstream os;
    os<<s1<<s2<<s3<<s4;
    cout<<os.str() << endl;
    
    system("pause");
}
```


## 有关STL的函数(std， algorithm)

### sort, reverse

### find(Iterator it1,Iterator it2,type val)
返回val出现的第一个指针，如果没找到，返回尾指针

### nth_number(RandomAccessIterator first, RandomAccessIterator nth,RandomAccessIterator last, [Compare comp])

保持nth迭代器对应位置的数就是整个序列的第n个数。其左边的数小于它，右边的数大于它。（注意在这之后nth指向的数就可能变了）

### lower_bound / upper_bound (ForwardIterator first, ForwardIterator last, const T& val, [Compare comp]);

返回第一个大于等于（upperbound对应的是大于）val的指针。如果全不符合，返回last。

```c++
template <class ForwardIterator, class T>
  ForwardIterator lower_bound (ForwardIterator first, ForwardIterator last, const T& val)
{
  ForwardIterator it;
  iterator_traits<ForwardIterator>::difference_type count, step;
  count = distance(first,last);
  while (count>0)
  {
    it = first; step=count/2; advance (it,step);
    if (*it<val) {                 // or: if (comp(*it,val)), for version (2)
      first=++it;
      count-=step+1;
    }
    else count=step;
  }
  return first;
}
// http://www.cplusplus.com/reference/algorithm/lower_bound/
```

### bool next_permutation (ForwardIterator first, ForwardIterator last)

使得first到last之间的序列变为下一个全排列

- 在使用前需要升序排序
- 当不存在下一个排列时（全为降序），返回false


```c++
#include <iostream>
#include <algorithm>
#include <string>
using namespace std;
int main()
{
    string s("afgcbed");
    string::iterator p = find(s.begin(), s.end(), 'c');
    if (p!= s.end())
        cout << p - s.begin() << endl;  //输出 3
    sort(s.begin(), s.end());
    cout << s << endl;  //输出 abcdefg
    next_permutation(s.begin(), s.end());
    cout << s << endl; //输出 abcdegf
    return 0;
}
```