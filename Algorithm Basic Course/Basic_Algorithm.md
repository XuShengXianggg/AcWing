# 基础算法

上课：理解算法主要思想

课后：理解代码模板并且能够快速默写  用题目检验 重复3-5次就能很好的提升熟练度

## 排序

### 快速排序

$O(nlogn)$

基于分治思想

1. 确定分界点： $q[l]$	$q[l + r >> 1]$	$q[r]$	随机

   **快速排序这道题目的数据已加强，划分中点取左端点或右端点时会超时，改成取中点或者随机值即可**

2. ==调整区间==：满足x左边的元素都小于等于x，右边的元素都大于等于x（等于x不影响），所以x不一定在中间位置

   初始情况，指针i在最左边的左边一个，指针j在最右边的右边一个的位置

   从左向右移动指针i，直到遇到第一个大于x的数，停下来；从右向左移动指针j，直到遇到第一个小于x的数停下来。

   交换此时指针i和指针j指向的数

   在继续移动指针i和j，直到i和j相遇为止

   指针j前面的数都是小于等于x的，指针i后面的数都是大于等于x的

3. 递归处理：递归的形式处理左右两段

   两个区间：$[l,j]$       $[j+1 , r]$ 

**易错点**：

1. quick_sort() 函数里面传入的是 q[] , 因此在swap内也要使用 q[]；
2. do - while 循环步进条件 要区分 q[i] 和 q[j]；
3. 递归处理的时间前面的右边界为j ， 后面的左边界为 j+1。

**快速排序算法模板**

```cpp
void quick_sort(int q[], int l, int r)
{
    if (l >= r) return;

    int i = l - 1, j = r + 1, x = q[l + r >> 1];
    while (i < j)
    {
        do i ++ ; while (q[i] < x);
        do j -- ; while (q[j] > x);
        if (i < j) swap(q[i], q[j]);
    }
    quick_sort(q, l, j), quick_sort(q, j + 1, r);
}
```

### 快速选择算法

$O(n)$

快速选择算法用于求无序数列的第$k$小数

1. 找到分界点$x$，$q[l]$，$q[r]$，$q[l+r >>1]$
2. 左边所有数$Left<=x$，右边所有数$Right>=x$
3. 统计左边数的个数$S_l$，右边数的个数$S_r$
4. 若$k\leq S_l$，则第$k$小数必然在左半边，只需要递归处理左半边$Left$即可
5. 若$k>S_l$，则第$k$小数必然在右半边，只需要递归处理右半边$Right$即可，此时第$k$小数就是右半边的第$k-S_l$小数

```cpp
int quick_sort(int l, int r, int k)
{
	if(l == r)	return q[l];
    
    int i = l - 1, j = r + 1, x = q[l + r >> 1];
    while(i < j)
    {
		do{i ++;} while(q[i] < x);
        do{j --;} while(q[j] > x);
        if(i < j)	swap(q[i], q[j]);
    }
    int sl = j - l + 1;
    if(k <= sl)	return qucik_sort(l, j, k);
    else	return quick_sort(j + 1, r, k - sl);
}
```

### 归并排序

基于分治思想

1. 确定分界点，$mid = l + r  >> 1$，（快速排序取的是数值，归并排序里面确定的是位置）

2. 递归排序左边和右边，两边就变成了一个有序的数组

3. 归并——将两个有序数组合二为一

   双指针法处理两个数列，i指向a数组的0，j指向b数组的0

   如果$a[i] < a[j]$ 将$a[i]$ 放入新的数组，$i++$ 

   如果$a[j] < a[i]$ 将$a[j]$ 放入新的数组，$j++$ 

   到最后一定是有一个数组已经全部处理完成，还有一个数组没有处理完

   将未处理完的数组全部接到新数组的后面

**易错点**：

1. 两个区间 $[l, mid]$   $[mid + 1, r]$    $i = l, j = mid + 1$
2. 最后将$tmp$放入q的时候要注意条件是 $i = l, i <= r$

**归并排序算法模板**

```cpp
void merge_sort(int q[], int l, int r)
{
    if (l >= r) return;

    int mid = l + r >> 1;
    merge_sort(q, l, mid);
    merge_sort(q, mid + 1, r);

    int k = 0, i = l, j = mid + 1;
    while (i <= mid && j <= r)
        if (q[i] <= q[j]) tmp[k ++ ] = q[i ++ ];
        else tmp[k ++ ] = q[j ++ ];

    while (i <= mid) tmp[k ++ ] = q[i ++ ];
    while (j <= r) tmp[k ++ ] = q[j ++ ];

    for (i = l, j = 0; i <= r; i ++, j ++ ) q[i] = tmp[j];
}
```

### 逆序对数量

> 逆序对：前面的数比后面的数严格大

1. 左半边的逆序对数量$merge\_sort(l, mid)$
2. 右半边的逆序对数量$merge\_sort(mid+1, r)$
3. 对于两边序列合成一段有序序列的过程中，若$q[i]>q[j]$，那么左半边$i$往后的所有数都大于$q[j]$，$S_j=mid - i +1$

```cpp
ll merge_sort(int l, int r)
{
	if(l >= r)	return 0;
    int mid = l + r >> 1;
    ll res = merge_sort(l, mid)+ merge_sort(mid + 1, r);
    
    int k = 0, i = l, j = mid + 1;
    while(i <= mid && j <=r)
    {
		if(q[i] <= q[j])	tmp[k ++] = q[i ++];
        else
        {
			tmp[k ++] = q[j ++];
            res += mid - i + 1;
        }
    }
    while (i <= mid) tmp[k ++ ] = q[i ++ ];
    while (j <= r) tmp[k ++ ] = q[j ++ ];
    
    for (i = l, j = 0; i <= r; i ++, j ++ ) q[i] = tmp[j];
    return res;
}
```

## 二分

### 整数二分

有单调性就一定可以二分，但是不具有单调性的题目也一定可以二分，二分的本质不是单调性

在区间上定义了某种性质，该性质在左半边满足，在右半边不满足，左右半边不能相交（整数二分）

二分可以寻找性质的边界，既可以选择不满足的边界，也可以选择满足的边界

==时刻保证答案在区间内部==

1. 找中间值$mid$： $mid = l + r >> 1$

2. 先写一个 $check()$ 函数，然后判断如何更新，如果是 $l = mid$ ，就要把 $mid$ 改成 $l + r >> 1$

3. 每次看更新区间是 $l = mid$ ( 补上 +1 ) 还是 $r = mid$

4. 找左区间的右边界

   1. $if( check( mid ) == true )$

      那么mid就在满足性质的区间里面 ， 那么边界答案在 $[mid, r]$ 里面 （包含$mid$）

      更新：$l = mid$

   2. $if( check( mid ) == false )$

      那么边界答案在 $[ l , mid - 1 ]$ 里面

      更新：$r = mid - 1$

5. 找右区间的左边界

   1. $if( check( mid ) == true )$

      那么边界答案在$ [ l , mid ]$

      更新：$r = mid$

   2. $if( check( mid ) == false )$

      那么边界答案在$ [ l , mid ]$

      更新：$l = mid + 1$

**注意点：**

1. 每次选择下一个答案所在的区间

2. 二分是否有解和题目有关，和二分模板无关，二分是一定有解的，只需要最后判断二分得到的答案和题目要求的答案是否相同即可



给定数列，求元素的起始位置和终止位置

判断元素的起始位置，$mid$满足条件，说明起始位置在$mid$前面，$r = mid$

判断元素的终止位置，$mid$满足条件，说明终止位置在$mid$后面，$l = mid$

**整数二分算法模板**

```cpp
bool check(int x) {/* ... */} // 检查x是否满足某种性质

// 区间[l, r]被划分成[l, mid]和[mid + 1, r]时使用：
int bsearch_1(int l, int r)
{
    while (l < r)
    {
        int mid = l + r >> 1;
        if (check(mid)) r = mid;    // check()判断mid是否满足性质
        else l = mid + 1;
    }
    return l;
}

// 区间[l, r]被划分成[l, mid - 1]和[mid, r]时使用：
int bsearch_2(int l, int r)
{
    while (l < r)
    {
        int mid = l + r + 1 >> 1;
        if (check(mid)) l = mid;
        else r = mid - 1;
    }
    return l;
}
//边界答案都存在l指向的位置
```

### 浮点数二分

四位小数用 1e-6

六位小数用 1e-8

因为是浮点数二分，不需要考虑边界问题

**浮点数二分算法模板**

```cpp
bool check(double x) {/* ... */} // 检查x是否满足某种性质

double bsearch_3(double l, double r)
{
    const double eps = 1e-6;   // eps 表示精度，取决于题目对精度的要求
    while (r - l > eps)
    {
        double mid = (l + r) / 2;
        if (check(mid)) r = mid;	
        else l = mid;
    }
    return l;
}
```

## 高精度

### 高精度加法

1. 大整数存储：将数字存到数组里，第一个位置存个位，第二个位置存十位......
2. 从$A_0+B_0$开始算起，算个位，满十进一

**易错点：**

1. 将数字存在字符串里面，然后倒序转换进数组
1. 在函数内模拟整数各数位的加法，在十位的时候加上个位满十溢出的值，最后倒序输出即可

**高精度加法算法模板：**

```cpp
// C = A + B, A >= 0, B >= 0
vector<int> add(vector<int> &A, vector<int> &B)
{
    if (A.size() < B.size()) return add(B, A);

    vector<int> C;
    int t = 0;
    for (int i = 0; i < A.size(); i ++ )
    {
        t += A[i];
        if (i < B.size()) t += B[i];
        C.push_back(t % 10);
        t /= 10;
    }

    if (t) C.push_back(t);
    return C;
}

int main()
{
    string a, b;
    cin>>a>>b;

    vector<int> A, B;
    for(int i = a.size() - 1; i >= 0; i --) A.push_back(a[i] - '0');
    for(int j = b.size() - 1; j >= 0; j --) B.push_back(b[j] - '0');

    auto C = add(A, B);

    for(int i = C.size() - 1; i >= 0; i --) printf("%d",C[i]);
    return 0;
}
```

### 压位高精

加法压9位，乘法压4位



### 高精度减法

​		A<sub>3</sub> A<sub>2</sub> A<sub>1</sub> A<sub>0</sub>

​	-   	 B<sub>2</sub> B<sub>1</sub> B<sub>0</sub> 

$A >= B$ 计算 $A - B$

$A < B$   计算 $- ( B - A )$

$t$ 表示是否借位

$A_2 - B_2 - t  >= 0$	$A_2 - B_2 - t $

$A_2 - B_2 - t   <  0$	$A_2 - B_2 + 10 - t $

**注意点：**

1. 在$cmp$函数中最后要记得的返回true，因为判断的是是否 $A >= B$
2. 不要忘记去除答案的前导0

**高精度减法算法模板：**

```cpp
//判断A>=B
bool cmp(vector<int> &A, vector<int> &B)
{
	if(A.size() != B.size())	return A.size() > B.size();
    for(int i = A.size() - 1; i >= 0; i --)
    {
        if(A[i] != B[i])	return A[i] > B[i];
    }
    return true;
}

// C = A - B, 满足A >= B, A >= 0, B >= 0
vector<int> sub(vector<int> &A, vector<int> &B)
{
    vector<int> C;
    int t = 0;
    for (int i = 0; i < A.size(); i ++ )
    {
        t = A[i] - t;
        if (i < B.size()) t -= B[i];
        C.push_back((t + 10) % 10);
        if (t < 0) t = 1;
        else t = 0;
    }

    while (C.size() > 1 && C.back() == 0) C.pop_back();	//去除前导0
    return C;
}

int main()
{
    string a, b;
    cin>>a>>b;

    vector<int> A, B;
    for(int i = a.size() - 1; i >= 0; i --) A.push_back(a[i] - '0');
    for(int j = b.size() - 1; j >= 0; j --) B.push_back(b[j] - '0');
	
    if(cmp(A, B))
    {
        auto C = sub(A, B);
        for(int i = C.size() - 1; i >= 0; i --) printf("%d",C[i]);
    }
    else
    {
        auto C = sub(B, A);
        printf("-");
        for(int i = C.size() - 1; i >= 0; i --) printf("%d",C[i]);
    }
    return 0;
}
```

### 高精度乘法

123 * 12

3 * 12 = 36  36 %10 = 6   36 / 10 = 3

2 * 12 + 3 = 27  27 % 10 = 7    27 / 10 = 2

1 * 12 + 2 = 14  14 % 10 = 4    14 / 10 = 1

0 * 12 + 1 = 1   1 % 10 = 1

1476



A<sub>5</sub> A<sub>4</sub> A<sub>3</sub> A<sub>2</sub> A<sub>1</sub> A<sub>0</sub> 

\*                        b

进位：t

个位：A<sub>0</sub> * b % 10      t = A<sub>0</sub> * b / 10

十位：(A<sub>1</sub> * b + t ) % 10      t = (A<sub>1</sub> * b + t ) / 10

**注意点：**

1. 注意一下进位的t，到最后可能已经运算完了但是t里面还存着首位的1，所以可以在最后加上去

   所以也可以在循环里面不考虑t，在循环结束后加上  if(t)   C.push_back(t);  即可

**高精度乘低精度算法模板：**

```cpp
// C = A * b, A >= 0, b >= 0
vector<int> mul(vector<int> &A, int b)
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
```

### 高精度除法

1234 ➗ 11

r = 0 * 10 + 1 = 1        1 / 11 = 0     r = 1 % 11 = 1

r = 1 * 10 + 2 = 12    12 / 11 = 1    r = 12 % 11 = 1

r = 1 * 10 + 3 = 13    13 / 11 = 1    r = 13 % 11 = 2

r = 2 * 10 + 4 =  24    24 / 11 = 2   r = 14 % 11 = 2

r = 0 * 10 + 2 = 2         2 / 11 = 0    r= 2 % 11 = 2

1234 ➗ 11 = 112 ...... 2     



A<sub>3</sub> A<sub>2</sub> A<sub>1</sub> A<sub>0</sub>  ➗ b

r = 0 * 10 + A<sub>1</sub>   r / b = C<sub>3</sub>       r =  r % b 

r = r * 10 + A<sub>2</sub>    r / b = C<sub>2</sub>    r  = r  %  b

**注意点：**

1. 注意要reverse，以及头文件algorithm，这里是顺序求出答案存入数组的

**高精度除以低精度算法模板：**

```cpp
// A / b = C ... r, A >= 0, b > 0
vector<int> div(vector<int> &A, int b, int &r)
{
    vector<int> C;
    r = 0;
    for (int i = A.size() - 1; i >= 0; i -- )
    {
        r = r * 10 + A[i];
        C.push_back(r / b);
        r %= b;
    }
    reverse(C.begin(), C.end());
    while (C.size() > 1 && C.back() == 0) C.pop_back();
    return C;
}
```

## 前缀和与差分

### 一维前缀和

$S[i] = a[1] + a[2] + ... a[i]$

==建议从a[1]开始存元素==

**预处理O(n)  询问O(1)**

1. 如何求$S[i]$ 

```cpp
S[0] = 0;
for(int i = 1; i <= n; i ++)
    S[i] = S[i - 1] + a[i];
```

2. 作用：可以快速求出原数组中一段数$[l,r]$的和，

```cpp
a[l] + ... + a[r] = S[r] - S[l - 1];
```

**一维前缀和算法模板：**

```cpp
S[i] = a[1] + a[2] + ... a[i]
a[l] + ... + a[r] = S[r] - S[l - 1]
```

### 二维前缀和

$a[i][j]$ 表示元素

$S[i][j]$ 表示左上角$i$行$j$列矩形里面所有元素的和

a b c

d e f

g h i

$a[2][2]-a[3][3]:s[3][3]-s[1][3]-s[3][1]+s[1][1]$

那么要求以  $a[x_1][y_1]$和  $a[x_2][y_2]$ 为顶点的矩形里面的元素的和，就是 $s[x_2][y_2]-s[x_1-1][y_2]-s[x_2][y_1-1]+s[x_1-1][y_1-1]$ 

（容斥原理）

```cpp
for(int i = 1; i <= n; i ++)
{
    for(int j = 1; j <= m; j ++)
    {	//预处理
        s[i][j] = s[i - 1][j] + s[i][j - 1] - s[i - 1][j - 1] + a[i][j];
    }
}
//询问
s[x2][y2] - s[x1 - 1][y2] - s[x2][y1 - 1] + s[x1 - 1][y1 - 1];
```

**二维前缀和算法模板：**

```cpp
S[i, j] = 第i行j列格子左上部分所有元素的和
以(x1, y1)为左上角，(x2, y2)为右下角的子矩阵的和为：
S[x2, y2] - S[x1 - 1, y2] - S[x2, y1 - 1] + S[x1 - 1, y1 - 1]
```

### 一维差分

差分是前缀和的逆运算

由a[1]  a[2]   a[3]  构造 b[1]   b[2]   b[3] 数组，使得a[i] = b[1] + ... + b[i]， 也就是说 a[] 是 b[] 的前缀和

所以 b[1] = a[1]                   b[2] = a[2] - a[1]       b[i] = a[i] - a[i - 1]



要给 [l , r] 之间的元素都加上c         a[l] + c , a[ l + 1] + c ,, O(n)

差分：O(n)  构造差分数组，更改b[]，再求一遍前缀和，就可以得到a[]

差分O(n)  修改O(1)

a[] 是 b[] 的前缀和     将b[l] + c之后，a[l] 往后也会加上c，但是将a[r + 1] - c ， 就可以取消对于后面的元素的影响了



```cpp
int q[maxn], b[maxn];
for(int i = 1; i <= n; i ++)    scanf("%d", &q[i]);
    
for(int i = 1; i <= n; i ++)    b[i] = q[i] - q[i - 1];		//求差分数组

scanf("%d%d%d", &l, &r, &c);
b[l] += c;
b[r + 1] -= c;
//进行增加操作

for(int i = 1; i <= n; i ++)    q[i] = b[i] + q[i - 1]; 	//前缀和求原数组
```

**一维差分算法模板：**

```cpp
给区间[l, r]中的每个数加上c：B[l] += c, B[r + 1] -= c
```

### 二维差分

原矩阵 $a[i][j]$

构造差分矩阵 $b[i][j]$ 

```cpp
void insert(int x1, int y1, int x2, int y2, int c)
{
    b[x1][y1] += c;
    b[x2 + 1][y1] -= c;
    b[x1][y2 + 1] -= c;
    b[x2 + 1][y2 + 1] += c;
}
//构建差分数组
for(int i = 1; i <= n; i ++)
{
    for(int j = 1; j <= m; j ++)
    {
        insert(i, j, i, j, q[i][j]);
    }
}
//将元素加上c
int x1, y1, x2, y2, c;
scanf("%d%d%d%d%d", &x1, &y1, &x2, &y2, &c);
insert(x1, y1, x2, y2, c);
//求二维前缀和，还原为原数组
for(int i = 1; i <= n; i ++)
{
    for(int j = 1; j <= m; j ++)
    {	
        q[i][j] = q[i - 1][j] + q[i][j - 1] - q[i - 1][j - 1] + b[i][j];
    }
}
```

**二维差分算法模板：**

```cpp
给以(x1, y1)为左上角，(x2, y2)为右下角的子矩阵中的所有元素加上c：
S[x1, y1] += c, S[x2 + 1, y1] -= c, S[x1, y2 + 1] -= c, S[x2 + 1, y2 + 1] += c
```

## 双指针算法

可以是两个指针分别指向两个序列，也可以是两个指针指向一个序列，维护一段区间

核心思想：将 $O(n^2)$ 优化到 $O(n)$ 

本质上就是通过找到**单调性**进行优化

**双指针算法算法模板：**

```cpp
for (int i = 0, j = 0; i < n; i ++ )
{
    while (j < i && check(i, j)) j ++ ;

    // 具体问题的逻辑
}
常见问题分类：
    (1) 对于一个序列，用两个指针维护一段区间
    (2) 对于两个序列，维护某种次序，比如归并排序中合并两个有序序列的操作
```

### 最长连续不重复子序列

> 最长的不包含重复数字的连续子序列的长度

1 2 2 3 5	->  2 3 5       len = 3

暴力做法：先枚举终点，再枚举起点(j为起点，i为终点)

```cpp
for(int i = 0; i < n; i ++)
{
    for(int j = 0; j <= i; j ++)
    {
        if(check(j, i))
        {
            res = max(res, i - j + 1);
        }
    }
}
```

优化做法：

本质上还是枚举每一个i，看左边的j离他最远的话是在哪个位置

我们现在只需要枚举每一个i，然后判断j需不需要向后移动

```cpp
//双指针算法
for(int i = 0; i < n; i ++)
{
    while(j <= i && check(j, i))	j ++;
    
    res = max(res, i - j + 1);
}
```

1. 遍历数组$a$中的每一个元素$a[i]$, 对于每一个$i$，找到$j$使得双指针$[j, i]$维护的是以$a[i]$结尾的最长连续不重复子序列，长度为$i - j + 1$, 将这一长度与$res$的较大者更新给$res$。
2. 对于每一个$i$，如何确定$j$的位置：由于$[j, i - 1]$是前一步得到的最长连续不重复子序列，所以如果$[j, i]$中有重复元素，一定是$a[i]$，因此右移$j$直到$a[i]$不重复为止（由于$[j, i - 1]$已经是前一步的最优解，此时$j$只可能右移以剔除重复元素$a[i]$，不可能左移增加元素，因此，$j$具有“单调性”、本题可用双指针降低复杂度。
3. 用数组$s$记录子序列$a[j - i]$中各元素出现次数，遍历过程中对于每一个$i$有四步操作：输入$a[i]$ -> 将$a[i]$出现次数$s[a[i]]$加1 -> 若$a[i]$重复则右移$j$（$s[a[j]]$要减1） -> 确定$j$及更新当前长度$i - j + 1$给$r$。

## 位运算

求n的二进制表示中第k位为几

n = 15 = (1111)<sub>2</sub>   

1. 先把第k位移到最后一位 n >> k
2. 看个位是几 x & 1



lowbit：返回x的最后一位1

x= (1010)<sub>2</sub> 	lowbit(x) = (10)<sub>2</sub> 

x = (101000)<sub>2</sub>   lowbit(x) = (1000)<sub>2</sub> 

lowbit(n) = n & -n = n & (~ x + 1)

可以用于**求n里面1的个数**

```cpp
int lowbit(int x)
{
    return x & -x;
}


int res = 0;
while(x)	x -= lowbit(x), res ++;		//每次减去x的最后一位1
cout<<res;

// 或者
int cnt = 0;
while(x)
{
    if(x & 1)   cnt ++;
    x >>= 1;
}
```

**位运算算法模板：**

```cpp
求n的二进制表示中第k位数字: n >> k & 1
返回n的最后一位1：lowbit(n) = n & -n
```

## 离散化

整数的离散化

一个数列，值很大，但是数量不多（数据范围在\[0, 10<sup>9</sup>\] ，数量在\[0, 10<sup>5</sup>] ）

a[] : 1 , 3 , 100, 2000, 500000 这几个下标的元素有用，其他都没有用

b[] : 0 , 1 , 2 , 3 , 4

问题：

1. a中可能有重复元素，需要==去重==
2. 如何算出a[i]离散化后的值是多少（二分）



需要先将所有要进行操作的数存进数组，在进行离散化

**值域跨度很大，但是非常稀疏**

将所有会用到的下标离散化映射到一个稠密数组内，在稠密数组内进行前缀和，考虑的是相对关系

**注意点：**

1. ==一定要注意映射到0开始还是1开始的序列！！！==

**离散化算法模板：**

```cpp
vector<int> alls; // 存储所有待离散化的值

sort(alls.begin(), alls.end()); // 将所有值排序
alls.erase(unique(alls.begin(), alls.end()), alls.end());   // 去掉重复元素

// 二分求出x对应的离散化的值
int find(int x) // 找到第一个大于等于x的位置
{
    int l = 0, r = alls.size() - 1;
    while (l < r)
    {
        int mid = l + r >> 1;
        if (alls[mid] >= x) r = mid;
        else l = mid + 1;
    }
    return r + 1; // 映射到1, 2, ...n
}
```

### 区间和

> 假定有一个无限长的数轴，数轴上每个坐标上的数都是 0。
>
> 首先进行 n 次操作，每次操作将某一位置 x 上的数加 c。
>
> 接下来，进行 m次询问，每个询问包含两个整数 l 和 r，你需要求出在区间\[l,r\] 之间的所有数的和。

```cpp
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

const int N = 1e5 + 10;
typedef pair<int, int> PII;

int q[N * 3], s[N * 3];

vector<PII> add, query;
vector<int> alls;

int find(int x)
{
    int l = 0, r = alls.size() - 1;
    while(l < r)
    {
        int mid = l + r >> 1;
        if(alls[mid] >= x)  r = mid;
        else    l = mid + 1;
    }
    return r;
}

int main()
{
    int n, m, x, c, l, r;
    scanf("%d%d", &n, &m);
    for(int i = 0; i < n; i ++)
    {
        scanf("%d%d", &x, &c);
        add.push_back({x, c});
        alls.push_back(x);
    }
    for(int i = 0; i < m; i ++)
    {
        scanf("%d%d", &l, &r);
        query.push_back({l, r});
        alls.push_back(l), alls.push_back(r);
    }
    sort(alls.begin(), alls.end());
    alls.erase(unique(alls.begin(), alls.end()), alls.end());
    for(auto p : add)
    {
        q[find(p.first)] += p.second;
    }
    s[0] = q[0];						// 求前缀和
    for(int i = 1; i < alls.size(); i ++)
    {
        s[i] = s[i - 1] + q[i];
    }
    for(auto p : query)					// 查询操作
    {
        printf("%d\n", s[find(p.second)] - s[find(p.first) - 1]);
    }
    return 0;
}
```

## unique函数

对于一个有序数列，如何判断不是重复的

1. 是第一个元素
2. 和前一个元素不一样, a[i] != a[i - 1]

```cpp
vector<int>::iterator unique(vector<int> &a)
{
    int j = 0;
    for(int i = 0; i < a.size(); i ++)
        if(!i || a[i] != a[i - 1])
            a[j ++] = a[i];
    //a[0] ~ a[j - 1]中所有不重复的数
    return a.begin() + j;
}
```

## 区间合并

给很多区间，假如两个区间有交集，就合并为一个区间

1. 按照所有区间的左端点排序

2. 从左到右扫描所有的区间，再扫描的过程中进行处理

3. 假如下一个起始点和终止点在当前区间内，就不变

   假如下一个起始点在当前区间内，但是终止点在当前区间外，就更新当前区间的右端点

   假如下一个起始点和终止点都在当前区间外，就将当前区间存入结果，继续扫描下一区间

4. 或者进一步简化一下

   假如下一起始点在当前区间内，就让当前区间的右端点变成 当前右端点和下一终止点的较大值

   假如下一起始点在当前区间外，就将当前区间存入答案并继续扫描下一区间

**区间合并算法模板：**

```cpp
// 将所有存在交集的区间合并
void merge(vector<PII> &segs)
{
    vector<PII> res;
    
	//首先对原数组进行排序
    //pair是先对第一关键词排序，再对第二关键词排序
    sort(segs.begin(), segs.end());
	
    //先初始化区间为负无穷
    int st = -2e9, ed = -2e9;
    
    //遍历每一个区间，如果这个区间的右端点在下一个区间的起始点前面，就将该区间保存
    //然后更新当前区间
    //假如这个区间的右端点在下一区间的后面，就比较当前右端点和下一区间的右端点，取较大值作为区间右端点
    for (auto seg : segs)
        if (ed < seg.first)
        {
            if (st != -2e9) res.push_back({st, ed});
            st = seg.first, ed = seg.second;
        }
        else ed = max(ed, seg.second);

    if (st != -2e9) res.push_back({st, ed});

    segs = res;
}
```

