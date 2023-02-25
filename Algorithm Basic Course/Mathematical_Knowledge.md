# 数学知识

化为最小正整数

$(x\%m + m)\%m$

## 质数

> 质数和合数的概念只针对于大于1的整数成立。
>
> 质数：在大于1的整数中，如果只包含1和本身这两个约数，就被称为质数，或者素数。

### 质数的判定

**试除法**

```cpp
bool is_prime(int n)
{
    if(n < 2)	return false;
    for(int i = 2; i < n; i ++)
    {
        if(n % i == 0)	return false;
    }
    return true;
}
```

$$
d|n->\frac{n}{d}|n\\
(a|b 表示a可以被b整除)\\
3|12->4|12\\
2|12->6|12
$$

因此在枚举的过程中，我们可以只枚举较小的那个数，即$d\leq\frac{n}{d},d^2\leq n, d\leq\sqrt{n}$，

**试除法判定质数算法模板**

$O(\sqrt{n})$

```cpp
bool is_prime(int x)
{
    if (x < 2) return false;
    for (int i = 2; i <= x / i; i ++ )
        if (x % i == 0)
            return false;
    return true;
}
```

### 分解质因数

**试除法**

从小到大枚举所有的数，判断是否能被整除从而枚举所有质因数$O(n)$

```cpp
void divide(int n)
{
    for(int i = 2; i <= n; i ++)
    {
    	if(n % i == 0)
        {
            int s = 0;
            while(n % i == 0)
            {
                n /= i;
                s ++;
            }
            printf("%d %d\n", i, s);
        }
    }
}
```

n中最多只包含一个大于$\sqrt{n}$的质因子（假如有两个大于$\sqrt{n}$的质因子，乘起来就会大于n）

因此只需要枚举2到$\sqrt{n}$，在判断最后剩下来的数是否为1，不为1说明是大于$\sqrt{n}$的质因子并输出，优化为$O(\sqrt{n})$

**试除法分解质因数算法模板**

```cpp
void divide(int x)
{
    for (int i = 2; i <= x / i; i ++ )
        if (x % i == 0)		// i 一定是质数
        {
            int s = 0;
            while (x % i == 0) x /= i, s ++ ;
            printf("%d %d\n", i, s);
        }
    if (x > 1) printf("%d 1\n", x);
    cout << endl;
}
```

## 筛法

### 朴素筛法

当$i=2$时，循环$\frac{n}{2}$次

当$i=3$时，循环$\frac{3}{n}$次
$$
\frac{n}{2}+\frac{n}{3}+...+\frac{n}{n}=n(\frac{1}{2}+\frac{1}{3}+...+\frac{1}{n})\\
1+\frac{1}{2}+\frac{1}{3}+...+\frac{1}{n} = lnn + c\\
\therefore \frac{n}{2}+\frac{n}{3}+...+\frac{n}{n}=nlnn<nlogn
$$
$O(nlogn)$

**朴素筛法求素数算法模板**

```cpp
int primes[N], cnt;     // primes[]存储所有素数
bool st[N];         // st[x]存储x是否被筛掉,m

void get_primes(int n)
{
    for(int i = 2; i <= n; i ++)
    {
        if(!st[i])	
        {
            primes[cnt ++] = n;
        }
        for(int j = i + i; j <= n; j += i)
            st[j] = true; 
    }
}
```

### 埃氏筛法

如果我们留下了$p$，说明$p$是一个质数。

对于合数来说，它的倍数也一定被这个合数的因此筛掉了，所有不用再考虑筛合数的倍数

我们可以只删所有质数的倍数，对于非质数，我们不需要删他的倍数

**质数定理**：$1-n$中有$\frac{n}{lnn}$个质数

优化为$O(nloglogn)$

**埃氏筛法求素数算法模板**

```cpp
int primes[N], cnt;     // primes[]存储所有素数
bool st[N];         // st[x]存储x是否被筛掉

void get_primes(int n)
{
    for (int i = 2; i <= n; i ++ )
    {
        if (st[i]) continue;
        primes[cnt ++ ] = i;
        for (int j = i + i; j <= n; j += i)
            st[j] = true;
    }
}
```

### 欧拉(线性)筛法

每个数$n$只会被最小质因子筛掉

循环里面有两种情况：

1. $i\%primes[j]==0$， $primes[j]$一定是$i$的最小质因子，$primes[j]$一定是$primes[j]\times i$的最小质因子
2. $i\%primes[j]!=0$， $primes[j]$一定小于$i$的所有最小质因子，$primes[j]$也一定是$primes[j]\times i$的最小质因子

对于一个合数x，假设$primes[j]$是$x$的最小质因子，当$i$枚举到$x/primes[j]$的时候，就会被筛掉

所以，每个数只会被他的最小质因子筛掉，只会被筛一次

可以通过**算术基本定理**来理解

$O(n)$

e.g. 当$primes[]$中已经存储了2，3，5时，判断6，已经被2标记过

$st[2\times 6]=true$    退出循环    3 * 6 = 18 = 2 * 9  所以18一定会被2标记  

当$i$ 是 $primes[j]$ 的倍数时， $a \times i = a \times primes[j] * k$ 必定可以被$primes[j]$整除

**线性筛法求素数算法模板**

```cpp
int primes[N], cnt;     // primes[]存储所有素数
bool st[N];         // st[x]存储x是否被筛掉

void get_primes(int n)
{
    for (int i = 2; i <= n; i ++ )
    {
        if (!st[i]) primes[cnt ++ ] = i;
        for (int j = 0; primes[j] <= n / i; j ++ )
        {
            st[primes[j] * i] = true;
            if (i % primes[j] == 0) break;		// primes[j]一定是i的最小质因子
        }
    }
}
```

## 约数

### 试除法求约数

**试除法求所有约数算法模板**

```cpp
vector<int> get_divisors(int x)
{
    vector<int> res;
    for (int i = 1; i <= x / i; i ++ )
        if (x % i == 0)
        {
            res.push_back(i);
            if (i != x / i) res.push_back(x / i);
        }
    sort(res.begin(), res.end());
    return res;
}
```

### 约数个数

基于**算术基本定理**
$$
N=p_1^{\alpha 1}\times p_2^{\alpha 2}\times ... \times p_n^{\alpha n}\\
约数个数:(\alpha_1+1)\times (\alpha_2+1) \times ... \times (\alpha_n+1)\\
证明如下:\\
设m为N的约数\\
又\because N=p_1^{\alpha 1}\times p_2^{\alpha 2}\times ... \times p_n^{\alpha n}\\
\therefore m=p_1^{\beta 1}\times p_2^{\beta 2}\times ... \times p_n^{\beta n}(\beta_i \in{0,1,...,\alpha_i})\\
\therefore \beta_1有\alpha_1+1种选法，...,\beta_n有\alpha_n+1种选法\\
由分步乘法计数原理\\
m有(\alpha_1+1)\times (\alpha_2+1) \times ... \times (\alpha_n+1)种选法
$$

### 约数之和

$$
N=p_1^{\alpha 1}\times p_2^{\alpha 2}\times ... \times p_n^{\alpha n}\\
约数之和:(p_1^0+p_1^1+...p_1^{\alpha_1})\times ... \times (p_k^0+p_k^1+...p_k^{\alpha_k})\\
(乘法分配律展开)
$$

$$
72=2^3\times 3^2\\
约数个数:(3+1)\times (2+1) = 12\\
1,2,4,8\\1,3,9\\6,12,18,24,36,72\\
1\times (1+3^1+3^2)+2\times (1+3^1+3^2)+2^2\times (1+3^1+3^2)+2^3\times (1+3^1+3^2)=(1+2+2^2+2^3)\times(1+3+3^2)\\
$$

$$
p^0=1\\
p^0+p^1=(1\times p) + 1= p + 1\\
p^0+p^1+p^2= ((p + 1) \times p) + 1 = p^2 + p + 1\\
ans = (ans \times p) + 1
$$



**约数个数和约数之和算法模板**

```cpp
如果 N = p1^c1 * p2^c2 * ... *pk^ck
约数个数： (c1 + 1) * (c2 + 1) * ... * (ck + 1)
约数之和： (p1^0 + p1^1 + ... + p1^c1) * ... * (pk^0 + pk^1 + ... + pk^ck)
    
// 质因数分解,存储在map中
void divide(int n)
{
    for(int i = 2; i <= n / i; i ++)
    {
        if(n % i == 0)
        {
            int s = 0;
            while(n % i == 0)	n /= i, s ++;
            mp[i] += s;
        }
    }
    if(n != 1)  mp[n] ++;
}

// 约数个数
for(auto p : mp)
{
    ans = (ans * (p.second + 1) % mod) % mod;
}

// 约数之和
for(auto p : mp)
{
    long long num = 1;
    for(int i = 0; i < p.second; i ++)
    {
        num = (num * p.first + 1) % mod;
    }
    ans = (ans * (num % mod)) % mod;
}
```

### 欧几里得算法

$$
\because d|a,d|b\\
\therefore d|a+b,d|ax+by\\
a\%b=a-\lfloor\frac{a}{b}\rfloor*b=a-c*b\\
(a,b)=(b,a\%b)=(b,a-c*b)\\
$$



**欧几里得算法算法模板**

```cpp
int gcd(int a, int b)
{
    return b ? gcd(b, a % b) : a;
}
```

## 欧拉函数

### 公式法

$$
1-N中与N互质的数的个数被称为欧拉函数,记为\phi(N)\\
在算术基本定理中,N=P_1^{\alpha_1}P_2^{\alpha_2}...P_m^{\alpha_m},则:\\
\phi(N)=N*\frac{p_1-1}{p_1}*\frac{p_2-1}{p_2}*...*\frac{p_m-1}{p_m\\}
$$

$$
1,2,3,4,5,6\\
与6互质的数有:1,5\\
6=2^1\times3^1\\
\phi(6)=6\times\frac{2-1}{2}\times\frac{3-1}{3}=2\\
$$

$$
证明如下:(容斥原理)\\
1.从1-N中去除p_1,p_2,...,p_k的所有倍数\\
N-\frac{N}{p_1}-\frac{N}{p_2}-...-\frac{N}{p_k}\\
2.加上所有p_i*p_j的倍数\\
N-\frac{N}{p_1}-\frac{N}{p_2}-...-\frac{N}{p_k}+\frac{N}{p_1p_2}+\frac{N}{p_1p_3}+\frac{N}{p_{k-1}p_k}\\
3.减去所有p_i*p_j*p_k的倍数\\
N-\frac{N}{p_1}-\frac{N}{p_2}-...-\frac{N}{p_k}+\frac{N}{p_1p_2}+\frac{N}{p_1p_3}+\frac{N}{p_{k-1}p_k}-\frac{N}{p_1p_2p_3}-...-\frac{N}{p_{k-2}p_{k-1}p_{k}}\\
N-\frac{N}{p_1}-\frac{N}{p_2}-...-\frac{N}{p_k}+\frac{N}{p_1p_2}+\frac{N}{p_1p_3}+\frac{N}{p_{k-1}p_k}+...+(-1)^k\frac{N}{p_1...p_k}=N*\frac{p_1-1}{p_1}*\frac{p_2-1}{p_2}*...*\frac{p_m-1}{p_m\\}=\phi(N)\\
$$

**求欧拉函数算法模板**

$O(\sqrt{n})$

```cpp
int phi(int x)
{
    int res = x;
    for (int i = 2; i <= x / i; i ++ )
        if (x % i == 0)
        {
            res = res / i * (i - 1);
            while (x % i == 0) x /= i;
        }
    if (x > 1) res = res / x * (x - 1);

    return res;
}
```

### 筛法

求1-N每个数的欧拉函数

$$
i = p_1^{\alpha_1} ...p_k^{\alpha_k}\\
primes[j] * i = primes[j]*p_1^{\alpha_1} ...p_k^{\alpha_k}\\
\\
若p为质数，那么1-p中有p-1个数和p互质\\
\\
假如i\%primes[j]==0\\
则primes[j]是i的最小质因子\\
\therefore primes[j] = p_1\\
\therefore primes[j] * i = p_1^{\alpha_1+1} ...p_k^{\alpha_k}\\
\phi(i)=i*(\frac{p_1-1}{p_1})*(\frac{p_2-1}{p_2})*...*(\frac{p_k-1}{p_k})\\
\phi(primes[j] * i)=primes[j] * i*(\frac{p_1-1}{p_1})*(\frac{p_2-1}{p_2})*...*(\frac{p_k-1}{p_k})=primes[j]*\phi(i)\\
\therefore \phi(primes[j] * i)=primes[j]*\phi(i)\\
\\
假如i\%primes[j]!=0\\
那么primes[j]一定是primes[j]*i的最小质因子,并且primes[j]不存在于i的质因子当中\\
\phi(i)=i*(\frac{p_1-1}{p_1})*(\frac{p_2-1}{p_2})*...*(\frac{p_k-1}{p_k})\\
\phi(primes[j]*i)=primes[j]*i*(\frac{p_1-1}{p_1})*...*(\frac{p_k-1}{p_k})*(\frac{primes[j]-1}{primes[j]})=primes[j]*\frac{primes[j]-1}{primes[j]}*\phi(i)\\
\therefore \phi(primes[j]*i)=(primes[j]-1)*\phi(i)\\
$$
**筛法求欧拉函数算法模板**

```cpp
int primes[N], cnt;     // primes[]存储所有素数
int euler[N];           // 存储每个数的欧拉函数
bool st[N];         // st[x]存储x是否被筛掉


void get_eulers(int n)
{
    euler[1] = 1;
    for (int i = 2; i <= n; i ++ )
    {
        if (!st[i])
        {
            primes[cnt ++ ] = i;
            euler[i] = i - 1;
        }
        for (int j = 0; primes[j] <= n / i; j ++ )
        {
            st[primes[j] * i] = true;
            if (i % primes[j] == 0)
            {
                euler[primes[j] * i] = euler[i] * primes[j];
                break;
            }
            euler[primes[j] * i] = euler[i] * (primes[j] - 1);
        }
    }
}
```

### 欧拉定理

$$
若a与n互质,则a^{\phi(n)} \equiv 1 (mod \; n)\\
特别地,当n为质数时,a^{n-1} \equiv 1 (mod \; n) \quad (费马定理)\\
$$


$$
证明如下:\\
1-n中,与n互质的数为:a_1,a_2,...,a_{\phi(n)}\\
又\because a与n互质\\
\therefore aa_1,aa_2,...,aa_{\phi(n)}也与n互质\\
\\
已知a*a_i与n互质,要证(a*a_i)\%n与n互质\\
设c=(a*a_i)\%n\\
假设c与n不互质\\
则存在d>1,s.t.c=kd,n=jd\\
\because c=(a*a_i)\%n\\
\therefore a*a_i=fn+c=fjd+kd= (fj+k)d\\
\because n=jd\\
\therefore a*a_i与n不互质,假设不成立\\
\therefore (a*a_i)\%n与n互质\\
\\
\because (a*a_i)\%n<n\\
又\because 1-n中,与n互质的数为:a_1,a_2,...,a_{\phi(n)}\\
\therefore (a*a_i)\%n \in \{a_1,a_2,...,a_{\phi(n)}\}\\
\because (a\times b)\%n=(a\%n)\times (b\%n)且aa_1,aa_2,...,aa_{\phi(n)}互不相同\\
\therefore (aa_1\times aa_2\times ...\times aa_{\phi(n)})\%n=a_1\times a_2\times ...\times a_{\phi(n)}\\
\therefore a^{\phi(n)}\times a_1\times a_2\times ...\times a_{\phi(n)}= a_1\times a_2\times ...\times a_{\phi(n)} \; (mod \; n)\\
a^{\phi(n)} \equiv 1 (mod \; n)\\
$$

## 快速幂

快速求出$a^k \; mod\; p$ 

O(log k)
$$
a^k \; mod\; p\\
预处理出\; a^{2^0} \; mod \; p,....,a^{2^{log\;k}}\;mod\;p这log\;k个数\\
a^k=a^{2^{x_1}}a^{2^{x_2}}...a^{2^{x_t}}=a^{2^{x_1}+2^{x_2}+...+2^{x_t}}\\
k=2^{x_1}+2^{x_2}+...+2^{x_t}\\
(101011)_2=2^0+2^1+2^3+2^5\\
\\
预处理:\\
a^{2^0}=a\\
a^{2^1}=(a^{2^0})^2\\
...\\
a^{2^{log\;k}}=(a^{2^{log\;k-1}})^2\\
每个数是前面的数的平方,预处理出log\;k个数,复杂度O(log\;k)
$$

$$
(5)_{10}=(101)_2\\
4^5\;mod\;10\\
4^{2^0}\;mod\;10\equiv 4\;mod\;10\\
4^{2^1}\;mod\;10\equiv 6\;mod\;10\\
4^{2^2}\;mod\;10\equiv 6\;mod\;10\\
4^5=4^{2^0}4^{2^2}=4*6=4\;mod\;10
$$

**快速幂算法模板**

```cpp
// 求 m^k mod p，时间复杂度 O(logk)。

int qmi(int m, int k, int p)
{
    int res = 1 % p, t = m;
    while (k)
    {
        if (k&1) res = res * t % p;
        t = t * t % p;
        k >>= 1;
    }
    return res;
}
```

### 快速幂求逆元

乘法逆元：==**b与p互质且p为质数**==

> 若整数$b,m$互质，并且对于任意的整数$a$，如果满足$b|a$，则存在一个整数$x$，使得$a/b\equiv a\times x\;(mod\;m)$，则称$x$为$b$的模$m$乘法逆元，记为 $b^{−1}\;(mod\;m)$。
> $b$存在乘法逆元的充要条件是$b$与模数$m$互质。当模数$m$为质数时，$b^{m−2}$即为 b的乘法逆元。

$$
\frac{a}{b}\equiv ax\;mod\;m\\
\frac{a}{b}\equiv ab^{-1}\;mod\;m\\
bx\equiv 1(mod \;p)\\
若b、p互质且p为质数,则由费马定理,有b^{p-1}\equiv1(mod\;p)\\
bb^{p-2}\equiv1(mod\;p)\\
\therefore b^{p-2}为b的乘法逆元\\
!!!充要条件:b与p互质且p为质数\\
$$

```cpp
#include <iostream>

using namespace std;

typedef long long ll;

ll qmi(int m, int k, int p)
{
    ll res = 1, t = m;
    while(k)
    {
        if(k & 1)   res = res * t % p;
        t = t * t % p;
        k >>= 1;
    }
    return res;
}

int main()
{
    int n;
    cin >> n;
    while(n --)
    {
        int a, p;
        cin >> a >> p;
        if(a % p != 0)  cout << qmi(a, p - 2, p) << endl;
        else    cout << "impossible" << endl;
    }
    return 0;
}
```

## 扩展欧几里得

$$
裴蜀定理\\
对任意正整数a,b,一定存在非零整数x,y,使得ax+by=gcd(a,b)\\
当b=0时,ax+by=a,易得x=1,y可以取任意实数\\
当b!=0时,ax+by=gcd(a,b)=gcd(b,a\;mod\;b)\\
bx'+(a\;mod\;b)y'=gcd(b,a\;mod\;b)\\
bx'+(a-\lfloor\frac{a}{b}\rfloor b)y'=gcd(a,b)\\
ay'+b(x'-\lfloor\frac{a}{b}\rfloor y')=ax+by\\
互换x'、y'\\
ax'+b(y'-\lfloor\frac{a}{b}\rfloor x')=ax+by\\
x=x',y=y'-\lfloor\frac{a}{b}\rfloor x'\\
\\
ax+by=(a,b)\\
ax+by=d有解当且仅当d=k(a,b)\\
$$

**扩展欧几里得算法模板**

```cpp
// 求x, y，使得ax + by = gcd(a, b)
int exgcd(int a, int b, int &x, int &y)
{
    if (!b)
    {
        x = 1; y = 0;
        return a;
    }
    int d = exgcd(b, a % b, y, x);
    y -= (a/b) * x;
    return d;
}
```

### 求解线性同余方程

$$
ax\equiv b(mod \; m)\\
ax=my+b\\
ax-my=b\\
ax+my=b\\
当b为gcd(a,m)的倍数时有解,x = (long \;long)x\times b / gcd(a,m) \% m\\
$$

```cpp
#include <iostream>

using namespace std;

typedef long long ll;

int exgcd(int a, int b, int &x, int &y)
{
    if(!b)
    {
        x = 1, y = 0;
        return a;
    }
    int d = exgcd(b, a % b, y, x);
    y -= a / b * x;
    return d;
}

int main()
{
    int n;
    cin >> n;
    while(n --)
    {
        int a, b, k, m, x;
        cin >> a >> b >> m;
        int d = exgcd(a, m, x, k);
        if(b % d == 0)  cout << (ll)b / d * x % m<< endl;
        else    cout << "impossible" << endl;
    }
    return 0;
}
```

## 中国剩余定理

$$
给定一堆两两互质的数m_1,m_2,...,m_k,有线性同余方程组\\
\begin{cases}
\ x\equiv a_1(mod\;m_1)	\\
\ x\equiv a_2(mod\;m_2)	\\
\ ...... \\
\ x\equiv a_k(mod\;m_k)	\\
\end{cases}
\\
令M=m_1m_2...m_k\\
M_i=\frac{M}{m_i}(因为m_i两两互质,所以M_i与除了m_i以外的其他m互质)\\
M_i^{-1}表示M_i模m_i的逆(用扩欧来求解)\\
M_ix\equiv1(mod\;m_i) \Rightarrow M_ix+km_i=1\\
中国剩余定理:x=a_1\times M_1\times M_1^{-1}+a_2\times M_2\times M_2^{-1} + ...+a_k\times M_k\times M_k^{-1}
$$

```cpp
#include <iostream>

using namespace std;

typedef long long ll;
const int N = 15;

ll a[N], m[N];
ll M = 1, res;

ll exgcd(ll a, ll b, ll &x, ll &y)
{
    if(!b)
    {
        x = 1, y = 0;
        return a;
    }
    ll d = exgcd(b, a % b, y, x);
    y -= (a / b) * x;
    return d;
}

int main()
{
    int n;
    cin >> n;
    for(int i = 1; i <= n; i ++)
    {
        cin >> m[i] >> a[i];
        M *= m[i];
    }
    for(int i = 1; i <= n; i ++)
    {
        ll Mi = M / m[i], x, k, d;
        d = exgcd(Mi, m[i], x, k);
        x %= m[i];
        res = (res + a[i] * Mi * x) % M;
    }
    cout << (res % M + M) % M << endl;
    return 0;
}
```

### 拓展中国剩余定理

不满足两两互质条件时

> 给定$2n$个整数$a_1,a_2,...,a_n$和$m_1,m_2,...,m_n$，求一个最小的非负整数x，满足$\forall i \in[1,n],x\equiv m_i(mod\;a_i)$

$$
\begin{cases}
\ x\equiv m_1(mod\;a_1)	\\
\ x\equiv m_2(mod\;a_2)	\\
\ ...... \\
\ x\equiv m_n(mod\;a_n)	\\
\end{cases}
\\ 

\Rightarrow

\begin{cases}
\ x\;mod\;a_1=m_1	\\
\ x\;mod\;a_2=m_2    \\
\ ...... \\
\ x\;mod\;a_n=m_n	\\
\end{cases}
$$

$$
对于每两个式子(我们考虑将其合并):\\
\begin{cases}
\ x\;mod\;a_1=m_1\\
\ x\;mod\;a_2=m_2\\
\end{cases}
\\

\Rightarrow
\begin{cases}
\ x = k_1a_1+m_1\\
\ x = k_2a_2+m_2\\
\end{cases}
\\
\Rightarrow
k_1a_1+m_1 = k_2a_2+m_2\\
\Rightarrow k_1a_1-k_2a_2=m_2-m_1\\
\Rightarrow k_1a_1+k_2a_2=m_2-m_1\\
\because a_1、a_2、m_1、m_2已知\\
\therefore k_1a_1+k_2a_2=gcd(a_1,a_2),可求出k_1,k_2\\
k_1a_1+k_2a_2=m_2-m_1有解当且仅当gcd(a1,a2) | m2-m1\\
让k_1,k_2扩大\frac{m_2-m_1}{gcd(a_1,a_2)}倍,得到k_1a_1+k_2a_2=m_2-m_1的一组解\\
\begin{cases}
\ k1=k1+k\frac{a_2}{d}(k_1\in Z)\\
\ k2=k2+k\frac{a_a}{d}(k_2\in Z)\\
\end{cases}
\\
此时,新的k_1,k_2仍满足原式\\
但为了避免溢出,通常取k1为满足条件的最小的正整数\\
\therefore k_1=k_1\%\frac{a_2}{d},k_2=k2\%\frac{a_1}{d}\\
\therefore x=k_1a_1+m_1=(k1+k\frac{a_2}{d})a_1+m_1=a_1k_1+m_1+k\frac{a_1a_2}{d}
=a_1k_1+m_1+k*lcm(a_1,a_2)\\
令m_0=a_1k_1+m_1,a_0=lcm(a_1,a_2)\\
则x=m_0+ka_0
$$

```cpp
#include <iostream>

using namespace std;

typedef long long ll;

ll exgcd(ll a, ll b, ll &x, ll &y)
{
    if (!b)
    {
        x = 1, y = 0;
        return a;
    }
    ll d = exgcd(b, a % b, y, x);
    y -= a / b * x;
    return d;
}

int main()
{
    int n;
    cin >> n;
    ll a1, m1;
    cin >> a1 >> m1;
    for (int i = 0; i < n - 1; i ++ )
    {
        ll a2, m2, k1, k2;
        cin >> a2 >> m2;
        ll d = exgcd(a1, a2, k1, k2);
        if ((m2 - m1) % d)
        {
            cout << "-1" << endl;
            return 0;
        }

        k1 *= (m2 - m1) / d;
        k1 = (k1 % (a2/d) + a2/d) % (a2/d);

        m1 = k1 * a1 + m1;
        a1 = a1 / d * a2;
    }
    ll x = (m1 % a1 + a1) % a1;
    cout << x << endl;
    return 0;
}
```

## 高斯消元

$O(n^3)$的线性时间内求解n元线性方程组
$$
\\
\begin{cases}
\ a_{11}x_1+a_{12}x_2+...+a_{1n}x_n=b_1 \\
\ a_{21}x_1+a_{22}x_2+...+a_{2n}x_n=b_2 \\
\ ... \\
\ a_{n1}x_1+a_{n2}x_2+...+a_{nn}x_n=b_n \\
\end{cases}
\\
解的情况:无解、无穷多解、唯一解\\
\\
对系数矩阵进行如下初等行列变换操作:\\
1.把某一行乘一个非零的数\\
2.交换某两行\\
3.把某行的若干倍加到另一行上去\\
\\
1. 完美阶梯型——唯一解\\
2. 0=非零——无解\\
3. 0=0——无穷多组解\\
\\
高斯消元:\\
枚举每一列c:\\
1.找到当前列中绝对值最大的这一行\\
2.将这行换到最上面(第c行)去\\
3.将该行的第一个数变成1\\
4.将下面所有行的第c列消成0\\
$$


**高斯消元算法模板**

```cpp
#include <iostream>
#include <algorithm>
#include <cmath>

using namespace std;

const int N = 110;
const double eps = 1e-6;

double a[N][N];
int n;

int guass()
{
    int c, r;
    // 对每一列进行消元
    for(c = 1, r = 1; c <= n; c ++)
    {
        // 找出当前列的绝对值的最大值
        int t = r;
        for(int i = r; i <= n; i ++)
        {
            if(fabs(a[i][c]) > fabs(a[t][c]))   t = i;
        }
        
        // 如果最大值为0,跳出
        if(fabs(a[t][c]) < eps)   continue;

        // 将最大值所在的那一行换到最上面
        for(int i = c; i <= n + 1; i ++)    swap(a[t][i], a[r][i]);

        // 将r行c列最大值化为1
        for(int i = n + 1; i >= c; i --)    a[r][i] /= a[r][c];

        // 将c列下面的值都化为0
        for(int i = r + 1; i <= n; i ++)
        {
            if(fabs(a[i][c]) < eps)   continue;
            for(int j = n + 1; j >= c; j --){
                a[i][j] -= a[i][c] * a[r][j]; 
            }    
        }
        r ++;
    }
    
    
    if(r < n + 1)
    {
        for(int i = r; i <= n; i ++)
        {
            if(fabs(a[i][n + 1]) > eps)   return 3;     // 无解
        }
        return 2;       // 有无穷多组解
    }

    // 计算所有答案
    for(int i = n - 1; i >= 1; i --)
    {
        for(int j = i + 1; j <= n; j ++)
        {
            a[i][n + 1] -= a[i][j] * a[j][n + 1];
        }
    }
    
    return 1;       // 唯一解
}

int main()
{
    
    cin >> n;
    for(int i = 1; i <= n; i ++)
    {
        for(int j = 1; j <= n + 1; j ++)
        {
            scanf("%lf", &a[i][j]);
        }
    }
    int t = guass();
    
    if(t == 1)
    {
        for(int i = 1; i <= n; i ++)
        {
            if(fabs(a[i][n + 1]) < eps)     a[i][n + 1] = 0.00;     // 特判-0.00
            printf("%.2f\n", a[i][n + 1]);
        }
    }
    else if(t == 2)
    {
        cout << "Infinite group solutions" << endl;
    }
    else
    {
        cout << "No solution" << endl;
    }
    return 0;
}
```

### 高斯消元解异或线性方程组

```cpp
M[1][1]x[1] ^ M[1][2]x[2] ^ … ^ M[1][n]x[n] = B[1]
M[2][1]x[1] ^ M[2][2]x[2] ^ … ^ M[2][n]x[n] = B[2]
…
M[n][1]x[1] ^ M[n][2]x[2] ^ … ^ M[n][n]x[n] = B[n]
```

方程组中的系数和常数为 0 或 1，每个未知数的取值也为 0 或 1。

其中 `^` 表示异或($XOR$)，$M[i][j]$ 表示第 $i$ 个式子中 $x[j]$ 的系数，$B[i]$ 是第 $i$ 个方程右端的常数，取值均为 0 或 1。

异或 = 不进位加法0

1. 消成上三角矩阵
2. 判断解的情况

```cpp
#include <iostream>
#include <cmath>
#include <algorithm>

using namespace std;

const int N = 110;

int n;
int a[N][N];

int gauss()
{
    int c, r;
    for(c = 1, r = 1; c <= n; c ++)
    {
        int t = r;
        for(int i = r; i <= n; i ++)
        {
            if(fabs(a[i][c]) > fabs(a[t][c]))   t = i;
        }
        
        if(a[t][c] == 0)    continue;
        
        for(int i = c; i <= n + 1; i ++)    swap(a[t][i], a[r][i]);
        
        for(int i = n + 1; i >= c; i --)    a[r][i] /= a[r][c];
        
        for(int i = r + 1; i <= n; i ++)
        {
            if(a[i][c] == 0)    continue;
            for(int j = n + 1; j >= c; j --)
            {
                a[i][j] ^= a[r][j];
            }
        }
        
        r ++;
    }
    
    if(r < n + 1)
    {
        for(int i = r; i <= n; i ++)
        {
            if(a[i][n + 1] != 0)    return 3;
        }
        return 2;
    }
    
    for(int i = n - 1; i >= 1; i --)
    {
        for(int j = i + 1; j <= n; j ++)
        {
            a[i][n + 1] ^= a[i][j] * a[j][n + 1]; 
        }
    }
    
    return 1;
}

int main()
{
    cin >> n;
    for(int i = 1; i <= n; i ++)
    {
        for(int j = 1; j <= n + 1; j ++)
        {
            cin >> a[i][j];
        }
    }
    
    int t = gauss();
    
    if(t == 1)
    {
        for(int i = 1; i <= n; i ++)
        {
            cout << a[i][n + 1] << endl;
        }
    }
    else if(t == 2)
    {
        cout << "Multiple sets of solutions" << endl;
    }
    else
    {
        cout << "No solution" << endl;
    }
}
```

## 求组合数

### 递推法

10万组询问，a、b范围2000

$O(n^2)$
$$
C_a^b=\frac{a!}{b!(a-b)!}\\
C_a^b=C_{a-1}^{b}+C_{a-1}^{b-1}\\
$$
**递推法求组合数算法模板**

```cpp
// c[a][b] 表示从a个苹果中选b个的方案数
for (int i = 0; i < N; i ++ )
    for (int j = 0; j <= i; j ++ )
        if (!j) c[i][j] = 1;
        else c[i][j] = (c[i - 1][j] + c[i - 1][j - 1]) % mod;
```

### 预处理逆元

1万组询问，a、b范围 10<sup>5</sup>

$O(nlog\;n)$
$$
C_a^b=\frac{a!}{b!(a-b)!}\\
可以预处理阶乘\\
fact[i] = i!\;mod\; 10^9+7\\
infact[i] = (i!)^{-1}\;mod\;10^9+7\\
C_a^b=fact[a]\times infact[b-a] \times infact[b]\\
$$
**通过预处理逆元的方式求组合数算法模板**

```cpp
// 首先预处理出所有阶乘取模的余数fact[N]，以及所有阶乘取模的逆元infact[N]
// 如果取模的数是质数，可以用费马小定理求逆元
ll qmi(ll a, ll k, ll p)    // 快速幂模板
{
    ll res = 1;
    while (k)
    {
        if (k & 1) res = res * a % p;
        a = a * a % p;
        k >>= 1;
    }
    return res;
}

// 预处理阶乘的余数和阶乘逆元的余数
fact[0] = infact[0] = 1;
for (int i = 1; i < N; i ++ )
{
    fact[i] = fact[i - 1] * i % mod;
    infact[i] = qmi(fact[i], mod - 2, mod) % mod;
}

C_a^b = fact[a] * infact[b] % mod * infact[a - b] % mod
```

### Lucas定理

20组询问，a、b范围 10<sup>18</sup>，p范围10<sup>5</sup>

$p*log\;p*log\;n$ 
$$
C_a^b \equiv C_{a\;mod\;p}^{b\;mod\;p}\times C_{a/p}^{b/p}\;(mod\; p)\\
先将a、b化为比p小的数,再通过\\
C_a^b=\frac{a!}{b!(a-b)!}=\frac{a\times (a-1)\times ... \times 1}{b \times (b -1)\times ... \times 1 \times (a-b) \times ... \times 1}=\frac{a\times...\times(a-b+1)}{b \times (b -1)\times ... \times 1 }\\
$$
**Lucas定理算法模板**

```cpp
// 若p是质数，则对于任意整数 1 <= m <= n，有：
    C(n, m) = C(n % p, m % p) * C(n / p, m / p) (mod p)

ll qmi(ll a, ll k, ll p)  // 快速幂模板
{
    ll res = 1 % p;
    while (k)
    {
        if (k & 1) res = res * a % p;
        a = a * a % p;
        k >>= 1;
    }
    return res;
}

ll C(ll a, ll b, ll p)  // 通过定理求组合数C(a, b)
{
    if (a < b) return 0;

    ll x = 1, y = 1;  // x是分子，y是分母
    for (int i = a, j = 1; j <= b; i --, j ++ )
    {
        x = x * i % p;
        y = y * j % p;
    }

    return x * qmi(y, p - 2, p) % p;
}

int lucas(ll a, ll b, int p)
{
    if (a < p && b < p) return C(a, b, p);
    return C(a % p, b % p, p) * lucas(a / p, b / p, p) % p;
}
```

### 分解质因数法

$$
C_a^b=\frac{a\times(a-1)\times...\times(a-b+1)}{b\times(b-1)\times...\times1}=\frac{a!}{b!(a-b)!}\\
=p_1^{\alpha_1}p_2^{\alpha_2}...p_k^{\alpha_k}\\
$$

$$
求分子a!分解每个p的次数:\\
cnt_p(a!)=\lfloor \frac{a}{p}\rfloor + \lfloor \frac{a}{p^2}\rfloor+\lfloor \frac{a}{p^3}\rfloor+...=p的倍数中\in[1,a]的个数+p……2的倍数中\in[1,a]的个数+...\\
为什么只+1而不+k\\
因为a!中p^k低阶项的次数已经被算过了\\
比如p^3既是p 的倍数也是P^2的倍数\\
8!=8\times7\times6\times5\times4\times3\times2\times1\\
2的倍数:2、4、6、8\\
2^2的倍数:4、8\\
2^3的倍数:8\\
加起来是7\\
8=2\times2\times2,6=2\times3,4=2\times2,2=2\\
共有7个2
$$

**分解质因数法求组合数算法模板**

```cpp
当我们需要求出组合数的真实值，而非对某个数的余数时，分解质因数的方式比较好用：
    1. 筛法求出范围内的所有质数
    2. 通过 C(a, b) = a! / b! / (a - b)! 这个公式求出每个质因子的次数。 n! 中p的次数是 n / p + n / p^2 + n / p^3 + ...
    3. 用高精度乘法将所有质因子相乘

int primes[N], cnt;     // 存储所有质数
int sum[N];     // 存储每个质数的次数
bool st[N];     // 存储每个数是否已被筛掉


void get_primes(int n)      // 线性筛法求素数
{
    for (int i = 2; i <= n; i ++ )
    {
        if (!st[i]) primes[cnt ++ ] = i;
        for (int j = 0; primes[j] <= n / i; j ++ )
        {
            st[primes[j] * i] = true;
            if (i % primes[j] == 0) break;
        }
    }
}


int get(int n, int p)       // 求n！中的次数
{
    int res = 0;
    while (n)
    {
        res += n / p;
        n /= p;
    }
    return res;
}

vector<int> mul(vector<int> &A, int b)			// 高精度乘低精度模板
{
    vector<int> C;

    int t = 0;
    for (int i = 0; i < A.size() || t; i ++ )
    {
        if (i < A.size()) t += A[i] * b;
        C.push_back(t % 10);
        t /= 10;
    }

    while (C.size() > 1 && C.back() == 0) C.pop_back();

    return C;
}



int main()
    
get_primes(a);  // 预处理范围内的所有质数

for (int i = 0; i < cnt; i ++ )     // 求每个质因数的次数
{
    int p = primes[i];
    sum[i] = get(a, p) - get(b, p) - get(a - b, p);
}

vector<int> res;
res.push_back(1);

for (int i = 0; i < cnt; i ++ )     // 用高精度乘法将所有质因子相乘
    for (int j = 0; j < sum[i]; j ++ )
        res = mul(res, primes[i]);

for(int i = res.size() - 1; i >= 0; i --)
    printf("%d", res[i]);
```

## 卡特兰数

**卡特兰数算法模板**

```cpp
给定n个0和n个1，它们按照某种顺序排成长度为2n的序列，满足任意前缀中0的个数都不少于1的个数的序列的数量为：
Cat(n) = C(2n, n) / (n + 1)
```

$$
n=3\\
1.000111\\
2.001101\\
3.001011\\
4.010011\\
5.010101\\
\\
C_{2n}^n-C_{2n}^{n-1}=\frac{C_{2n}^n}{n+1}
$$

将 01 序列置于坐标系中，起点定于原点。若 0 表示向右走，1 表示向上走，那么任何前缀中 0 的个数不少于 1 的个数就转化为，路径上的任意一点，横坐标大于等于纵坐标。题目所求即为这样的合法路径数量。

下图中，表示从 $(0,0)$ 走到 $(n,n)$ 的路径，在绿线及以下表示合法，若触碰红线即不合法。

由图可知，任何一条不合法的路径（如黑色路径），都对应一条从 $(0,0)$ 走到 $(n−1,n+1)$ 的一条路径（如灰色路径）。而任何一条 $(0,0)$ 走到 $(n−1,n+1)$ 的路径，也对应了一条从$(0,0)$ 走到 $(n,n)$ 的不合法路径。

从$(0,0)$走到$(n,n)$的路径有$C_{2n}^n$条，从$(0,0)$走到$(n-1,n+1)$的路径有$C_{2n}^{n-1}$条，合法路径条数为
$$
C_{2n}^{n}-C_{2n}^{n-1}=\frac{(2n)!}{n!n!}-\frac{(2n)!}{(n-1)!(n+1)!}\\
=\frac{(2n)!}{n\times(n-1)\times...\times1\times n\times(n-1)\times...\times1}-\frac{(2n)!}{(n-1)\times...\times 1\times (n+1) \times n \times...\times 1}\\
=\frac{(2n)!}{n\times(n-1)\times...\times1\times n\times(n-1)\times...\times1}-\frac{n}{n+1}\times \frac{(2n)!}{n\times (n-1)\times...\times 1\times  n \times...\times 1}\\
=\frac{1}{n+1}\times \frac{(2n)!}{n!n!}= \frac{C_{2n}^n}{n+1}\\
这里的n+1也要记得用逆元处理!!!
$$
<img src="https://m1.im5i.com/2022/12/25/UGUP2W.jpg" style="zoom: 50%;" />

## 容斥原理

2个韦恩图的面积并:$S_1+S_2-S_1S_2$

3个韦恩圆的面积并:$S_1+S_2+S_3-S_1S_2-S_1S_3-S_2S_3+S_1S_2S_3$

n个韦恩圆的面积并:$S_1+S_2+...+S_n-S_1S_2-...-S_{n-1}S_n+...+(-1)^{n-1}S_1S_2...S_n$

共有$C_n^1+C_n^2+...+C_n^n=2^n-1$项		$O(2^n)$

从集合的角度来看:
$$
|S_1\bigcup S_2\bigcup...\bigcup S_n|=\Sigma_i|S_i|-\Sigma_{ij}|S_i\bigcap S_j|+\Sigma_{ijk}|S_i\bigcap S_j\bigcap S_k|...
$$

$$
\because C_k^0-C_k^1+C_k^2-C_k^3+C_k^4+...+(-1)^{n}C_k^k=1\\
\therefore C_k^1-C_k^2+C_k^3-C_k^4+...+(-1)^{n-1}C_k^k=1\\
$$

> 给定一个整数 $n$ 和 $m$ 个不同的质数 $p_1,p_2,…,p_m$。
>
> 请你求出 $1∼n$ 中能被 $p_1,p_2,…,p_m $中的至少一个数整除的整数有多少个。

1~10中能被2或3整除的数的个数：
$$
S_2=\{2,4,6,8,10\}\\
S_3=\{3,6,9\}\\
S_2\bigcap S_3=\{6\}\\
|S_2\bigcup S_3|=|S_2|+|S_3|-|S_2\bigcap S_3|=5+3-1=7\\
$$
只需要计算$2^m$次
$$
1-n中p的倍数的个数:\lfloor \frac{n}{p}\rfloor \\
|S_{p_1}\bigcup S_{p_2}\bigcup ... \bigcup S_{p_n}| = |S_{p_1}|+...+|S_{p_n}|-|S_{p_1}\bigcap S_{p_2}|-...+|S_{p_1}\bigcap S_{p_2} \bigcap S_{p_3}|+...\\
$$
那么如何用代码表示每个集合选与不选的状态呢？

一共有$2^n-1$中状态，由1开始

这里使用二进制，以$m=4$为例，$(1101)_2$表示选中集合$S_1,S_2,S_4$，所以集合中的元素个数为$\frac{n}{p_1p_2p_4}$

再根据公式，可以得到前面的系数为$(-1)^{3-1}=1$

所以在这个状态下就是$res += n/ (p1*p1*p4)$

但是需要特别注意枚举的质数的积大于n的情况，这里需要特判一下

以及为什么要 $n/(p_1\times ... \times p_n)$而不是$n/p_1/.../p_n$，是因为直接整除会有精度误差

```cpp
#include <iostream>
#include <cmath>

using namespace std;

typedef long long ll;

const int N = 20;

int p[N];
int n, m;

int main()
{
    cin >> n >> m;
    for(int i = 0; i < m; i ++)    cin >> p[i];
    ll res = 0;
    for(int i = 1; i < pow(2, m); i ++)
    {
        int cnt = 0;
        ll t = 1;
        for(int j = 0; j < m; j ++)
        {
            if(i >> j & 1)
            {
                if(t * p[j] > n)
                {
                    t = -1;
                    break;
                }
                t *= p[j];
                cnt ++;
            }
        }
        if(t == -1) continue;
        // cout << cnt << " " << t << endl;
        if(cnt % 2) res += n / t;
        else    res -= n / t;
    }
    cout << res << endl;
    return 0;
}
```

## 博弈论

### 公平组合游戏ICG

若一个游戏满足：

1. 由两名玩家交替行动；

2. 在游戏进程的任意时刻，可以执行的合法行动与轮到哪名玩家无关；
3. 不能行动的玩家判负；

则称该游戏为一个公平组合游戏。
NIM博弈属于公平组合游戏，但城建的棋类游戏，比如围棋，就不是公平组合游戏。因为围棋交战双方分别只能落黑子和白子，胜负判定也比较复杂，不满足条件2和条件3。

### 有向图游戏

给定一个有向无环图，图中有一个唯一的起点，在起点上放有一枚棋子。两名玩家交替地把这枚棋子沿有向边进行移动，每次可以移动一步，无法移动者判负。该游戏被称为有向图游戏。
任何一个公平组合游戏都可以转化为有向图游戏。具体方法是，把每个局面看成图中的一个节点，并且从每个局面向沿着合法行动能够到达的下一个局面连有向边。



先手必胜状态：先手进行某一个操作，留给后手是一个必败状态时，对于先手来说是一个必胜状态。即先手可以走到某一个必败状态。

先手必败状态：先手无论如何操作，留给后手都是一个必胜状态时，对于先手来说是一个必败状态。即先手走不到任何一个必败状态。

### Mex运算

设S表示一个非负整数集合。定义$mex(S)$为求出不属于集合$S$的最小非负整数的运算，即：
$mex(S) = min\{x\}$, $x$属于自然数，且$x$不属于$S$

也就是集合里面不存在的最小的自然数，比如$mex\{1,2,3\} = 0$

### SG函数

终点状态为全部是0，$SG(终点)=0$

在有向图游戏中，对于每个节点$x$，设从$x$出发共有$k$条有向边，分别到达节点$y_1, y_2, …, y_k$，定义$SG(x)$为$x$的后继节点$y_1, y_2, …, y_k $的$SG$函数值构成的集合再执行$mex(S)$运算的结果，即：
$SG(x) = mex(\{SG(y1), SG(y2), …, SG(yk)\})$
特别地，整个有向图游戏$G$的$SG$函数值被定义为有向图游戏起点$s$的$SG$函数值，即$SG(G) = SG(s)$。

### 有向图游戏的和

设$G_1, G_2, …, G_m$ 是$m$个有向图游戏。定义有向图游戏$G$，它的行动规则是任选某个有向图游戏$G_i$，并在$G_i$上行动一步。$G$被称为有向图游戏$G_1, G_2, …, G_m$的和。
有向图游戏的和的$SG$函数值等于它包含的各个子游戏$SG$函数值的异或和，即：
$SG(G) = SG(G_1) \bigoplus SG(G_2) \bigoplus … \bigoplus SG(G_m)$

**定理模板**

有向图游戏的某个局面必胜，当且仅当该局面对应节点的SG函数值大于0。
有向图游戏的某个局面必败，当且仅当该局面对应节点的SG函数值等于0。

### NIM游戏

> 给定$n$堆物品，第$i$堆物品有$A_i$个。两名玩家轮流行动，每次可以任选一堆，取走任意多个物品，可把一堆取光，但不能不取。取走最后一件物品者获胜。两人都采取最优策略，问先手是否必胜。

我们把这种游戏称为NIM博弈。把游戏过程中面临的状态称为局面。整局游戏第一个行动的称为先手，第二个行动的称为后手。若在某一局面下无论采取何种行动，都会输掉游戏，则称该局面必败。
所谓采取最优策略是指，若在某一局面下存在某种行动，使得行动后对面面临必败局面，则优先采取该行动。同时，这样的局面被称为必胜。我们讨论的博弈问题一般都只考虑理想情况，即两人均无失误，都采取最优策略行动时游戏的结果。
NIM博弈不存在平局，只有先手必胜和先手必败两种情况。



定理： NIM博弈先手必胜，当且仅当 $A_1 \bigoplus A_2 \bigoplus … \bigoplus A_n != 0$ 

证明：

1. 操作到最后，每堆石子个数都是$0$的时候，$0 \bigoplus 0 \bigoplus 0 ... \bigoplus 0 = 0$，先手必败

2. $A_1 \bigoplus A_2 \bigoplus … \bigoplus A_n = x \neq 0$ 

   假设$x$的二进制表示中最高的一位1在第$k$位，那么$A_1-A_n$中必然存在一个数$A_i$，$A_i$的第$k$位二进制为1

   那么，存在$A_i \bigoplus x < A_i$，所以从第$i$堆中拿走 $A_i - ( A_i \bigoplus x)$ 个石子是合法的

   那么第$i$堆就剩下 $A_i - (A_i - ( A_i \bigoplus x)) = A_i \bigoplus x$个石子

   $A_1 \bigoplus A_2 \bigoplus ... \bigoplus A_n \bigoplus x = x \bigoplus x = 0$

3. $A_1 \bigoplus A_2 \bigoplus … \bigoplus A_n = 0$

   要证明从$A_1 - A_n$中任选一堆拿任意个，可以使得 $A_1 \bigoplus A_2 \bigoplus … \bigoplus A_n$ 的值不是0

   反证法：

   存在$(A_1 \bigoplus A_2 \bigoplus … \bigoplus A_i') \bigoplus (A_1 \bigoplus A_2 \bigoplus … \bigoplus A_i) = 0$  

   则有 $A_i' \bigoplus A_i = 0 \Rightarrow A_i' = A_i$

基于上述证明：

1. 如果先手面对的状态是$A_1 \bigoplus A_2 \bigoplus … \bigoplus A_n \neq 0$那么先手总可以通过拿走某一堆若干个石子，将局面变成$A_1 \bigoplus A_2 \bigoplus … \bigoplus A_n = 0$ .如此重复，最后一定是后手面临最终没有石子可拿的状态。先手必胜。
2. 如果先手面对的局面是$A_1 \bigoplus A_2 \bigoplus … \bigoplus A_n = 0$，那么无论先手怎么拿，都会将局面变成$A_1 \bigoplus A_2 \bigoplus … \bigoplus A_n \neq 0$，那么后手总可以通过拿走某一堆若干个石子，将局面变成$A_1 \bigoplus A_2 \bigoplus … \bigoplus A_n = 0$。如此重复，最后一定是先手面临最终没有石子可拿的状态。先手必败。

#### 台阶-NIM游戏

> 现在，有一个 $n$ 级台阶的楼梯，每级台阶上都有若干个石子，其中第 $i$ 级台阶上有 $a_i$ 个石子($i≥1$)。两位玩家轮流操作，每次操作可以从任意一级台阶上拿若干个石子放到下一级台阶中（不能不拿）。已经拿到地面上的石子不能再拿，最后无法进行操作的人视为失败。问如果两人都采用最优策略，先手是否必胜。

e.g. 石子数量为2 1 3 

先手将3台阶的1块拿到2台阶，变为 2 2 2 

然后对手从偶数k台阶拿多少到奇数k-1台阶，就从奇数k-1台阶拿多少到偶数k-2台阶，假如对手从3台阶拿多少到2，就相应的从1拿多少到地上（0台阶）



此时，我们需要将奇数台阶看作是一个的经典Nim游戏，如果先手时奇数台阶上的值的异或值为0，则先手必败，反之必胜     

先手时，如果奇数台阶异或非0，根据经典Nim游戏，先手总有一种方式使奇数台阶异或为0，于是先手留了奇数台阶异或为0的状态给后手，于是轮到后手：
①当后手移动偶数台阶上的石子时，先手只需将对手移动的石子继续移到下一个台阶，这样奇数台阶的石子相当于没变，于是留给后手的又是奇数台阶异或为0的状态
②当后手移动奇数台阶上的石子时，留给先手的奇数台阶异或非0，根据经典Nim游戏，先手总能找出一种方案使奇数台阶异或为0

因此无论后手如何移动，先手总能通过操作把奇数异或为0的情况留给后手，当奇数台阶全为0时，只留下偶数台阶上有石子。

（核心就是：先手总是把奇数台阶异或为0的状态留给对面，即总是将必败态交给对面）

因为偶数台阶上的石子要想移动到地面，必然需要经过偶数次移动，又因为奇数台阶全0的情况是留给后手的，因此先手总是可以将石子移动到地面，当将最后一个（堆）石子移动到地面时，后手无法操作，即后手失败。

因此如果先手时奇数台阶上的值的异或值为非0，则先手必胜，反之必败！

#### 集合-NIM游戏

> 给定 $n$ 堆石子以及一个由 $k$ 个不同正整数构成的数字集合 $S$。现在有两位玩家轮流操作，每次操作可以从任意一堆石子中拿取石子，每次拿取的石子数量必须包含于集合 $S$，最后无法进行操作的人视为失败。问如果两人都采用最优策略，先手是否必胜。

1. 我们可以确定对于确定的集合$S$，以及唯一确定的数字，其$SG$值也是唯一确定的

   即使在不同石子堆中，由于取石子的方式唯一，所以其终点和分支是唯一的，所以在所有石子堆中，在集合$S$确定的条件下，每个数的$SG$值都保持不变。

2. 假设取石子的集合为$\{2,5\}$，且仅有一堆石子，石子数为10

```cpp
					->	3(1)	->	1(0)
		->	5(2)	->	0(0)          
10(1)				
    	->  8(0)   -> 	3(1)	->	1(0)
                
            		->  6(1)	->	1(0)
                    			->	4(0)	->	2(1)	->	0(0)
```



我们设终点的SG值为10，当仅有一堆石子的时候，如果$SG(10) \neq 0$必胜，否则必败。

当$SG$值不为0时，因为$SG$值经过了$mex$运算，所以其连接的点中必然有一点$SG$值为0，当走到$SG$为0的这一点时，由于$mex$运算，该点连接的所有点的值必然不为0。

又因为终点的$SG$值为0，所以只要先手的$SG$值不为0，就可以一直走到$SG$值为0的点，从而最终走向终点。

3. 当有任意多堆石子的时候

假设任意每堆石子的$SG$值已知

```cpp
10(3)
15(5)
20(2)
```

每堆石子的$SG$值都是与其连接的点的$SG$值中，最小的且与连接点的值不同的最小非负整数，所以选择时都可以选择$SG$值$0-SG(x)$之间的任意数

e.g.   
$$
\because SG(10)=3\\
\therefore SG(10)可以选择连接点的SG值是[0,3)的范围
$$
那么，将每堆石子的$SG$值取出来，可以发现这些值拼接起来就可以称为NIM游戏

当$SG(x_1)\bigoplus SG(x_2) \bigoplus SG(x_3) = m\neq 0$时，可以选择$SG$值最大的那一个，如$SG(x_2)$，使得
$$
SG(x_2')=SG(x_2) \bigoplus m\\
\Rightarrow SG(x_1)\bigoplus SG(x_2')\bigoplus SG(x_3) = SG(x_1)\bigoplus SG(x_2)\bigoplus SG(x_3) \bigoplus m = m \bigoplus m=0
$$
所以，当所有石子堆的异或值不等于0的时候必胜



我们可以通过类似于递归+记忆化搜索的方式实现$SG$函数

```cpp
#include <iostream>
#include <set>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 110, M = 10010;
int minn = 10010;

int s[N], sgv[M];
int k, n, res = 0;

int sg(int x)
{
    if(sgv[x] != -1)    return sgv[x];
    if(x < minn)    return sgv[x] = 0;
    set<int> st;
    for(int i = 0; i < k; i ++)
    {
        if(x >= s[i])    st.insert(sg(x-s[i]));
    }
    for(int i = 0; ; i ++)
    {
        if(!st.count(i))    return sgv[x] = i;
    }
}

int main()
{
    scanf("%d", &k);
    for(int i = 0; i < k; i ++) scanf("%d", &s[i]), minn = min(minn, s[i]);
    scanf("%d", &n);
    memset(sgv, -1, sizeof(sgv));
    while(n --)
    {
        int x;
        scanf("%d", &x);
        res ^= sg(x);
    }
    if(res) printf("Yes\n");
    else    printf("No\n");
    return 0;
}
```

#### 拆分-NIM游戏

> 给定 $n$ 堆石子，两位玩家轮流操作，每次操作可以取走其中的一堆石子，然后放入两堆**规模更小**的石子（新堆规模可以为 $0$，且两个新堆的石子总数可以大于取走的那堆石子数），最后无法进行操作的人视为失败。问如果两人都采用最优策略，先手是否必胜。

相比于集合-Nim，这里的每一堆可以变成小于原来那堆的任意大小的两堆

即$a[i]$可以拆分成$(b[i],b[j])$,为了避免重复规定$b[i]>=b[j]$,即：$a[i]>b[i]>=b[j]$

相当于一个局面拆分成了两个局面，由$SG$函数理论，多个独立局面的$SG$值，等于这些局面$SG$值的异或和。
因此需要存储的状态就是$sg(b[i])^sg(b[j])$（与集合-Nim的唯一区别）

因为这题中原堆拆分成的两个较小堆小于原堆即可，因此任意一个较小堆的拆分情况会被完全包含在较大堆中，因此$set$可以开全局。

```cpp
#include <iostream>
#include <cstring>
#include <set>

using namespace std;

const int N = 110;
int s[N];

int sg(int x)
{
    if(s[x] != -1)  return s[x];
    set<int> st;
    for(int i = 0; i < x; i ++)
    {
        for(int j = 0; j <= i; j ++)
        {
            st.insert(sg(i) ^ sg(j));
        }
    }
    for(int i = 0; ; i ++)
    {
        if(st.count(i) == 0)     return s[x] = i;
    }
}

int main()
{
    memset(s, -1, sizeof(s));
    
    int n, res = 0;
    cin >> n;
    while(n --)
    {
        int x;
        cin >> x;
        res ^= sg(x);
    }
    if(res) cout << "Yes" << endl;
    else    cout << "No" << endl;
    return 0;
}
```

