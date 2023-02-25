# 动态规划

## log

```cpp
// exp（n）值为e^n次方
// 以e为底的log()函数
// 以10为底的log10()函数；

cout<<log(9)<<endl;				// 2.19722
cout<<log(exp(9))<<endl;		// 9
cout<<log10(10)<<endl;			// 1

// 自定义以m为底，求log n的值
double a=log(n)/log(m);
```

## lower_bound和upper_bound

`lower_bound( )`和`upper_bound( )`都是利用==二分查找==的方法在一个==排好序的数组==中进行查找的。

在从小到大的排序数组中，

`lower_bound( begin,end,num)`：从数组的`begin`位置到`end-1`位置二分查找第一个==大于等于num==的数字，找到返回该数字的地址，不存在则返回`end`。**通过返回的地址减去起始地址`begin`,得到找到数字在数组中的下标**。

`upper_bound( begin,end,num)`：从数组的`begin`位置到`end-1`位置二分查找第一个==大于num==的数字，找到返回该数字的地址，不存在则返回`end`。**通过返回的地址减去起始地址`begin`,得到找到数字在数组中的下标**。

```cpp
// -7 10 9 2 3 8 8 1

sort(q.begin(), q.end());
for(int i = 0; i < n; i ++)
    printf("%d ", q[i]);
printf("\n");
// -7 1 2 3 8 8 9 10 

p = lower_bound(q.begin(), q.end(), 2) - q.begin();
printf("%d %d\n", p, q[p]);
// 2 2

p = upper_bound(q.begin(), q.end(), 2) - q.begin();
printf("%d %d\n", p, q[p]);
// 3 3
```

在从大到小的排序数组中，重载`lower_bound()`和`upper_bound()`

`lower_bound( begin,end,num,greater<type>() )`:从数组的`begin`位置到`end-1`位置二分查找第一个==小于等于num==的数字，找到返回该数字的地址，不存在则返回`end`。**通过返回的地址减去起始地址`begin`,得到找到数字在数组中的下标**。

`upper_bound( begin,end,num,greater<type>() )`:从数组的`begin`位置到`end-1`位置二分查找第一个==小于num==的数字，找到返回该数字的地址，不存在则返回`end`。**通过返回的地址减去起始地址`begin`,得到找到数字在数组中的下标**。

```cpp
// -7 10 9 2 3 8 8 1

sort(q.begin(), q.end(), [](int a, int b){
    return a > b;
});
for(int i = 0; i < n; i ++)
    printf("%d ", q[i]);
printf("\n");
// 10 9 8 8 3 2 1 -7 

p = lower_bound(q.begin(), q.end(), 2, greater<int>()) - q.begin();
printf("%d %d\n", p, q[p]);
// 5 2

p = upper_bound(q.begin(), q.end(), 2, greater<int>()) - q.begin();
printf("%d %d\n", p, q[p]);
// 6 1
```

## 背包问题

### 01背包

> 有$N$件物品和一个容量是$V$的背包。==每件物品只能使用一次==。第$i$件物品的体积是$v_i$，价值是$w_i$。
>
> 求解将哪些物品装入背包，可使这些物品的总体积不超过背包容量，且总价值最大。输出最大价值。

将$dp$分为两个部分：状态表示和状态计算

* 我们用$f(i,j)$来表示状态，每个状态对应着一个集合及其属性，

  这个集合对应着只从前$i$个物品当中选择并且选出来的物品的总体积$\leq j$的所有选法

  集合的属性代表着集合选法的最大价值，所以$f( i , j )$ 对应着这里面的若干种选法所能达到的最大价值

* 所谓状态计算，就是将集合进行划分，划分成若干个更小的已知集合，从而能够通过这些子集将该集合算出来

  划分原则：不重不漏

  在这里我们将$f(i,j)$划分为不含$i$和含$i$的两个子集合

* 第$ i$ 件物品可以选择也可以不选择

  不含$i$：从$[1 , i - 1]$中间选取总体积不超过$ j$ 的物品，即$f( i - 1 , j )$

  含$i$：从$[1 , i - 1] $中间选取总体积不超过$ j - V(i)$ 的物品，即$f( i - 1 , j - V_i) + W_i$

  所以，$f( i , j ) = max ( f( i - 1 , j ) , f( i - 1 , j - V_i ) + W_i )$

那么，总价值的最大值就是$f(N,V)$

**二维实现**

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N];
int dp[N][N];
// dp[i][j] 表示从前i件物品中挑选出总体积小于等于j的所有情况中的最大总价值
// dp[i][j] 第i件可以选也可以不选，所以dp[i][j] = max(dp[i-1][j],dp[i-1][j-v[i]]+w[i])

int main()
{
    cin >> n >> m;
    for(int i = 1; i <= n; i ++)    cin >> v[i] >> w[i];
    
    // dp[0][0~v] = 0;
    for(int i = 0; i <= m; i ++)
        dp[0][i] = 0;
    
    for(int i = 1; i <= n; i ++)
        for(int j = 0; j <= m; j ++)
        {
            dp[i][j] = dp[i - 1][j];
            if(j >= v[i])   dp[i][j] = max(dp[i][j], dp[i - 1][j - v[i]] + w[i]);
        }
    
    cout << dp[n][m] << endl;
    return 0;
}
```

**一维优化**

二维转化为一维：删掉了第一维（在前$i$个物品中取），$f(j)$ 表示 拿了总体积不超过$ j$ 的物品能得到的最大总价值。

二维时的更新方式：$f[i][j]=max(f[i - 1][j] ,f[i - 1][j - v[i]] + w[i])$

对于每次循环的下一组$i$，只会用到$i-1$来更新当前值，不会用到$i-2$及之前值。于是可以在这次更新的时候，将原来的更新掉，反正以后也用不到。所以对于$i$的更新，只需用一个数组，直接覆盖就行了。

对于每次$j$的更新，只需用到之前$i-1$时的$j$或者$j-v[i]$，不会用到后面的值。
所以为了防止串着改，我们采取从后往前更新的方式，用原来$i-1$的数组来更新$i$。
（如果从前往后更新的话，前面的更新过之后，会接着更新后面的值，这样就不能保证是用原来$i-1$的数组来更新$i$的了）

如何转化为一维呢？
只用一个数组，每次都覆盖前面的数组。

如果当前位置的东西不拿的话，和前一位置的信息（原来$i-1$数组的这个位置上的值）是相同的，所以不用改变。

如果当前位置的东西拿了的话，需要和前一位置的信息（原来$i-1$数组的这个位置上值）取$max$。

所以，更新方式就为：$f[j]=max(f[j],f[j-v[i]]+w[i])$

整个更新方式就相当于：

每次$i++$，就从后往前覆盖一遍$f$数组，看每个位置上的值是否更新。

如果使用顺序，会先更新f[4],再更新f[7]，对于这个书包问题来讲，就是有可能，在更新f[4]的时候，已经把这次能加的物品加进来了，然后更新f[7]的时候，还有可能再加一次，所以必须使用逆序，保证，f[4]是没有加入新物品前，背包里的最优解。

另外，由于$f[j]$要么等于$f[j]$，要么等于$f[j-v[i]]$，所以只需要对$j$从$m$循环到$v[i]$即可

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N], f[N];

int main()
{
    scanf("%d%d", &n, &m);
    for(int i = 1; i <= n; i ++)    scanf("%d%d", &v[i], &w[i]);
    for(int i = 1; i <= n; i ++)
        for(int j = m; j >= v[i]; j --)
            f[j] = max(f[j], f[j - v[i]] + w[i]);
    printf("%d\n", f[m]);
    return 0;
}
```

### 完全背包

> 有N件物品和一个容量是V的背包。==每件物品能用无限次==。第i件物品的体积是v<sub>i</sub>，价值是w<sub>i</sub>。
>
> 求解将哪些物品装入背包，可使这些物品的总体积不超过背包容量，且总价值最大。输出最大价值。

将动态规划分为两部分：状态表示和状态计算

* 状态表示为$f(i,j)$ ，$f(i,j)$表示的是所有只考虑前$i$个物品并且总体积不大于$j$的所有选法的集合

  $f(i,j)$的属性就是集合里面选法的总价值的最大值

* 状态计算对应着集合的划分，该集合可以分为若干组，表示这第$i$个物品选多少个，从$0$个到$k$个

  第$i$个物品选$0$件，$f(i,j)=f(i-1,j)$

  第$i$个物品选$k$件，去掉$k$个物品$i$，求$f(i-1,j-k*v[i])$，再把$k$个物品$i$加回来，$f(i,j)=f(i-1,j-k*v[i])+k*w[i]$ 

**三维实现**

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N], f[N][N];

int main()
{
    scanf("%d%d", &n, &m);
    for(int i = 1; i <= n; i ++)    scanf("%d%d", &v[i], &w[i]);
    for(int i = 1; i <= n; i ++)
        for(int j = 0; j <= m; j ++)
            for(int k = 0; v[i] * k <= j; k ++)
                f[i][j] = max(f[i][j], f[i - 1][j - v[i] * k] + k * w[i]);
    printf("%d\n", f[n][m]);
    return 0;
}
```

**二维优化**

$f(i,j)=f(i-1,j-k*v[i])+k*w[i]$

$f(i,j)=max(f(i-1,j),f(i-1,j-v)+w,f(i-1,j-2*v)+2*w,...,f(i-1,j-k*v)+k*w)$ 

$f(i,j-v)=max(f(i-1,j-v),f(i-1,j-2*v)+w),f(i-1,j-3*v)+2*w,...$ 

$f(i,j)=max(f(i-1,j),f(i,j-v)+w)$ 

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N], f[N][N];

int main()
{
    scanf("%d%d", &n, &m);
    for(int i = 1; i <= n; i ++)    scanf("%d%d", &v[i], &w[i]);
    for(int i = 1; i <= n; i ++)
        for(int j = 0; j <= m; j ++)
        {
            f[i][j] = f[i - 1][j];
            if(j >= v[i])    f[i][j] = max(f[i][j], f[i][j - v[i]] + w[i]);
        }
    printf("%d\n", f[n][m]);
    return 0;
}
```

**一维优化**

$f(i,j)=max(f(i-1,j),f(i,j-v)+w)$ 

由于这里的转移是到$f(i,j-v)$，需要前面被计算过，因此不需要倒序计算

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N], f[N];

int main()
{
    scanf("%d%d", &n, &m);
    for(int i = 1; i <= n; i ++)    scanf("%d%d", &v[i], &w[i]);
    for(int i = 1; i <= n; i ++)
        for(int j = v[i]; j <= m; j ++)
            f[j] = max(f[j], f[j - v[i]] + w[i]);
    printf("%d\n", f[m]);
    return 0;
}
```

### 多重背包

> 有N件物品和一个容量是V的背包。==每个物品最多有S<sub>i</sub>个==。第i件物品的体积是v<sub>i</sub>，价值是w<sub>i</sub>。
>
> 求解将哪些物品装入背包，可使这些物品的总体积不超过背包容量，且总价值最大。输出最大价值。

动态规划分为状态表示和状态计算

* 状态表示为$f(i,j)$，$f(i,j)$表示的是所有只考虑前$i$个物品并且总体积不大于$j$的所有选法的集合

  $f(i,j)$的属性就是集合里面选法的总价值的最大值

* 状态计算： 将$f(i,j)$集合划分为第$i$个物品选$0$个到$S_i$个

  $f(i,j)=max(f(i-1,j-k*v)+k*w) \quad k \in [0,S_i]$ （同完全背包）

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;

int n, m;
int v[N], w[N], s[N], f[N][N];

int main()
{
    scanf("%d%d", &n, &m);
    for(int i = 1; i <= n; i ++)    scanf("%d%d%d", &v[i], &w[i], &s[i]);
    for(int i = 1; i <= n; i ++)
        for(int j = 0; j <= m; j ++)
            for(int k = 0; k <= s[i] && v[i] * k <= j; k ++)
                f[i][j] = max(f[i][j], f[i - 1][j - v[i] * k] + w[i] * k);
    printf("%d\n", f[n][m]);
    return 0;
}
```

#### 二进制优化

某物品最多有s件，我们需要从所有的物品中选择若干件，使这个背包的价值最大。

题目并没有说某物品一定需要选多少件出来，也没有说一共要选多少件出来。只是选择若干件，至于选几件，无所谓，但要保证价值最大。

按照优化的策略某物品有s件，我们给其打包分成了好几个大的物品。第一个大物品是包含原来该物品的1件，第二个大物品是包含原来该物品的2件，第三个大物品是包含原来该物品的4件，第四个大物品是包含原来该物品的8件,.....依次类推。

此时我们就把所有的物品都重新进行了一个分类。原先每个物品最多s件，我们就把这个件数条件给消去了。取而代之的是，按照一定原先件数组合出来的新若干大物品。

我们又已知按照我们划分成大物品进行搭配组合，一定能转化为原先的若干件小物品出来。

并且选择某物品的最多件数，是不会超过原先该物品的s件。

所以就转化为从下面这些若干件大物品中，选择能使背包容积最大大的情况下，价值最高。这个就是一个01问题。

物品$i$原来有$S_i$ 件，可以将$S_i$拆分为$1,2,4,8,16,32,64,...,2^k$ ，这些数的和为$2^{k}-1$，那么再增加一件$S_i-(2^k-1)=S_i-2^k+1$

那么，通过$1,2,4,8,...,2^k,S_i-2^k+1$这些数可以组合出$[1,S_i]$内的任意一个数，那么就是$log S_i$个数，转化为01背包问题

```cpp
// 1000 * ceil(log(2000) / log(2)) = 11000
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 11010, M = 2010;

int n, m, a, b, c, cnt = 0;
int v[N], w[N], f[M];

int main()
{
    scanf("%d%d", &n, &m);
    for(int i = 1; i <= n; i ++)
    {
        int k = 1;
        scanf("%d%d%d", &a, &b, &c);
        while(k <= c)
        {
            cnt ++;
            v[cnt] = a * k;
            w[cnt] = b * k;
            c -= k;
            k <<= 1;
        }
        if(c)
        {
            cnt ++;
            v[cnt] = a * c;
            w[cnt] = b * c;
        }
    }
    n = cnt;
    for(int i = 1; i <= n; i ++)
        for(int j = m; j >= v[i]; j --)
            f[j] = max(f[j], f[j - v[i]] + w[i]);
    printf("%d\n", f[m]);
    return 0;
}
```

### 分组背包

> 有N件物品和一个容量是V的背包。==每组物品有若干个，同一组内的物品最多只能选一个==。第i件物品的体积是$V_{ij}$，价值是$W_{}ij$，（i为组号，j为组内编号）。
>
> 求解将哪些物品装入背包，可使这些物品的总体积不超过背包容量，且总价值最大。输出最大价值。

动态规划分为状态表示和状态计算

* 状态表示为$f(i,j)$，$f(i,j)$表示的是所有只考虑前$i$组物品并且总体积不大于$j$的所有选法的集合

  $f(i,j)$的属性就是集合里面选法的总价值的最大值

* 状态计算： 枚举第$i$组物品选哪个

  第$i$组物品不选$f(i,j)=f(i-1,j)$

  第$i$组物品选第$k$个$f(i,j)=f(i-1,j-v[i][j])+w[i][k]$ 

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 110;

int n, m;
int s[N], v[N][N], w[N][N], f[N][N];

int main()
{
    scanf("%d%d", &n, &m);
    for(int i = 1; i <= n; i ++)
    {
        scanf("%d", &s[i]);
        for(int j = 1; j <= s[i]; j ++)
            scanf("%d%d", &v[i][j], &w[i][j]);
    }
    for(int i = 1; i <= n; i ++)
        for(int j = 0; j <= m; j ++)
        {
            f[i][j] = f[i - 1][j];
            for(int k = 1; k <= s[i]; k ++)
                if(j >= v[i][k])    f[i][j] = max(f[i][j], f[i - 1][j - v[i][k]] + w[i][k]);
        }
    printf("%d\n", f[n][m]);
    return 0;
}
```

一维滚动数组逆序优化

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 110;

int n, m;
int s[N], v[N][N], w[N][N], f[N];

int main()
{
    scanf("%d%d", &n, &m);
    for(int i = 1; i <= n; i ++)
    {
        scanf("%d", &s[i]);
        for(int j = 1; j <= s[i]; j ++)
            scanf("%d%d", &v[i][j], &w[i][j]);
    }
    for(int i = 1; i <= n; i ++)
        for(int j = m; j >= 0; j --)		// 涉及到k，因此只能判断
            for(int k = 1; k <= s[i]; k ++)
                if(j >= v[i][k])    f[j] = max(f[j], f[j - v[i][k]] + w[i][k]);
    printf("%d\n", f[m]);
    return 0;
}
```

## 线性DP

### 数字三角形

> 给定一个数字三角形，从顶部出发，在每一结点可以选择移动至其左下方的结点或移动至其右下方的结点，一直走到底层，要求找出一条路径，使路径上的数字的和最大。

* 状态表示 $f(i,j)$ 表示所有从起点走到$(i,j)$的路径的集合，$f(i,j)$ 的属性为所有路径上的数字之和的最大值

* 状态计算 $f(i,j)$ 可以分为从左上来的一类和从右上来的一类

  $f(i,j) = max(f(i-1,j-1),f(i-1,j))+v(i,j)$ 

复杂度：状态数量*转移计算量

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 510;

int n, a[N][N], f[N][N], res = -0x3f3f3f3f;

int main()
{
    scanf("%d", &n);
    for(int i = 1; i <= n; i ++)
        for(int j = 1; j <= i; j ++)
            scanf("%d", &a[i][j]);
            
    for(int i = 0; i <= n; i ++)
        for(int j = 0; j <= n; j ++)
            f[i][j] = -0x3f3f3f3f;
            
    f[1][1] = a[1][1];
    for(int i = 2; i <= n; i ++)
        for(int j = 1; j <= i; j ++)
            f[i][j] = max(f[i - 1][j - 1], f[i - 1][j]) + a[i][j];
    for(int i = 1; i <= n; i ++)    res = max(res, f[n][i]);
    printf("%d\n", res);
    return 0;
}
```

当然，我们也可以自底向上分析

那么$f(i,j)$集合就可以划分为左下和右下两个组成

$f(i,j)=max(f(i+1,j),f(i+1,j+1))+a(i,j)$

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 510;

int n, a[N][N], f[N][N];

int main()
{
    scanf("%d", &n);
    for(int i = 1; i <= n; i ++)
        for(int j = 1; j <= i; j ++)
            scanf("%d", &a[i][j]);
    for(int i = 1; i <= n; i ++)    f[n][i] = a[n][i];
    for(int i = n - 1; i >= 1; i --)
        for(int j = 1; j <= i; j ++)
            f[i][j] = max(f[i + 1][j], f[i + 1][j + 1]) + a[i][j];
    printf("%d\n", f[1][1]);
    return 0;
}
```

### 最长上升子序列

> 给定一个长度为 $N$ 的数列，求数值严格单调递增的子序列的长度最长是多少。

* 状态表示

  $f(i)$表示所有以第$i$个数结尾的上升子序列的集合

  $f(i)$存储的属性为集合里面每一个上升子序列的长度的最大值

* 状态计算

  $f(i)$的集合可以划分为若干子集，以第$i-1$个数来分类，将倒数第2个数划定为$a_0$(仅有自身)到$a_{i-1}$

  同时为了满足上升子序列，还要保证第$i-1$个数小于第$i$个数

  如果不存在就不取这种情况

  $f(j)=max\{f[j]+1\}\quad a_j<a_i \quad \& \quad j \in[0,i-1]$

$O(n^2)$

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;

int n, res = -0x3f3f3f3f;
int a[N], f[N];

int main()
{
    scanf("%d", &n);
    for(int i = 1; i <= n; i ++)    scanf("%d", &a[i]);
    for(int i = 1; i <= n; i ++)
    {
        f[i] = 1;
        for(int j = 1; j < i; j ++)
            if(a[j] < a[i]) f[i] = max(f[i], f[j] + 1);
    }
    for(int i = 1; i <= n; i ++)    res = max(res, f[i]);
    printf("%d\n", res);
    return 0;
}
```

### 最长上升子序列优化

$O(nlogn)$

首先数组$a$存输入的数，开辟一个数组$q$用来存结果，最终数组$q$的长度就是最终的答案

假如数组$q$现在存了数，当到了数组$a$的第$i$个位置时，首先判断是否$a[i] > q[cnt] $ 若是大于则直接将这个数添加到数组f中，即$f[++cnt] = a[i]$，这个操作时显然的。
当$a[i] <= f[cnt]$ 的时,我们就用$a[i]$去替代数组$q$中的第一个大于等于$a[i]$的数，因为在整个过程中我们维护的数组$q$ 是一个递增的数组，所以我们可以用二分查找在 $logn$ 的时间复杂的的情况下直接找到对应的位置，然后替换，即$q[l] = a[i]$。

> 数组$q$的解释：每个长度的序列都有多个，当序列结尾的数越小，后面能跟的数就越多，就会优先选择
>
> 数组$q[i]$表示长度为$i$的序列的末尾元素，我们不难推测该数组是单调递增的
>
> 假设$q[i] > q[i+1]$，那么长度为$i+1$的序列的倒数第二个元素$p$必然小于$q[i+1]$，也就小于$q[i]$
>
> 由于$q[i]$为相同长度的序列中末尾元素最小的一条，就有$q[i]<p$，矛盾，因此$q[i]\leq q[i+1]$

我们用$a[i]$去替代$q[i]$的含义是：以$a[i]$为最后一个数的严格单调递增序列,这个序列中数的个数为$l$个。

这样当我们遍历完整个数组$a$后就可以得到最终的结果。

`3 1 2 1 8 5 6`

`q 3`    1 < 3 

`q 1`    2 > 1

`q 1 2`    1 < 2    1 = 1

`q 1 2`    8 > 2

`q 1 2 8`    5 < 8    5 > 2

`q 1 2 5`    6 > 5

`q 1 2 5 6`    

**注意**：如果题目要求是输出最长子序列，那么这个解法就有问题了，所以适用性不是很强

`1 2 6 8 5`
本做法得到的最长子序列应该是 `1 2 5 8`
但实际答案确实`1 2 6 8`
但是这道题求的是最大元素个数，所以个数没有问题。

题解中最难理解的地方在于栈中序列虽然递增，但是每个元素在原串中对应的位置其实可能是乱的，那为什么这个栈还能用于计算最长子序列长度？
实际上这个栈【不用于记录最终的最长子序列】，而是【以$stk[i]$结尾的子串长度最长为$i$】或者说【长度为$i$的递增子串中，末尾元素最小的是$stk[i]$】。理解了这个问题以后就知道为什么新进来的元素要不就在末尾增加，要不就替代第一个大于等于它元素的位置。
这里的【替换】就蕴含了一个贪心的思想，对于同样长度的子串，我当然希望它的末端越小越好，这样以后我也有更多机会拓展。

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int  N = 1e5 +10;

int n, len = 1;
int a[N], q[N];

int main()
{
    scanf("%d", &n);
    for(int i = 1; i <= n; i ++)    scanf("%d", &a[i]);
    
    q[1] = a[1];
    for(int i = 2; i <= n; i ++)
    {
        if(a[i] > q[len])   q[++ len] = a[i];
        else
        {
            int l = 1, r = len;
            while(l < r)
            {
                int mid = l + r >> 1;
                if(q[mid] >= a[i])   r = mid;
                else    l = mid + 1;
            }
            q[l] = a[i];
        }
    }
    cout << len << endl;
    return 0;
}
```

输出最长上升子序列

```cpp
#include <bits/stdc++.h>
using namespace std;

/*
测试用例：
8
-7 10  9  2  3  8  8  1
*/

const int N = 1e5 + 10;
int n, a[N];
int f[N], idx; // dp数组，idx:下标指针,因为从1开始，也可以理解为个数、LIS的最大长度
int pos[N];    // p数组，记录原始数组每个数字在dp数组中出现的位置,也就是这个数字a[i]它当过第几名

//输出一条LIS路径,判断需要配合Special Judge
void print() {
    vector<int> path;
    for (int i = n; i >= 1 && idx; i--) //找够idx个就结束了
        if (pos[i] == idx) {            //找到idx,idx-1,idx-2,...的名次
            path.push_back(a[i]);       //记录idx,idx-1,idx-2,...名次的数字入路径数组
            idx--;
        }
    for (int i = path.size() - 1; i >= 0; i--) printf("%d ", path[i]);
}

int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) scanf("%d", &a[i]);

    // 1、出发
    f[++idx] = a[1];
    pos[1] = 1; //每个数字都会产生一个在f数组中的位置记录

    // 2、后续元素进行处理
    for (int i = 2; i <= n; i++)
        if (a[i] > f[idx]) {
            f[++idx] = a[i];
            pos[i] = idx; //每个数字都会产生一个在f数组中的位置记录
        } else {
            int t = lower_bound(f + 1, f + idx + 1, a[i]) - f;
            f[t] = a[i];
            pos[i] = t; //每个数字都会产生一个在f数组中的位置记录
        }
    //输出lis长度
    printf("%d\n", idx);

    //输出LIS路径方法
    print();

    return 0;
}
```

### 最长公共子序列

> 给定两个长度分别为 $N$ 和 $M$ 的字符串 $A$ 和 $B$，求既是 $A$ 的子序列又是 $B$的子序列的字符串长度最长是多少。

**法一**

* 状态表示$f(i,j)$

  $f(i,j)$表示所有在第一个序列的前$i$个字母中出现，且在第二个序列的前$j$个字母中出现的子序列

  属性为这些公共子序列长度的最大值

* 状态计算

  $f(i,j)$集合的划分：以$a[i]$和$b[j]$==是否在子序列当中==作为划分的依据，那么一共有4种情况，划分为4个子集

  $(0,0)$表示不选$a[i]$也不选$b[j]$，$(0,1)$表示不选$a[i]$选$b[j]$，$(1,0)$表示选$a[i]$不选$b[j]$，$(1，1)$表示同时选$a[i]$和$b[j]$

  $(0,0)$即为$f(i-1,j-1)$       $(1,1)$即为$f(i-1,j-1)+1$（这种情况只在$a[i]==b[j]$时出现）

  
  
  $(0,1)$表示在公共子序列中$a[i]$不出现但是$b[j]$出现，$f(i-1,j)$表示在第一个序列的前$i-1$个字母中出现，且在第二个序列的前$i$个字母中出现的子序列，此时$b[j]$不一定出现，可以知道$f(i-1,j)$包含了$(0,1)$这种情况 ，但是由于求的是max，所以不影响
  
  > 例如，要求$a,b,c$的最大值可以这样求，$max(max(a,b),max(b,c))$，虽然b被重复计算，但是仍然可以求出max，求max属性只要保证不漏即可，但是求数量属性就要保证不重不漏。
  
  $(1,0)$ 也同理，$f(i,j-1)$包含了$(0,1)$这种情况
  
  那么事实上，$(0,0)$这种情况也包含在$(0,1)$和$(1,0)$的情况里面

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;

int n, m;
string a, b;
int f[N][N];

int main()
{
    cin >> n >> m;
    cin >> a >> b;
    a = " " + a, b = " " + b;
    for(int i = 1; i <= n; i ++)
    {
        for(int j = 1; j <= m; j ++)
        {
            f[i][j] = max(f[i - 1][j], f[i][j - 1]);
            if(a[i] == b[j])    f[i][j] = max(f[i][j], f[i - 1][j - 1] + 1);
        }
    }
    cout << f[n][m] << endl;
    return 0;
}
```

**法二**

* 状态表示$f(i,j)$

  $f(i,j)$表示所有在第一个序列的前$i$个字母中出现，且在第二个序列的前$j$个字母中出现的子序列

  属性为这些公共子序列长度的最大值

* 状态计算

  按两个序列末尾的字符是不是相等来区分

  ![问题分析.PNG](https://cdn.acwing.com/media/article/image/2020/02/06/28466_6610da5048-%E9%97%AE%E9%A2%98%E5%88%86%E6%9E%90.PNG)

如果两个字符相等，就可以直接转移到`f[i-1][j-1]`，不相等的话，两个字符一定有一个可以抛弃，可以对`f[i-1][j]`,`f[i][j-1]`两种状态取`max`来转移。

![状态转移.PNG](https://cdn.acwing.com/media/article/image/2020/02/06/28466_e2c0e13048-%E7%8A%B6%E6%80%81%E8%BD%AC%E7%A7%BB.PNG)

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;

int n, m;
string a, b;
int f[N][N];

int main()
{
    cin >> n >> m;
    cin >> a >> b;
    a = " " + a, b = " " + b;
    for(int i = 1; i <= n; i ++)
        for(int j = 1; j <= m; j ++)
        {
            if(a[i] == b[j])    f[i][j] = f[i - 1][j - 1] + 1;
            else    f[i][j] = max(f[i - 1][j], f[i][j - 1]);
        }
    printf("%d\n", f[n][m]);
    return 0;
}
```

### 最短编辑距离

> 给定两个字符串 $A$ 和 $B$，现在要将 $A$ 经过若干操作变为 $B$，可进行的操作有：
>
> 1. 删除——将字符串 $A$ 中的某个字符删除。
> 2. 插入——在字符串 $A$ 的某个位置插入某个字符。
> 3. 替换——将字符串 $A$ 中的某个字符替换为另一个字符。
>
> 现在请你求出，将 $A$ 变为 $B$ 至少需要进行多少次操作。

* 状态表示
  * 集合：所有将$A[1,i]$变成$B[1,j]$的操作方式
  * 属性：所有操作方式的操作次数的最小值
* 状态计算：要将$A[1,i]$变为$B[1,j]$有三种方式
  * 将$A[i]$删除后能够和$B$匹配，那么$A[1,i-1]$和$B[1,j]$是匹配的，即$f(i,j)=f(i-1,j)+1$
  * 在$A[i]$后面插入新的字符能够和$B$匹配，那么$A[1,i]$和$B[1,j-1]$是匹配的，即$f(i,j)=f(i,j-1)+1$
  * 将$A[i]$改为$B[j]$后可以匹配，那么$A[1,i-1]$和$B[1,j-1]$是匹配的，即$f(i,j)=f(i-1,j-1)+1$，但是如果这一位上本来就相等就不用修改，即$f(i,j)=f(i-1,j-1)$

需要先初始化，当$A$的长度为0时，变为$B$需要$B$的长度步数操作，以及当$B$的长度为0时，变为$A$需要$A$的长度步数操作

证明参考[编辑距离算法的正确性证明，请问有没有相关的资料呢？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/425049057/answer/1583825407)

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1010;

int n, m;
string a, b;
int f[N][N];

int main()
{
    cin >> n >> a >> m >> b;
    a = " " + a, b = " " + b;
    
    for(int i = 1; i <= n; i ++)    f[i][0] = i;
    for(int i = 1; i <= m; i ++)    f[0][i] = i;
    
    for(int i = 1; i <= n; i ++)
        for(int j = 1; j <= m; j ++)
        {
            f[i][j] = min(f[i - 1][j] + 1, f[i][j - 1] + 1);
            if(a[i] == b[j])    f[i][j] = min(f[i][j], f[i - 1][j - 1]);
            else    f[i][j] = min(f[i][j], f[i - 1][j - 1] + 1);
        }
    cout << f[n][m] << endl;
    return 0;
}
```

### 编辑距离

> 给定 $n$ 个长度不超过 $10$ 的字符串以及 $m$ 次询问，每次询问给出一个字符串和一个操作次数上限。
>
> 对于每次询问，请你求出给定的 $n$ 个字符串中有多少个字符串可以在上限操作次数内经过操作变成询问给出的字符串。
>
> 每个对字符串进行的单个字符的插入、删除或替换算作一次操作。

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 15, M = 1010;

int n, m;
string a[M];
int f[N][N];

int change(string a, string b)
{
    memset(f, 0, sizeof(f));
    int la = a.length(), lb = b.length();
    a = " " + a, b = " " + b;
    for(int i = 0; i <= lb; i ++)    f[0][i] = i;
    for(int i = 0; i <= la; i ++)    f[i][0] = i;
    
    for(int i = 1; i <= la; i ++)
    {
        for(int j = 1; j <= lb; j ++)
        {
            f[i][j] = min(f[i - 1][j] + 1, f[i][j - 1] + 1);
            if(a[i] == b[j])    f[i][j] = min(f[i][j], f[i - 1][j - 1]);
            else    f[i][j] = min(f[i][j], f[i - 1][j - 1] + 1);
        }
    }
    return f[la][lb];
}

int main()
{
    cin >> n >> m;
    for(int i = 0; i < n; i ++)     cin >> a[i];
    
    while(m --)
    {
        string b;
        int cnt = 0, num;
        cin >> b >> num;
        for(int i = 0; i < n; i ++)
        {
            if(change(a[i], b) <= num)  cnt ++;
        }
        cout << cnt << endl;
    }
    return 0;
}
```

## 区间DP

### 石子合并

> 设有 $N$ 堆石子排成一排，其编号为 $1，2，3，…，N$。每堆石子有一定的质量，现在要将这 $N$ 堆石子合并成为一堆。每次只能合并相邻的两堆，合并的代价为这两堆石子的质量之和，合并后与这两堆石子相邻的石子将和新堆相邻，合并时由于选择的顺序不同，合并的总代价也不相同。找出一种合理的方法，使总的代价最小，输出最小代价。

状态表示为某一区间

* 状态表示

  $f[i,j]$ 表示第$i$到第$j$堆石子的区间

  * 集合：所有将第$i$到第$j$堆石子合并成一堆石子的合并方式
  * 属性：所有合并方式中代价的最小值

* 状态计算

  ==最后一次合并一定是左边连续的一部分和右边连续的一部分进行合并==

  最后一步一定是将两堆合并成一堆，因此可以以最后一次合并的分界线位置作为分类标准

  将$[i,j]$分为$[i,k]$和$[k+1,j]$两个区间，找到$k$使得总价值最小

  将$[i,k]$和$[k+1,j]$两个区间合并为$[i,j]$的代价是唯一确定的，因此只需要找到$[i,k]+[k+1,j]$的最小价值即可
  
  $f[i,j]=min(f[i,k]+f[k+1,j])+s[j]-s[i-1]\quad k\in[i,k-1]$

为了确保在合并后面的时候前面的小区间已经合并好，所以可以按照==区间长度==从小到大进行枚举，然后对于==区间左端点==进行枚举

**区间 DP 常用模版**

所有的区间dp问题枚举时，第一维通常是枚举区间长度，并且一般 `len = 1` 时用来初始化，枚举从 `len = 2` 开始；第二维枚举起点 `i` （右端点 `j` 自动获得，`j = i + len - 1`）

```cpp
for (int len = 1; len <= n; len++) 				// 区间长度
{         	
    for (int i = 1; i + len - 1 <= n; i++) 		// 枚举起点
    { 	
        int j = i + len - 1;                 	// 区间终点
        if (len == 1) 
        {
            dp[i][j] = 初始值
            continue;
        }

        for (int k = i; k < j; k++) 			// 枚举分割点，构造状态转移方程
        {        
            dp[i][j] = min(dp[i][j], dp[i][k] + dp[k + 1][j] + w[i][j]);
        }
    }
}
```

$O(n^3)$

```cpp
#include <algorithm>
#include <iostream>
#include <cstring>

using namespace std;

const int N = 310;

int n, a[N], s[N], f[N][N];

int main()
{
    scanf("%d", &n);
    for(int i = 1; i <= n; i ++)    scanf("%d", &a[i]);
    for(int i = 1; i <= n; i ++)    s[i] = s[i - 1] + a[i];
    for(int len = 2; len <= n; len ++)
        for(int i = 1; i + len - 1 <= n; i ++)
        {
            int l = i, r = i + len - 1;
            f[l][r] = 0x3f3f3f3f;
            for(int k = l; k < r; k ++)
                f[l][r] = min(f[l][r], f[l][k] + f[k + 1][r] + s[r] - s[l - 1]);
        }
    printf("%d\n", f[1][n]);
    return 0;
}
```

## 计数类DP

### 整数划分

> 一个正整数 $n$ 可以表示成若干个正整数之和，形如：$n=n_1+n_2+…+n_k$，其中 $n_1≥n_2≥…≥n_k,k≥1$。
>
> 我们将这样的一种表示称为正整数 $n$ 的一种划分。
>
> 现在给定一个正整数 $n$，请你求出 $n$ 共有多少种不同的划分方法。

因为有$n_{k-1}>n_k$，所以序列有序

**算法1**：看成背包问题，背包体积为$n$，有$n$个物品，体积分别为$1,2,...,n$，每件物品有无限个（完全背包），求恰好能够将背包装满的方案数

* 状态表示$f(i,j)$

  * 集合：从前$i$个物品中选，且体积恰好是$j$的选法
  * 属性：选法数量

* 状态计算：第$i$个物品选择$0,1,...,s$个，

  选0个状态的数量就是$f(i-1,j)$，选1个状态的数量就是$f(i-1,j-w_i)$，选了$k$个的数量就是$f(i-1,j-k*w_i)$ 

优化：

$f(i,j)=f(i-1,j)+f(i-1,j-w_i)+f(i-1,j-2*w_i)+...+f(i-1,j-s*w_i)$

$f(i,j-w_i)=f(i-1,j-w_i)+f(i-1,j-2*w_i)+...+f(i-1,j-s*w_i)$

$f(i,j)=f(i-1,j)+f(i,j-w_i)$

$f(j)=f(j)+f(j-w_i)$			因为等号右边存在$f(i)$，因此从小到大循环

初始化：求最大值时，当都不选时，价值显然是 0。而求方案数时，当都不选时，方案数是 1（即前 i 个物品都不选的情况也是一种方案），所以需要初始化为 1。即：`for (int i = 0; i <= n; i ++) f[i][0] = 1;`
等价变形后： `f[0] = 1`

```cpp
#include <iostream>

using namespace std;

const int N = 1010, mod = 1e9 + 7;

int n;
int f[N];

int main()
{
    cin >> n;
    f[0] = 1;
    for(int i = 1; i <= n; i ++)
        for(int j = i; j <= n; j ++)
            f[j] = (f[j] + f[j - i]) % mod; 
    cout << f[n] << endl;
    return 0;
}
```

**算法2**：

* 状态表示$f(i,j)$

  * 集合：所有总和为$i$，并且恰好表示成$j$个数的方案
  * 属性：数量

* 状态计算：

  方案可以分为两大类：方案中最小值是$1$和最小值大于$1$

  * 最小值是$1$：去掉最后的一个$1$，就可以变成$f(i-1,j-1)$的方案
  * 最小值大于$1$：将每个数都减去$1$，就可以变成$j$个数组成$i-j$的方案，即$f(i-j,j)$

  最后答案等于$f(n,1)+...+f(n,n)$

```cpp
#include <iostream>

using namespace std;

const int N = 1010, mod = 1e9 + 7;

int n, f[N][N], res;

int main()
{
    scanf("%d", &n);
    f[0][0] = 1;
    for(int i = 1; i <= n; i ++)
        for(int j = 1; j <= i; j ++)
            f[i][j] = (f[i - 1][j - 1] + f[i - j][j]) % mod;
    for(int i = 1; i <= n; i ++)    res = (res + f[n][i]) % mod;
    printf("%d\n", res);
    return 0;
}
```

## 数位统计DP

### 计数问题

> 给定两个整数 $a$ 和 $b$，求 $a$ 和 $b$ 之间的所有数字中 $0∼9$ 的出现次数。

分情况讨论

要求$[a,b]$中$0∼9$的出现次数

要实现$count(n,x)$函数代表$1 ∼ n$中$x$的出现次数，然后通过类似前缀和的思想来求$count(b,x)-count(a-1,x)$

* $1∼n$中$x$出现的次数$(x>1)$，假设当前数字为$abcdefg$，分别求出$x$在每一位上出现的次数，

  e.g. 求$x$在第4位上出现的次数，就是寻找有多少数满足：$1\leq mmm1nnn \le abcdefg$

  * $mmm$取到$000∼abc-1$，$nnn$取到$000∼999$，$x$可能的个数为$abc*1000$
  * $mmm=abc$
    * $d<x$，$abcxnnn>abcdefg$，个数为0
    * $d = x$，$nnn$只能取$000∼efg$， 个数为$efg+1$
    * $d>x$，那么$abcdefg>abc1nnn$，$nnn$可以取$000∼999$，个数为1000


* $x=0$时
  * $mmm$取到$001∼abc-1$，$nnn$取到$000∼999$，$x$可能的个数为$(abc-1)*1000$
  * $mmm=abc$
    - d<x，abcxnnn>abcdefg，个数为0
    - d=x，nnn只能取000∼efg， 个数为efg+1
    - d>x，那么abcdefg>abc1nnn，nnn可以取000∼999，个数为1000

```cpp
#include <iostream>
#include <algorithm>
#include <cmath>

using namespace std;

int digit(int n)
{
    int res = 0;
    while(n)    res ++, n /= 10;
    return res;
}

int count(int n, int i)
{
    int res = 0, d = digit(n);
    for (int j = 1; j <= d; ++ j)
    {
        int p = pow(10, j - 1), l = n / p / 10, r = n % p, dj = n / p % 10;
        if (i) res += l * p; 
        if (!i && l) res += (l - 1) * p;
        if ( (dj > i) && (i || l) ) res += p;
        if ( (dj == i) && (i || l) ) res += r + 1;
    }
    return res;
}

int main()
{
    int a, b;
    while (cin >> a >> b)
    {
        if(a == 0 && b == 0)    break;
        if (a > b) swap(a, b);
        for (int i = 0; i <= 9; ++ i) cout << count(b, i) - count(a - 1, i) << " ";
        cout << endl;
    }
    return 0;
}
```

```cpp
# include <iostream>
# include <cmath>

using namespace std;

int dgt(int n) 		// 计算整数n有多少位
{
    int res = 0;
    while (n) ++ res, n /= 10;
    return res;
}

int cnt(int n, int i) // 计算从1到n的整数中数字i出现多少次 
{
    int res = 0, d = dgt(n);
    // 从右到左第j位上 数字i出现多少次
    for (int j = 1; j <= d; ++ j)  
    {
        // l和r是第j位左边和右边的整数 (视频中的abc和efg); dj是第j位的数字
        // n=123456, j=3, p=100, l=123456/100/10=123, r=123456%100=56, dj=123456/100%10=4
        int p = pow(10, j - 1), l = n / p / 10, r = n % p, dj = n / p % 10;
        
        
        // 计算第j位左边的整数小于l (视频中l = 000 ~ abc - 1)的情况 左边不等于abc的时候 说明都是比abc小的数字  
        if (i) res += l * p; 
        // 如果不是统计数字0 左边直接乘p就行了 n=ab3xxx p=1000  
		// n=1236055 6000-6999这里1000  第j位上的6出现了p次 
        // 但是左边还有16000-16999 26000-26999 36000-36999...1226000-1226999 共左边数字l（即123）个 所以是l*p 
        else if (!i && l) res += (l - 1) * p; 
        // 统计的数字i = 0, 左边高位不能全为0(视频中xxx = 001 ~ abc - 1) 
		// 少了0000-0999的一种情况 从10000-10999 开始 ... 1220000-1220999 13000-13999 共(l-1)次 
		
        
		// 计算第j位左边的整数等于l (视频中l = abc)的情况 只会和*j位后面的数*有关
		// 下面就是l的左边相等的情况 对第j位上 不会多算6000-6999 ...1226000-1226999里面的任意个集合 123开始的情况
        if ( (dj > i) && (i || l) ) res += p;//第j位比现在统计的数字大 就可以直接加上p中情况 
		// n=1236055  则有1235000-1235999 999+1种情况 即p种 
		// 当统计的数字i==0 且 l==0， 举例  n=123456 l==0 第j位为1  就是p=100000 此时000000-099999是不成立的 
        // 因为我要统计第j位为i的时候 有多少个这样的 数 
        // 而此时   000000-099999 显然和 100000-199999 第j-1位为2的时候重复了

        if ( (dj == i) && (i || l) ) res += r + 1;//这是r有多少个 就是多少个+1
		// if(dj==i) n=1236055  1236000-1236055   即55+1种情况
		// 当统计的数字i==0 且 l==0， 举例  n=123456 l==0且i==0 就是000000 -0123456 
        // 而这个时候显然和 第j-1的位的时候重复了100000-109999

		// if(dj>i) n=1236000 则有1237000-1237999 所以是0 
    }
    return res;
}

int main()
{
    int a, b;
    while (cin >> a >> b)
    {
        if(a == 0 && b == 0)	break;
        if (a > b) swap(a, b);
        for (int i = 0; i <= 9; ++ i) cout << cnt(b, i) - cnt(a - 1, i) << ' ';
        cout << endl;
    }
    return 0;
}
```

## 状态压缩DP

### 蒙德里安的梦想

> 求把 $N×M$ 的棋盘分割成若干个 $1×2$ 的长方形，有多少种方案。
>
> 例如当 $N=2，M=4$ 时，共有 $5$ 种方案。当 $N=2，M=3$ 时，共有 $3$ 种方案。
>
> 如下图所示：
>
> ![2411_1.jpg](https://www.acwing.com/media/article/image/2019/01/26/19_4dd1644c20-2411_1.jpg)

当我们把所有的横向小方格摆放好的时候，那么剩下的格子只能用纵向小方格填充，填充方法唯一确定。总方案数等于只放横着的小方块的合法方案数。

因此摆放所有$1\times2$小方格的方法的数量就等于横着摆放$1\times2$小方格的数量

如何判断，当前方案数是否合法？ 所有剩余位置能否填充满竖着的小方块。可以按列来看，每一列内部所有连续的空着的小方块需要是偶数个。

每一列用状态$f(i,j)$来表示已经将前 $i -1$ 列摆好，且从第$i-1$列，伸出到第$i$ 列的状态是$j$的所有方案。其中j是一个二进制数，用来表示哪一行的小方块是横着放的，其位数和棋盘的行数一致。

要摆第$i$列时，上一列伸出来的小方格的行的状态为$j$（二进制），$j \in[0,31]$

假设当前状态为$j=(00001)_2$，上一个状态为$k=(10010)_2$

$00001 \quad \& \quad10010 = 00000$

一定要满足j和k不能冲突，即$j\&k=0$

当我们一列内用横向格子填充完毕后，要用纵向的格子去填充，所以连续的格子里面只会剩下偶数个空格子，即$j|k$不能存在连续奇数个0

$00001 \quad | \quad10010 = 10011$

$f(i, j) += f(i - 1, k)$ 

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

typedef long long ll;
const int N = 12, M = 1 << N;
// 1 << n 相当于 pow(2, n) , m代表着列的所有情况,从(00000...)到(11111...)

int n, m;		// n行m列
ll f[N][M];		// 注意范围long long
bool st[M];

int main()
{
    while(cin >> n >> m)
    {
        if(n == 0 && m == 0)    break;
        memset(f, 0, sizeof(f));
        for(int i = 0; i < 1 << n; i ++)		// 判断存在连续0的状态,筛掉奇数个连续0的情况
        {
            st[i] = true;						// 先初始化位true，在判断是否存在这种情况
            int cnt = 0;
            for(int j = 0; j < n; j ++)
            {
                // 如果到1了就判断前面连续0的个数是否为奇数, i >> j代表i的二进制第j位数
                if(i >> j & 1)					
                {
                    if(cnt & 1) st[i] = false;
                    cnt = 0;
                }
                else    cnt ++;					
            }
            if(cnt & 1) st[i] = false;
            // 奇数 & 1 = 1, 偶数 & 1 = 0
        }
        
        f[0][0] = 1;
        // 按定义这里是：前第-1列都摆好，且从-1列到第0列伸出来的状态为0的方案数。
        // 首先，这里没有-1列，最少也是0列。
        // 其次，没有伸出来，即没有横着摆的。即这里第0列只有竖着摆这1种状态。
        
        for(int i = 1; i <= m; i ++)					// 从第0列伸到第1列的情况开始
            for(int j = 0; j < 1 << n; j ++)				// j为这一列的情况
                for(int k = 0; k < 1 << n; k ++)			// k为上一列伸到这一列的情况
                    if((j & k) == 0 && st[j | k])	f[i][j] += f[i - 1][k];	
                    // 如果不冲突并且不存在连续奇数个0的情况，注意&和==的优先级
                    // 当前列的方案数就等于之前的第i-1列所有状态k的累加
        cout << f[m][0] << endl;						// 从0到m-1列已经处理完了，并且从m-1列没有伸到m列的情况
    }
    return 0;
}
```

### 最短Hamilton路径

> 给定一张 $n$ 个点的带权无向图，点从 $0∼n−1$ 标号，求起点 $0$ 到终点 $n−1$ 的最短 Hamilton 路径。
>
> Hamilton 路径的定义是从 $0$ 到 $n−1$ 不重不漏地经过每个点恰好一次。

假设:一共有七个点,用0,1,2,3,4,5,6来表示,那么先假设终点就是5,在这里我们再假设还没有走到5这个点,且走到的终点是4,那么有以下六种情况:
first: 0–>1–>2–>3–>4 距离:21
second: 0–>1–>3–>2–>4 距离:23
third: 0–>2–>1–>3–>4 距离:17
fourth: 0–>2–>3–>1–>4 距离:20
fifth: 0–>3–>1–>2–>4 距离:15
sixth: 0–>3–>2–>1–>4 距离:18

如果此时你是一个商人你会走怎样的路径?显而易见,会走第五种情况对吧?因为每段路程的终点都是4,且每种方案的可供选择的点是0到4,而商人寻求的是走到5这个点的最短距离,而4到5的走法只有一种,所以我们选择第五种方案，可寻找到走到5这个点儿之前,且终点是4的方案的最短距离,此时0~5的最短距离为(15+4走到5的距离).(假设4–>5=8)

同理:假设还没有走到5这个点儿,且走到的终点是3,那么有一下六种情况:
first: 0–>1–>2–>4–>3 距离:27
second: 0–>1–>4–>2–>3 距离:22
third: 0–>2–>1–>4–>3 距离:19
fourth: 0–>2–>4–>1–>3 距离:24
fifth: 0–>4–>1–>2–>3 距离:26
sixth: 0–>4–>2–>1–>3 距离:17

此时我们可以果断的做出决定:走第六种方案!!!,而此时0~5的最短距离为(17+3走到5的距离)(假设3–>5=5)

在以上两大类情况之后我们可以得出当走到5时:
1.以4为终点的情况的最短距离是:15+8=23;
2.以3为终点的情况的最短距离是:17+5=22;
经过深思熟虑之后,商人决定走以3为终点的最短距离,此时更新最短距离为:22。

当然以此类推还会有以1为终点和以2为终点的情况,此时我们可以进行以上操作不断更新到5这个点的最短距离,最终可以得到走到5这个点儿的最短距离,然后再返回最初的假设,再依次假设1,2,3,4是终点,最后再不断更新,最终可以得出我们想要的答案

用二进制来表示要走的所以情况的路径,这里用i来代替
例如走0,1,2,4这三个点,则表示为:10111;
走0,2,3这三个点:1101;		从右往左表示

* 状态表示$f(i,j)$
  * 集合：所有从0走到j，走过的所有点是i的所有路径（i的二进制表示经过的点）
  * 属性：最小值

* 状态计算

  根据倒数第二个点是哪个点来分类，假设倒数第二个点为k，0 ->... -> k -> j，从k到j的距离是固定的，所以要让从0到k最短

  而从0到k中间不能再经过j这个点，除此之外的路径 即$f(i-\{j\},k)+a(k,j)$

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 20, M = 1 << N;

int n;
int w[N][N], f[M][N];   // M表示路径，N表示终点

int main()
{
    cin >> n;
    for(int i = 0; i < n; i ++)
        for(int j = 0; j < n; j ++)
            cin >> w[i][j];
    memset(f, 0x3f, sizeof(f));				// 因为要求最小值，所以初始化为无穷大
    f[1][0] = 0;							// 因为零是起点,所以f[1][0]=0;
    for(int i = 0; i < 1 << n; i ++)		// i表示所有的情况
        for(int j = 0; j < n; j ++)			// j表示走到哪一个点
            if(i >> j & 1)					// 在i表示的情况里面，j必须出现
                for(int k = 0; k < n; k ++)	// k表示走到j这个点之前,以k为终点的最短距离
                    if((i ^ (1 << j)) >> k & 1)	// i ^ (1 << j) 表示 i表示的情况里面j点为0
                        f[i][j] = min(f[i][j], f[i ^ (1 << j)][k] + w[k][j]);
    // i ^ (1 << j) 表示将i的第j位改变后的值, 1 << j 表示 100...0(j个0)
    // 若i的第j位为1则异或为0，若为0则异或为1
    // i = 11111 , 终点为3, 1 << 3 = 1000, 11111 ^ 1000 = 10111
    cout << f[(1 << n) - 1][n - 1] << endl;
    // 表示所有点都走过了,且终点是n-1的最短距离
    // 位运算的优先级低于'+'-'所以有必要的情况下要打括号
    // f[111...1(n个1)][n-1]
    // 构造n个1，直接 1 << n 构造出1000...0(n个0),然后-1即可
    return 0;
}
```

## 树形DP

### 没有上司的舞会

> Ural 大学有 $N$名职员，编号为 $1∼N$。
>
> 他们的关系就像一棵以校长为根的树，父节点就是子节点的直接上司。
>
> 每个职员有一个快乐指数，用整数 $H_i$ 给出，其中 $1≤i≤N$。
>
> 现在要召开一场周年庆宴会，不过，没有职员愿意和直接上司一起参会。
>
> 在满足这个条件的前提下，主办方希望邀请一部分职员参会，使得所有参会职员的快乐指数总和最大，求这个最大值。

* 状态表示

  * 集合：

    $u$表示当前节点

    $f(u,0)$表示所有从以u为根的子树中选择，并且不选u这个点的方案

  ​		$f(u,1)$表示所有从以u为根的子树中选择，并且选择u这个点的方案

  * 属性：最大值

* 状态计算

  $f(u,0)=\Sigma(f(s_i,0),f(s_i,1))$

  $f(u,1)=\Sigma f(s_i,0)$

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 6010;

int n;
int happy[N], h[N], e[N], ne[N], idx;
int f[N][2];
bool has_father[N];

void add(int a, int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
}

void dfs(int u)
{
    f[u][1] = happy[u];						// 选u节点,就将u节点的开心值加上
    for(int i = h[u]; i != -1; i = ne[i])
    {
        int j = e[i];
        dfs(j);								// 要先递归处理子节点
        f[u][0] += max(f[j][0], f[j][1]);	// 不选u节点，根节点选取和不选取都行
        f[u][1] += f[j][0];					// 选了u节点，根节点不能选取
    }
}

int main()
{
    scanf("%d", &n);
    for(int i = 1; i <= n; i ++)    scanf("%d", &happy[i]);
    memset(h, -1, sizeof(h));
    for(int i = 0; i < n - 1; i ++)
    {
        int a, b;
        scanf("%d%d", &a, &b);
        has_father[a] = true;				// 标记有根节点，只有祖宗节点没有根节点
        add(b, a);
    }
    int root = 1;
    while(has_father[root]) root ++;		// 寻找根节点,从根节点开始dp
    dfs(root);
    printf("%d\n", max(f[root][0], f[root][1]));
    return 0;
}
```

## 记忆化搜索

### 滑雪

> 给定一个 $R$ 行 $C$ 列的矩阵，表示一个矩形网格滑雪场。
>
> 矩阵中第 $i$ 行第 $j$ 列的点表示滑雪场的第 $i$ 行第 $j$ 列区域的高度。
>
> 一个人从滑雪场中的某个区域内出发，每次可以向上下左右任意一个方向滑动一个单位距离。
>
> 当然，一个人能够滑动到某相邻区域的前提是该区域的高度低于自己目前所在区域的高度。
>
> 现在给定你一个二维矩阵表示滑雪场各区域的高度，请你找出在该滑雪场中能够完成的最长滑雪轨迹，并输出其长度(可经过最大区域数)。

* 状态表示$f(i,j)$

  * 集合：所有从$(i,j)$开始滑的路径
  * 属性：所有路径的最大长度

* 状态计算

  $(i,j)$之后往上下左右四个方向滑

递归形式实现记忆化搜索

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 310;

int n, m, res = -0x3f3f3f3f;
int h[N][N], f[N][N];

int dx[4] = {-1, 1, 0, 0}, dy[4] = {0, 0, -1, 1};

int dp(int x,int y)
{
    if(f[x][y] != -1)   return f[x][y];
    f[x][y] = 1;
    for(int i = 0; i < 4; i ++)
    {
        int a = x + dx[i], b = y + dy[i];
        if(a >= 1 && a <= n && b >= 1 && b <= m && h[a][b] < h[x][y])
            f[x][y] = max(f[x][y], dp(a, b) + 1);
    }
    return f[x][y];
}

int main()
{
    memset(f, -1, sizeof(f));
    scanf("%d%d", &n, &m);
    for(int i = 1; i <= n; i ++)
        for(int j = 1; j <= m; j ++)
            scanf("%d", &h[i][j]);
    for(int i = 1; i <= n; i ++)
        for(int j = 1; j <= m; j ++)
            res = max(res, dp(i, j));
    cout << res << endl;
    return 0;
}
```

