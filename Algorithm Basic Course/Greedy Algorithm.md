# 贪心

## Lambda函数

```cpp
// OuterVar为捕获变量 x, y为参数列表 int表示返回值 
[OuterVar](int x, int y) -> int{
    return OuterVar + x + y;
};
```

```cpp
// 编译器可以自动推断返回值类型
auto f = [](int x, int y) -> int{
    return x + y;
};

auto f = [](int x, int t){
    return x + y;
};
// 二者等价
cout << f(1, 2) << endl;
```

```cpp
// 变量捕获 让匿名函数可以访问以及修改函数外部的变量
// [] 空方括号表示不捕获任何变量
// [&M] 变量名前面有引用符号，则是按引用捕获，可以修改外围变量的值
// [N] 不加引用符号就是按值捕获
int N = 100, M = 10;

auto g = [N, &N](int i){
    M = 20;
    return N * i;
};

cout << g(10) << endl;	// 1000
cout << M << endl;	// 20

// [&] 方括号中若只写引用符号，则按引用捕获所有封闭范围中的变量
// [=] 方括号中若只写等于号，则按值捕获所有变量
// [&, =N] 可以指定一些变量按值捕获，其他变量都按引用捕获

// 还可以在变量捕获中定义新的变量并初始化，这些变量无需出现在匿名函数外围环境中
auto g = [N, &N, K = 5](int i){
    M = 20;
    cout << K << endl;
    return N * i;
};

// 参数列表中也支持auto推断变量类型
[](auto a, auto b){
    return a + b;
};
```

```cpp
struct Sg
{
    int l, r;
}sg[N];

sort(sg, sg + n, [&](Sg a, Sg b) { 
    return a.r < b.r; 
});

auto f = [](int x, int y){
    return x + y;
}(2, 3);
cout << f << endl;

auto f = [](int x, int y){
    return x + y;
};
cout << f(2, 3) << endl;
```

## 泛编程

### 自动推导

```cpp
auto d = 3;
```

1. auto声明的变量必须在定义时初始化
2. 初始化的右值可以是具体的数值，也可以是表达式和函数的返回值等
3. auto不能作为函数的形参类型
4. auto不能直接声明数组
5. auto不能定义类的非静态成员变量

### 函数模板

编译的时候，编译器推导实参的数据类型，根据实参的数据类型和函数模板，生成该类型的函数定义。

```cpp
template <typename T>
void Swap(T &a, T &b)
{
	T tmp = a;
	a = b;
	b = tmp;
}

int a = 10, b = 30;
Swap(a, b);
Swap<int>(a, b);
```

## 区间问题

区间问题，可以考虑按照左端点、右端点或者双关键字排序

### 区间选点

> 给定 $N$ 个闭区间 $[a_i,b_i]$，请你在数轴上选择尽量少的点，使得每个区间内至少包含一个选出的点。输出选择的点的最小数量。位于区间端点上的点也算作区间内。

<img src="https://cdn.acwing.com/media/article/image/2020/10/25/652_632882e016-image_19.png" alt="image_19.png" style="zoom:67%;" />

1. 将每个区间按照右端点从小到大排序（对于一个已经排好序的区间，肯定点选在越靠右越好）
2. 从前往后依次枚举每一个区间
   * 若当前区间可以覆盖掉上次区间的右端点，则进行下一轮循环
   * 如果本次区间不能覆盖掉上次区间的右端点，选择当前区间的右端点

证明：

$Ans$表示最小值，$cnt$表示一种可行方案点的数量，那么$Ans \leq cnt$

下一个选择点的区间一定和上一个选择点的区间没有交集，并且在上一个选择点的区间的右边，那么可以认为选择点的区间从左到右是没有任何交集的，那么要覆盖这$cnt$个区间的话就至少有$cnt$个点，所以有$Ans \geq cnt$

就有$Ans = cnt$ 

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1e5 + 10;

int n;

// 重载运算符
struct Range{
    int l, r;
    bool operator< (const Range &b) const
    {
        return r < b.r;    
    }
}range[N];

// lambda排序
// sort(range, range + n, [](struct Range a, struct Range b){
//     return a.r < b.r;
// });

int main()
{
    scanf("%d", &n);
    for(int i = 0; i < n; i ++)
        scanf("%d%d", &range[i].l, &range[i].r);
    
    // 结构体赋值
    // int l, r;
    // scanf("%d%d", &l, &r);
    // range[i] = {l, r};
    
    sort(range, range + n);
    
    int res = 0, ed = -0x3f3f3f3f;
    for(int i = 0; i < n; i ++)
    {
        if(range[i].l > ed)		// 如果本次区间在上一次区间右端点的右边，就无法覆盖
        {
            res ++;
            ed = range[i].r;	// 需要增加新的点，并更新右端点值
        }
    }
    printf("%d\n", res);
    return 0;
}
```

### 最大不相交区间数量

> 给定 $N$ 个闭区间 $[a_i,b_i]$，请你在数轴上选择若干区间，使得选中的区间之间互不相交（包括端点）。输出可选取区间的最大数量。

最大不相交区间数==最少覆盖区间点数

如果几个区间能被同一个点覆盖，说明他们相交了，所以有几个覆盖区间的点就是有几个不相交区间

1. 将每个区间按照右端点从小到大排序
2. 从前往后依次枚举每一个区间
   * 若当前区间中已经包含点，则直接continue
   * 否则选择当前区间的右端点

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1e5 + 10;

int n;
struct Range{
    int l, r;
}range[N];

int main()
{
    scanf("%d", &n);
    for(int i = 0; i < n; i ++) scanf("%d%d", &range[i].l, &range[i].r);
    
    sort(range, range + n, [](Range a, Range b){
        return a.r < b.r;
    });		// 按照区间右端点排序
    
    int res = 0, ed = -0x3f3f3f3f;
    for(int i = 0; i < n; i ++)
        if(range[i].l > ed)		// 如果当前区间的左端点大于之前区间的右端点，则不相交，更新当前区间的右端点为ed
        {
            ed = range[i].r;
            res ++;
        }
    printf("%d\n", res);
    return 0;
}
```

### 区间分组

> 给定 $N$ 个闭区间 $[a_i,b_i]$，请你将这些区间分成若干组，使得每组内部的区间两两之间（包括端点）没有交集，并使得组数尽可能小。输出最小组数。

有若干个活动，第$i$个活动开始时间和结束时间是$[a_i,b_i]$，同一个教室安排的活动之间不能交叠，求要安排所有活动，至少需要几个教室？

有时间冲突的活动不能安排在同一间教室，与该问题的限制条件相同，即最小需要的教室个数即为该题答案。

如果该时段现有的教室都满了，就开一个新的教室举办活动，如果有空教室，就使用并更新教室的占用时间

1. 将所有区间按照左端点从小到大排序
2. 从前往后处理每个区间，判断能否将其放到某个现有的组中，实际上就是寻找一个组满足` Max_r < L[i]`
   * 不存在这样的组，则开一个新的组将这个区间放入
   * 存在这样的组，就将该区间放入组中并更新当前组的`Max_r`

每次寻找结束时间最早的教室，可以使用堆来存储结束时间，因为区间端点不能交叠，所以即使开始时间和上衣互动结束时间相等也不能使用教室

```cpp
#include <iostream>
#include <algorithm>
#include <queue>

using namespace std;

const int N = 1e5 + 10;

int n;
struct Range{
    int l, r;
}range[N];

int main()
{
    scanf("%d", &n);
    for(int i = 0; i < n; i ++) scanf("%d%d", &range[i].l, &range[i].r);
    sort(range, range + n, [](Range a, Range b){
        return a.l < b.l;
    });
    priority_queue<int, vector<int>, greater<int> > heap;
    for(int i = 0; i < n; i ++)
    {
        if(heap.empty() || range[i].l <= heap.top())
            heap.push(range[i].r);		// 如果堆内没有元素，或者当前活动开始时间小于等于教室的最早结束时间，开一间新教室
        else if(range[i].l > heap.top())
        {								// 如果当前活动开始时间比教室最早结束时间早，就可以使用那间教室，并更新那间教室的结束时间
            heap.pop();
            heap.push(range[i].r);
        }    
    }
    printf("%d\n", heap.size());
    return 0;
}
```

### 区间覆盖

> 给定 $N$ 个闭区间 $[a_i,b_i]$ 以及一个线段区间 $[s,t]$，请你选择尽量少的区间，将指定线段区间完全覆盖。输出最少区间数，如果无法完全覆盖则输出 $−1$。

<img src="https://cdn.acwing.com/media/article/image/2020/10/26/652_1a48e6a417-QQ%E5%9B%BE%E7%89%8720201026093918.jpg" alt="QQ图片20201026093918.jpg" style="zoom: 33%;" />

1. 将所有区间按左端点从小到大排序
2. 从前往后依次枚举每个区间，在所有能够覆盖$start$的区间中，选择右端点最大的区间，选完后将$start$更新为右端点的最大值

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 1e5 + 10;

int n, st, ed;
struct Range{
    int l, r;
}range[N];

int main()
{
    scanf("%d%d%d", &st, &ed, &n);
    for(int i = 0; i < n; i ++) scanf("%d%d", &range[i].l, &range[i].r);
    sort(range, range + n, [](Range a, Range b){
        return a.l < b.l;
    });
    int res = 0;
    bool flag = false;
    for(int i = 0; i < n; i ++)
    {
        int j = i, r = -0x3f3f3f3f;
        while(j < n && range[j].l <= st)        // 找到左端点在起点左边，右端点尽可能大的区间
        {
            r = max(range[j].r, r);
            j ++;
        }
        if(r < st)  break;      // 如果最大的右端点还是小于起点，则无法覆盖
        res ++;                 // 如果可以覆盖，区间数+1
        if(r >= ed)
        {
            flag = true;        // 若右端点大于等于终点，就已经全部覆盖了
            break;
        }
        st = r;                 // 将当前右端点赋值给起点
        i = j - 1;              // 由于j之前的区间左端点都小于之前的st，右端点也小于当前st，因此可以将j-1赋值给i(前面最后一次j++)
    }
    if(flag == true)    printf("%d\n", res);
    else    printf("-1\n");
    return 0;
}
```

## Huffman树

给定$N$个权值作为$N$个叶子结点，构造一棵二叉树，若该树的带权路径长度达到最小，称这样的二叉树为最优二叉树，也称为哈夫曼树(Huffman Tree)。哈夫曼树是带权路径长度最短的树，权值较大的结点离根较近。

* 路径和路径长度

  在一棵树中，从一个结点往下可以达到的孩子或孙子结点之间的通路，称为路径。

  通路中分支的数目称为路径长度。若规定根结点的层数为$1$，则从根结点到第$L$层结点的路径长度为$L-1$。

  <img src="https://pic1.zhimg.com/80/v2-f07e3b68c7798559e664eeb6cf20ba3c_1440w.webp" alt="img" style="zoom:50%;" />

* 节点的权和带权路径长度

  若将树中结点赋给一个有着某种含义的数值，则这个数值称为该结点的权。结点的带权路径长度为：从根结点到该结点之间的路径长度与该结点的权的乘积。

  <img src="https://pic2.zhimg.com/80/v2-561bb507a8adcbbed29281abac1d3485_1440w.webp" alt="img" style="zoom: 67%;" />

* 树的带权路径长度

  树的带权路径长度规定为所有叶子结点的带权路径长度之和，记为`WPL`。

  如上图，数的带权路径长度为：`WPL = (2+3) * 3 + 4 * 2 + 6 * 1 = 29`

* 哈夫曼树的构造

  假设有$n$个权值，则构造出的哈夫曼树有$n$个叶子结点。 $n$个权值分别设为 $w_1、w_2、…、w_n$，则哈夫曼树的构造规则为：

  (1) 将$w_1、w_2、…，w_n$看成是有$n$ 棵树的森林(每棵树仅有一个结点)；

  (2) 在森林中选出两个根结点的权值最小的树合并，作为一棵新树的左、右子树，且新树的根结点权值为其左、右子树根结点权值之和；

  (3)从森林中删除选取的两棵树，并将新树加入森林；

  (4)重复(2)、(3)步，直到森林中只剩一棵树为止，该树即为所求得的哈夫曼树。

  例如：对 2，3，4，6 这四个数进行构造

  ![img](https://pic3.zhimg.com/80/v2-5c5bdbcdbfbacf4565bb5e99445b964a_1440w.webp)

### 合并果子

> 每一次合并可以把两堆果子合并到一起，消耗的体力等于两堆果子的重量之和。所有的果子经过 $n−1$ 次合并之后，就只剩下一堆了。在合并果子时总共消耗的体力等于每次合并所耗体力之和。求最小的体力耗费值。

经典哈夫曼树的模型，每次合并重量最小的两堆果子即可。

使用小根堆维护所有果子，每次弹出堆顶的两堆果子，并将其合并，合并之后将两堆重量之和再次插入小根堆中。

每次操作会将果子的堆数减一，一共操作 $n−1$次即可将所有果子合并成$1$堆。每次操作涉及到2次堆的删除操作和1次堆的插入操作，计算量是 $O(logn)$。因此总时间复杂度是 $O(nlogn)$。

```cpp
#include <iostream>
#include <queue>

using namespace std;

int n, x;
priority_queue<int, vector<int>, greater<int> > heap;

int main()
{
    scanf("%d", &n);
    for(int i = 0; i < n; i ++)
    {
        scanf("%d", &x);
        heap.push(x);
    }
    int res = 0;
    while(heap.size() > 1)
    {
        int a = heap.top(); heap.pop();
        int b = heap.top(); heap.pop();
        res += (a + b);
        heap.push(a + b);
    }
    printf("%d\n", res);
    return 0;
}
```

## 排序不等式

### 排队打水

> 有 $n$ 个人排队到 $1$ 个水龙头处打水，第 $i$ 个人装满水桶所需的时间是 $t_i$，请问如何安排他们的打水顺序才能使所有人的等待时间之和最小？输出最小的等待时间之和。

$$
总时间=t_1\times(n-1)+t_2\times(n-2)+...+t_n\times0
$$

按照从小到大的顺序排队，总时间最小

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

typedef long long ll;
const int N = 1e5 + 10;

int n;
int a[N];

int main()
{
    scanf("%d", &n);
    for(int i = 0; i < n; i ++) scanf("%d", &a[i]);
    sort(a, a + n);
    ll res = 0;
    for(int i = 0; i < n; i ++) res += (a[i] * (n - i - 1));
    printf("%lld\n", res);
    return 0;
}
```

## 绝对值不等式

### 货仓选址

> 在一条数轴上有 $N$ 家商店，它们的坐标分别为 $A_1∼A_N$。现在需要在数轴上建立一家货仓，每天清晨，从货仓到每家商店都要运送一车商品。为了提高效率，求把货仓建在何处，可以使得货仓到每家商店的距离之和最小。

$$
||a|-|b|| ≤|a±b|≤|a|+|b|\\
当b>a时,|a-x|+|b-x|\geq b-a(当且仅当x在[a,b]之间取等号)\\
求min f(x)\\
f(x) = |x_1-x|+|x_2-x|+...+|x_n-x|\\
=(|x_1-x|+|x_n-x|)+(|x_2-x|+|x_{n-1}-x|)+...\\
\geq (x_n-x_1) + (x_{n-1}-x_2) + ...\\
奇数就是中位数，偶数就是中间两个数之间的任意一个数\\
0,1,2,3 \rightarrow 1或2,3/2=1\\
0,1,2 \rightarrow 1,2/2=1\\
\therefore 中位数=n/2\\
$$

```cpp
#include <iostream>
#include <algorithm>
#include <cmath>

using namespace std;

typedef long long ll;
const int N = 1e5 + 10;

int n, a[N];

int main()
{
    scanf("%d", &n);
    for(int i = 0; i < n; i ++) scanf("%d", &a[i]);
    sort(a, a + n);
    ll res = 0;
    for(int i = 0; i < n; i ++) res += fabs(a[i] - a[n >> 1]);
    printf("%d\n", res);
    return 0;
}
```

## 推公式

### 耍杂技的牛

> 奶牛们站在彼此的身上，形成一个高高的垂直堆叠。这 $N$ 头奶牛中的每一头都有着自己的重量 $W_i$ 以及自己的强壮程度 $S_i$。一头牛支撑不住的可能性取决于它头上所有牛的总重量（不包括它自己）减去它的身体强壮程度的值，现在称该数值为风险值，风险值越大，这只牛撑不住的可能性越高。确定奶牛的排序，使得所有奶牛的风险值中的最大值尽可能的小，输出最大风险值的最小可能值。

每头牛的危险值 = 前面牛的重量值和 - 自身的强壮值，要使每头牛的危险值最小，这显然是与w 和 s同时相关

按照$W_i+S_i$​从小到大的顺序排序，最大的危险系数一定是最小的，

又重又强壮的肯定在下面，同时衡量重和强壮的指标就是两者之和

$w_i+s_i<w_{i+1}+s_{i+1}$

| 牛    | 交换前                         | 交换后                                 |
| ----- | ------------------------------ | -------------------------------------- |
| $i$   | $\Sigma_{j=1}^{i-1}w_j-s_i $   | $\Sigma_{j=1}^{i-1}w_j-s_{i+1} $       |
| $i+1$ | $\Sigma_{j=1}^{i}w_j-s_{i+1} $ | $\Sigma_{j=1}^{i-1}w_j+w_{i+1}-s_{i} $ |

| 牛    | 交换前         | 交换后           |
| ----- | -------------- | ---------------- |
| $i$   | $-s_i $        | $-s_{i+1} $      |
| $i+1$ | $w_i-s_{i+1} $ | $w_{i+1}-s_{i} $ |

$$
要使得最大的危险系数最小，显然有\\
w_{i+1}-s_{i} > -s_i \\
w_i-s_{i+1} > -s_{i+1}\\
又\because w_i+s_i < w_{i+1}+s_{i+1}\\
\therefore w_i-s_{i+1}  < w_{i+1}-s_{i}\\
\therefore w_{i+1}-s_{i} > w_i-s_{i+1} \quad \&\& \quad w_{i+1}-s_{i} >-s_i \\
交换后最大的危险系数变大\\
\because 按照w_i+s_i从小到大排序可以使得最大危险值最小
$$

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 50010;

int n;
struct Cow{
    int w, s;
}cow[N];

int main()
{
    scanf("%d", &n);
    for(int i = 0; i < n; i ++) scanf("%d%d", &cow[i].w, &cow[i].s);
    sort(cow, cow + n, [](Cow a, Cow b){
        return a.w + a.s < b.w + b.s;
    });
    int res = -0x3f3f3f3f, sum = 0;
    for(int i = 0; i < n; i ++)
    {
        res = max(res, sum - cow[i].s);
        sum += cow[i].w;
    }
    printf("%d\n", res);
    return 0;
}
```

