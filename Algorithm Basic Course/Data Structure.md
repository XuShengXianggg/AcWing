# 数据结构

基于数组模拟

## 进制转换

### R进制到十进制的转换

**按权展开求和**

![img](https://img-blog.csdnimg.cn/img_convert/6350ba23a2a44d83f5eb3500300a2d04.jpeg)

![img](https://img-blog.csdnimg.cn/img_convert/fcdc30e16522164c8d95dd31ec0d3e00.jpeg)![img](https://img-blog.csdnimg.cn/img_convert/70baa8830dff2531955d4458bcec57b8.jpeg)

```cpp
// 二进制转十进制
double TwotoTen(string &s)
{
    int len = s.length(), t;
    for(t = 0; t < len; t ++)
        if(s[t] == '.')     break;
    cout << t << endl;
    double res;
    for(int i = 0; i < t; i ++)
        if(s[i] == '1')     res += pow(2, t - 1 - i);
    for(int i = t + 1; i < len; i ++)
        if(s[i] == '1')     res += pow(2, t - i);
    return res;
}
```

### 十进制到R进制的转换

**对于整数除R取余**

![img](https://img-blog.csdnimg.cn/img_convert/2c9b37fee66f4def7fb496c118110bc9.jpeg)

```cpp
// 以十进制整数转二进制为例
string TentoTwo(int x)
{
    string str = "";
    while(x)
    {
        str += (x % 2) + '0';
        x /= 2;
    }
    reverse(str.begin(), str.end());
    return str;
}
```

**对于小数乘R取整 **

![img](https://img-blog.csdnimg.cn/img_convert/db5fb92ad5ac5e2ebb36fe0d0e78ee11.jpeg)

```cpp
// 以十进制小数转二进制为例
string TentoTwo(double x)
{
    string str = "";
    int t = 0;
    while(x && t < 10)
    {
        x *= 2, t ++;
        str += ((x > 1) ? "1" : "0");
        if(x > 1)   x -= 1;
    }
    return str;
}
```

## 链表与邻接表

### 单链表

用途：邻接表（存储图和树）



head -> A -> B -> C -> D -> 空集

编号：   0      1      2     3        -1

值：	e[0] = 3   e[1] = 5     e[2] = 7   e[3] = 9			

next: ne[0] = 1  ne[1] = 2   ne[2] = 3   ne[3] = -1



每个节点存储$value$值 和$ next$指针

$e[n]$：表示某个点的值

$ne[n]$：表示某个点的next指针，空节点下标用-1表示

**单链表算法模板：**

```cpp
// head存储链表头的位置下标，e[]存储节点的值，ne[]存储节点的next指针，idx表示当前用到了哪个节点
// 第一个插入的下标为1，...，第k个插入的下标为k
int head, e[N], ne[N], idx;

// 初始化
void init()
{
    head = -1;
    idx = 1;				// 第k个插入的数下标为k
}

// 在链表头插入一个数a
// 先将a的next指针指向head节点， 再将head指向a
void insert_to_head(int a)
{
    e[idx] = a, ne[idx] = head, head = idx ++ ;
}

// 将x这个点插入到下标为k的点后面
// 先让x的next指针指向k的next指针指向的位置
// 将k的next指针指向x
void insert(int k, int x)
{
    e[idx] = x, ne[idx] = ne[k], ne[k] = idx ++ ;
}
    
// 将头结点删除，需要保证头结点存在
void remove_head()
{
    head = ne[head];
}

// 将下标是k的点后面的点删除
// 将下标为k的next指针指向 k的next指针的下标的 next指针指向的位置
void remove(int k)
{
    ne[k] = ne[ne[k]];
}

// 遍历输出链表的值
void print()
{
    for(int i = head; i != -1; i = ne[i])
        printf("%d ", e[i]);
    printf("\n");
}

// 在主程序中一定要初始化
```

### 双链表

用途：优化某些问题

双链表每个节点有两个指针，一个指向前，一个指向后

**双链表算法模板：**

```cpp
// e[]表示节点的值，l[]表示节点的左指针，r[]表示节点的右指针，idx表示当前用到了哪个节点
// 令下标为0的点为head头节点，下标为1的点为tail尾节点
// 第1个插入的点下标为3，第k个插入的点下标为 k + 2
int e[N], l[N], r[N], idx;

// 初始化
void init()
{
    //0是左端点，1是右端点
    r[0] = 1, l[1] = 0;
    idx = 3;					
}

// 在下标是k的点的右边，插入x
void insert_right(int k, int x)
{
    e[idx] = x;
    l[idx] = k, r[idx] = r[k];
    l[r[k]] = idx, r[k] = idx ++;  
}

// 在下标是k的点的左边，插入x
void insert_left(int k, int x)
{
    insert_right(l[k], x);
}

// 在最左侧插入一个数x，即在下标为0的点右边插入一个数x
void insert_head(int x)
{
    insert_right(0, x);
}

// 在最右侧插入一个数x，即在下标为1的点左边插入一个数x
void insert_tail(int x)
{
    insert_left(1, x);
}

// 删除下标为k的点
void remove(int k)
{
    l[r[k]] = l[k];
    r[l[k]] = r[k];
}

// 遍历输出链表的值
void print()
{
    for(int i = r[0]; i != 1; i = r[i])
        printf("%d ", e[i]);
    printf("\n");
}
```

## 栈与队列

### 栈

先进后出

**栈算法模板：**

```cpp
// tt表示栈顶下标
int stk[N], tt = 0;

// 向栈顶插入一个数x
void push(int x)
{
    stk[ ++ tt] = x;
}

// 从栈顶弹出一个数
void pop()
{
    tt -- ;
}

// 查询栈顶元素
int query()
{
    return stk[tt];
}

// 判断栈是否为空
bool empty()
{
    if (tt > 0)	return false;
    else	return true;
}
```

#### 表达式求值

先看下只有 + 和 * 的。

输入长度为n的字符串，例如：1+2+3*4*5

输出表达式的值，即：63

应该用什么数据结构？

栈。

应该先计算哪一步？

实际应该先计算1+2。

“表达式求值”问题，两个核心关键点：

（1）双栈，一个操作数栈，一个运算符栈；

（2）运算符优先级，栈顶运算符，和，即将入栈的运算符的优先级比较：

如果栈顶的运算符优先级低，新运算符直接入栈

如果栈顶的运算符优先级高，先出栈计算，新运算符再入栈

仍以1+2+3*4*5举例，看是如何利用上述两个关键点实施计算的。

首先，这个例子只有+和*两个运算符，所以它的运算符表是：

![00.webp.jpg](https://cdn.acwing.com/media/article/image/2021/03/20/55289_095276ee89-00.webp.jpg)

这里的含义是：

（1）如果栈顶是+，即将入栈的是+，栈顶优先级高，需要先计算，再入栈；

（2）如果栈顶是+，即将入栈的是*，栈顶优先级低，直接入栈；

（3）如果栈顶是*，即将入栈的是+，栈顶优先级高，需要先计算，再入栈；

（4）如果栈顶是*，即将入栈的是*，栈顶优先级高，需要先计算，再入栈；

有了运算符表，一切就好办了。

![01.webp.jpg](https://cdn.acwing.com/media/article/image/2021/03/20/55289_0d357dc289-01.webp.jpg)

一开始，初始化好输入的字符串，以及操作数栈，运算符栈。

![02.webp.jpg](https://cdn.acwing.com/media/article/image/2021/03/20/55289_10cc705789-02.webp.jpg)

一步步，扫描字符串，操作数一个个入栈，运算符也入栈。

![3.png](https://cdn.acwing.com/media/article/image/2021/03/20/55289_14359ecb89-3.png)

下一个操作符要入栈时，需要先比较优先级。

栈内的优先级高，必须先计算，才能入栈。

![4.webp.jpg](https://cdn.acwing.com/media/article/image/2021/03/20/55289_17b96fe289-4.webp.jpg)

计算的过程为：

（1）操作数出栈，作为num2；

（2）操作数出栈，作为num1；

（3）运算符出栈，作为op；

（4）计算出结果；

![5.webp.jpg](https://cdn.acwing.com/media/article/image/2021/03/20/55289_1b6cac1c89-5.webp.jpg)

（5）结果入操作数栈；

![6.png](https://cdn.acwing.com/media/article/image/2021/03/20/55289_1ed49bd989-6.png)

接下来，运算符和操作数才能继续入栈。下一个操作符要入栈时，继续比较与栈顶的优先级。

栈内的优先级低，可以直接入栈。

![7.png](https://cdn.acwing.com/media/article/image/2021/03/20/55289_21bfcd8989-7.png)

字符串继续移动。

![8.png](https://cdn.acwing.com/media/article/image/2021/03/20/55289_26237d8e89-8.png)

又要比较优先级了。

![9.webp.jpg](https://cdn.acwing.com/media/article/image/2021/03/20/55289_28e8139389-9.webp.jpg)

栈内的优先级高，还是先计算（3*4=12），再入栈。

![10.png](https://cdn.acwing.com/media/article/image/2021/03/20/55289_2c8291c589-10.png)

不断入栈，直到字符串扫描完毕。

![11.webp.jpg](https://cdn.acwing.com/media/article/image/2021/03/20/55289_2e61c67089-11.webp.jpg)

不断出栈，直到得到最终结果3+60=63，算法完成。

总结

“表达式求值”问题，两个核心关键点：

（1）双栈，一个操作数栈，一个运算符栈；

（2）运算符优先级，栈顶运算符，和，即将入栈的运算符的优先级比较：
如果栈顶的运算符优先级低，新运算符直接入栈

如果栈顶的运算符优先级高，先出栈计算，新运算符再入栈

这个方法的时间复杂度为O(n)，整个字符串只需要扫描一遍。

运算符有+-*/()~^&都没问题，如果共有n个运算符，会有一个n*n的优先级表。

```cpp
#include <iostream>
#include <stack>
#include <string>
#include <unordered_map>
using namespace std;

stack<int> num;
stack<char> op;

//优先级表
unordered_map<char, int> h{ {'+', 1}, {'-', 1}, {'*',2}, {'/', 2} };


void eval()//求值
{
    int a = num.top();//第二个操作数
    num.pop();

    int b = num.top();//第一个操作数
    num.pop();

    char p = op.top();//运算符
    op.pop();

    int r = 0;//结果 

    //计算结果
    if (p == '+') r = b + a;
    if (p == '-') r = b - a;
    if (p == '*') r = b * a;
    if (p == '/') r = b / a;

    num.push(r);//结果入栈
}

int main()
{
    string s;//读入表达式
    cin >> s;

    for (int i = 0; i < s.size(); i++)
    {
        if (isdigit(s[i]))//数字入栈
        {
            int x = 0, j = i;//计算数字
            while (j < s.size() && isdigit(s[j]))
            {
                x = x * 10 + s[j] - '0';
                j++;
            }
            num.push(x);//数字入栈
            i = j - 1;
        }
        //左括号无优先级，直接入栈
        else if (s[i] == '(')//左括号入栈
        {
            op.push(s[i]);
        }
        //括号特殊，遇到左括号直接入栈，遇到右括号计算括号里面的
        else if (s[i] == ')')//右括号
        {
            while(op.top() != '(')//一直计算到左括号
                eval();
            op.pop();//左括号出栈
        }
        else
        {
            while (op.size() && h[op.top()] >= h[s[i]])//待入栈运算符优先级低，则先计算
                eval();
            op.push(s[i]);//操作符入栈
        }
    }
    while (op.size()) eval();//剩余的进行计算
    cout << num.top() << endl;//输出结果
    return 0;
}
```

### 普通队列

先进先出

**普通队列算法模板：**

```cpp
// hh 表示队头，tt表示队尾
int q[N], hh = 0, tt = -1;

// 向队尾插入一个数
void push(int x)
{
    q[ ++ tt] = x;
}

// 从队头弹出一个数
void pop()
{
    hh ++ ;
}

// 队头的值
int query_head()
{
    return q[hh];
}

// 队尾的值
int query_tail()
{
    return q[tt];
}

// 判断队列是否为空
bool empty()
{
    if (hh <= tt)	return false;
    else	return true;
}
```

### 循环队列

**循环队列算法模板：**

```cpp
// hh 表示队头，tt表示队尾的后一个位置
int q[N], hh = 0, tt = 0;

// 向队尾插入一个数
q[tt ++ ] = x;
if (tt == N) tt = 0;

// 从队头弹出一个数
hh ++ ;
if (hh == N) hh = 0;

// 队头的值
q[hh];

// 判断队列是否为空
if (hh != tt)
{

}
```

### 单调栈

给定一个序列，求序列中的每个数左边 离他最近的并且比它小的数

暴力做法：

```cpp
//i的左边比i小的第一个数
for(int i = 0; i < n; i ++)
{
    for(int j = i - 1; j >= 0; j --)
    {
        if(a[i] > a[j])
        {
            cout << a[j] << endl;
            break;
        }
    }
}
```

对于暴力寻找性质：

暴力：可以用栈存储i左边的所有元素，对于第$i$个元素，查找栈顶是否比它小，大的话就弹出

优化：栈里面有些元素是永远不会作为答案出现的，假如$a_3 >= a_5$，那么对于$5$往后的询问，$a_3$永远不会出现为答案

如果存在$x < y$，并且$a_x=a_y$ ，那么$a_x$就可以被删掉，那么栈里面 存储的就一定是严格单调上升的序列了



求第$i$个数左边，离得最近的比它小的数：

$x < y < i$，如果存在$a[x] >= a[y]$ 的话，假如$a[y] < a[i] $，那么就不会考虑$a[x]$了，所以$a[x] $就没有存在的必要了

所以只需要每次判断栈顶是否比当前元素大，如果大的话就弹出，直到栈顶比当前元素小，==先输出栈顶再push当前元素==

**复杂度：O(n)**

**注意点：**

1.  在用数组模拟栈的时候，需要$++ tt$，先加再赋！！！
2. 注意$check$函数里面的等于的情况，当栈顶元素和当前元素相等的时候，也不会用到前面的元素了

**单调栈算法模板：**

```cpp
常见模型：找出每个数左边离它最近的比它大/小的数
int tt = 0;
for (int i = 1; i <= n; i ++ )
{
    while (tt && check(stk[tt], i)) tt -- ;
    // if(tt)  cout << stk[tt] << " ";
    // else    cout << "-1 ";
    stk[ ++ tt] = i;
}
```

### 单调队列

求滑动窗口中的最大值/最小值

窗口可以用队列来维护

当窗口移动的时候，新的元素从队尾进，旧元素从队尾出

优化：

队列里面有些元素是没有用的



**（使用双端队列）**

求滑动窗口最大值：

滑动窗口的初始状态是指滑动窗口内只有一个元素，在此基础上向后移动，

第0次有1个元素，队头为-2，

第1次有2个元素，队头为-1，

第2次有3个元素，队头为0，

从第$k - 1$次开始出现全

第$i $次有3个元素，队头为$ i - 2$，即队头下标为$ i - k + 1$，就可以判断队头有没有出去

我们每次将有可能成为最大值的元素放入队列末尾

对于当前元素，我们==判断队列末尾是不是比当前元素小，假如队尾元素比当前元素小，就不可能成为最大的元素了，就从后面弹出==，

直到队列内没有比当前元素更小的元素

但是==队列头上可能存在比当前元素更大的元素，所以区间的最大值为队列头==

但是每次需要判断队头有没有出去，如果出去了，最大值就变了

==单调队列里面存的是下标==

**单调队列算法模板：**

```cpp
//常见模型：找出滑动窗口中的最大值/最小值
//单调队列里面存的是下标
//当前队首元素为 i - k + 1 
int hh = 0, tt = -1;
for (int i = 0; i < n; i ++ )
{
    while (hh <= tt && check_out(q[hh])) hh ++ ;  // 判断队头是否滑出窗口
    while (hh <= tt && check(q[tt], i)) tt -- ;	  // 将入队元素与队尾元素比较大小
    q[ ++ tt] = i;
    // 队头元素是最值
}
```

## KMP

> 给定字符串S，和模式串P，求P在S中所有出现的位置的起始下标

暴力做法：

```cpp
//S长度为n，P长度为m
for (int i = 1; i <= n; i ++ )
{
    //寻找原串
    bool flag = true;
    for (int j = 1; j <= m; j ++ )
    {
        if (s[i + j - 1] != p[j])
        {
            flag=false;
            break;
        }
    }
}
```

利用字符串的特殊信息来减少暴力枚举的次数

当我们匹配了一段失败后，会将模式串向后移动以希望能够继续匹配上

那么就存在第二次开始匹配能成功的一段字符串的前缀等于上一次匹配能成功的一段字符串的后缀

对于每一个点预处理出来一段后缀，要最长的一段后缀和前缀相等

字符串的某一段前后缀相等

$next$记录的就是当前作为后缀末位的$j$对应的前缀末位的位置

$next[i] = j$

$p[1,j] = p[i - j + 1, i]$

```cpp
S = "abababc"
P = "abababab"
     12345678
// 长度为i的前缀的border
//P的next数组
ne[1] = 0;	//a
ne[2] = 0;	//ab
ne[3] = 1;	//aba
ne[4] = 2;	//abab
ne[5] = 3;	//ababa
ne[6] = 4;	//ababab
ne[7] = 5;	//abababa
ne[8] = 6;	//abababab
```

**KMP算法模板：**

$O(n)$

```cpp
// s[]是长文本，p[]是模式串，n是s的长度，m是p的长度
//在某个头文件里面有next，所以一般用ne作为数组名
//求模式串的Next数组：

string p, s;
int ne[N];

cin >> n >> p >> m >> s;
p = " " + p, s = " " + s;

for (int i = 2, j = 0; i <= n; i ++ )
{
    while (j && p[i] != p[j + 1]) j = ne[j];
    // 对于前一个border，我们往后延长一位，如果p[i] == p[j + 1]，就可以得到长度+1的border
    // 如果不行，就去前缀的前缀判断
    if (p[i] == p[j + 1]) j ++ ;
    ne[i] = j;
}

// 匹配
for (int i = 1, j = 0; i <= m; i ++ )
{
    while (j && s[i] != p[j + 1]) j = ne[j];
    if (s[i] == p[j + 1]) j ++ ;
    if (j == n)
    {
        // 匹配成功,输出匹配位置下标(此处从0开始计数，如果要从1开始就是i - n + 1)
        printf("%d ", i - n);
        j = ne[j];
    }
}
```

全新的理解方式：

**前置知识**

字符串匹配问题中，给出两个字符串 text 和 pattern (本题中 text 为 S, pattern 为 P)，需要判断 pattern 是否是 text 的子串。一般把 text 称为文本串，pattern 称为模式串。暴力的解法为：
枚举文本串 text 的起始位置 i ，然后从该位开始逐位与模式串 pattern 进行匹配。如果匹配过程中每一位都相同，则匹配成功；否则，只要出现某位不同，就让文本串 text 的起始位置变为 i + 1，并从头开始模式串 pattern 的匹配。假设 m 为文本串的长度，n 为模式串的长度。时间复杂度为 O(nm)。显然，当 n 和 m 都达到 $10^5$ 级别时无法承受。

**next直观理解**

假设有一个字符串 s (下标从 0 开始)，那么它以 i 号位作为结尾的子串就是 s[0…i]。对该子串来说，长度为 k + 1 的前缀与后缀分别是 s[0…k] 与 s[i-k…i]。我们构造一个 int 型数组（叫啥无所谓，就叫它 next吧）。其中，next[i] 表示使字串 s[0…i] 中前缀 s[0…k] 等于后缀 s[i-k…i] 的最大的 k。（注意相等的前缀、后缀在原字串中不能是 s[0…i] 本身。这点很重要，在后面感性分析时会用到）；如果找不到相等的前后缀，就令 next[i] = -1。显然，next[i] 就是所求最长相等前后缀中前缀的最后一位的下标。

第一种方法直接画线画出子串 s[0…i] 的最长相等前后缀：

![在这里插入图片描述](https://img-blog.csdnimg.cn/1e21558e746e4749bc9601f46c5854f1.jpeg#pic_center)

第二种方法在上部给出后缀，下部给出前缀，再将相等的最长前后缀框起来。

![在这里插入图片描述](https://img-blog.csdnimg.cn/31a330e7d9bf4c228e77207716e39917.jpeg#pic_center)

next[i] 就是使子串 s[0…i] 有最长相等前后缀的前缀的最后一位的下标。（这里是从0开始的下标）

我们假设已经求出了 next[0] ~ next[i-1]，用它们来推算出 next[i]

还是用我们刚刚感性认识的 s = “abababc” 作为例子。假设已经有了 next[0] = -1、next[1] = -1、next[2] = 0、next[3] = 1，现在来求解 next[4]。如下图所示，当已经得到 next[3] = 1 时，最长相等前后缀为 “ab”，之后计算 next[4] 时，由于 s[4] == s[next[3] + 1] (这里的为什么要用 next[3]？想想至尊概念)，因此可以把最长相等前后缀 “ab” 扩展为 “aba”，因此 next[4] = next[3] + 1，并令 j 指向 next[4]。

![在这里插入图片描述](https://img-blog.csdnimg.cn/424559ac6ac9440ca60317a6bc962151.jpeg#pic_center)

接着在此基础上求解 next[5]。如下图所示，当已经得到 next[4] = 2 时，最长相等前后缀为 “aba”，之后计算 next[5] 时，由于 s[5] != s[next[4] + 1]，因此不能扩展当前相等前后缀，即不能直接通过 next[4] + 1 的方法得到 next[5]。既然相等前后缀没办法达到那么长，那不妨缩短一点！此时希望找到找到一个 j，使得 s[5] == s[j + 1] 成立，同时使得图中的波浪线 ~，也就是 s[0…j] 是 s[0…2] = “aba” 的后缀，而 s[0…j] 是 s[0…2] 的前缀是显然的。同时为了找到相等前后缀尽可能长，找到这个 j 应尽可能大。

![在这里插入图片描述](https://img-blog.csdnimg.cn/a05c126c1f6f402cadd54f08d192bd4f.jpeg#pic_center)

实际上我们上图要求解的 ~ 部分，即 s[0…j] 既是 s[0…2] = “aba” 的前缀，又是 s[0…2] = “aba” 的后缀，同时又希望其长度尽可能长，那么 s[0…j] 就是 s[0…2] 的最长相等前后缀。也就是说，只需要令 j = next[2]，然后再判断 s[5] == s[j + 1] 是否成立：如果成立，说明 s[0…j + 1] 是 s[0…5] 的最长相等前后缀，令 next[5] = j + 1 即可；如果不成立，就不断让 j = next[j]，直到 j 回到了 -1，或是途中 s[5] == s[j + 1] 成立。

![在这里插入图片描述](https://img-blog.csdnimg.cn/0b1d42b1ed63479abb2a9e941a327beb.jpeg#pic_center)

如上图所示，j 从 2 回退到 next[2] = 0，发现 s[5] == s[j + 1] 不成立，就继续让 j 从 0 回退到 next[0] = -1；由于 j 已经回退到了 -1，因此不再继续回退。这时发现 s[i] == s[j + 1] 成立，说明 s[0…j + 1] 是 s[0…5] 的最长相等前后缀，于是令 next[5] = j + 1 = -1 + 1 = 0，并令 j 指向 next[5]。

下面总结 next 数组的求解过程，并给出代码：

1. 初始化 next 数组，令 j = next[0] = -1。
2. 让 i 在 1 ~ len - 1范围内遍历，对每个 i ，执行 3、4，以求解 next[i]。
3. 直到 j 回退为 -1，或是 s[i] == s[j + 1] 成立，否则不断令 j = next[j]。
4. 如果 s[i] == s[j + 1]，则 next[i] = j + 1；否则 next[i] = j。

在此基础上我们进入 kmp，有了上面求 next 数组的基础，kmp 算法就是在照葫芦画瓢，给定一个文本串 text 和一个模式串 pattern，然后判断模式串 pattern 是否是文本串 text 的子串。
以 text = “abababaabc”、pattern = “ababaab” 为例。令 i 指向 text 的当前欲比较位，令 j 指向 pattern 中当前已被匹配的最后位，这样只要 text[i] == pattern[j + 1] 成立，就说明 pattern[j + 1] 也被成功匹配，此时让 i、j 加 1 继续比较，直到 j 达到 m - 1(m 为 pattern 长度) 时说明 pattern 是 text 的子串。在这个例子中，i 指向 text[4]、j 指向 pattern[3]，表明 pattern[0…3] 已经全部匹配成功了，此时发现 text[i] == pattern[j + 1] 成立，这说明 pattern[4] 成功匹配，于是令 i、j 加 1。

![在这里插入图片描述](https://img-blog.csdnimg.cn/2347e8c4aa1a4c0d82cba2fe3dab5ec9.jpeg#pic_center)

接着继续匹配，此时 i 指向 text[5]、j 指向 pattern[4]，表明 pattern[0…4] 已经全部匹配成功。于是试着判断 text[i] == pattern[j + 1] 是否成立：如果成立，那么就有 pattern[0…5] 被成功匹配，可以令 i、j 加 1 以继续匹配下一位。但此处 text[5] != pattern[4 + 1]，匹配失败。那么我们这里该怎么做？放弃之前 pattern[0…4] 的成功匹配成果，让 j 回退到 -1 开始重新匹配吗？那是暴力解的方法，我们来看一下 kmp 的处理。

为了不让 j 直接回退到 -1，应寻求回退到一个离当前的 j （此时 j 为 4）最近的 j’，使得 text[i] == pattern[j’ + 1] 能够成立，并且 pattern[0…j’] 仍然与 text 的相应位置处于匹配状态，即 pattern[0…j’] 是 pattern[0…j] 的后缀。这很容易令人想到之前的求 next 数组时碰到的类似问题。答案是 pattern[0…j’] 是 pattern[0…j] 的最长相等前后缀。也就是说，只需要不断令 j = next[j]，直到 j 回退到 -1 或者是 text[i] == pattern[j + 1] 成立，然后继续匹配即可。next 数组的含义就是当 j + 1 位失配时，j 应该回退到的位置。对于刚刚的例子，当 text[5] 与 pattern[4 + 1] 失配时，令 j = next[4] = 2，然后我们会发现 text[i] == pattern[j + 1] 能够成立，因此就让它继续匹配，直到 j == 6 也匹配成功，这就意味着 pattern 是 text 的子串。

![kmpxiugai1.jpg](https://cdn.acwing.com/media/article/image/2023/02/23/175110_35406a81b3-kmpxiugai1.jpg)

kmp 算法的一般思路如下：

1. 初始化 j = -1，表示 pattern 当前已被匹配的最后位。
2. 让 i 遍历文本串 text，对每个 i，执行 3、4来试图匹配 text[i] 和 pattern[j + 1]。
3. 直到 j 回退到 -1 或者是 text[i] == pattern[j + 1]，否则不断令 j = next[j]。
4. 如果 text[i] == pattern[j + 1]，则令 j ++。如果 j 达到 pattern_len - 1，说明 pattern 是 text 的子串。

我们观察上面的分析，能否发现：求解 next 数组的过程其实就是模式串 pattern 进行自我匹配的过程。

考虑如何统计 pattern 在 text 中出现的起始下标：
当 j = m - 1 时表示 pattern 完全匹配，此时可以输出 i - j (text 的结束位置减去 pattern 的长度就是 pattern 在 text 中出现的下标)。但问题在于：之后应该从 pattern 的哪个位置开始进行下一次匹配？ 由于 pattern 在 text 的多次出现可能是重叠的，因此不能什么都不做就让 i 加 1继续进行比较，而是必须先让 j 回退一段距离。此时 next[j] 代表着整个 pattern 的最长相等前后缀，从这个位置开始让 j 最大，即让已经匹配的部分最长，这样能保证既不漏解，又使下一次匹配省去许多无意义的比较。



## Trie树

快速存储和查找字符串集合的数据结构

```cpp
abcdef
abdef
aced
bcdf
bcff
    
                root
            a       	b
          b		c		  c	
        c  d	e		d	f
        d  e   1d	   1f  1f
        e 1f
       1f
//在单词结束的地方打标记
```

> 维护一个字符串集合，支持向集合中插入一个字符串以及询问一个字符串在集合中出现了多少次

关于trie树如何用数组去建树，一维是结点总数，而结点和结点之间的关系（谁是谁儿子）存在第二个维度，比如\[0\]\[1\]=3, \[0\]表示根节点，\[1\]表示它有一个儿子‘b’,这个儿子的下标是3；接着如果有一个\[3\]\[2\]=8 ; 说明根节点的儿子‘b’也有一个儿子‘c’，这个孙子的下标就是8；这样传递下去，就是一个字符串。随便给一个结点\[x\]\[y\], 并不能看出它在第几层，只能知道，它的儿子是谁。

![Trie1.PNG](https://cdn.acwing.com/media/article/image/2020/06/13/31041_aa11ff2cad-Trie1.PNG)

![Trie2.PNG](https://cdn.acwing.com/media/article/image/2020/06/13/31041_aed49a42ad-Trie2.PNG)

==不是按层排布的，但是可以正常查询==

**Trie树算法模板：**

```cpp
int son[N][26], cnt[N], idx;
// 0号点既是根节点，又是空节点
// son[][]存储树中每个节点的子节点,每个字母最多再往外连26条边
// cnt[]存储以每个节点结尾的单词数量,每个节点用idx表示
// idx存储当前用到了哪个下标

// 插入一个字符串
void insert(string str)
{
    int p = 0;
    for (int i = 0; i < str.size(); i ++ )
    {
        int u = str[i] - 'a';
        //找到下一条边的编号，如果未被创建就创建一下
        if (!son[p][u]) son[p][u] = ++ idx;
        p = son[p][u];
    }
    //以p结尾的单词增加
    cnt[p] ++ ;
}

// 查询字符串出现的次数
int query(string str)
{
    int p = 0;
    for (int i = 0; i < str.size(); i ++ )
    {
        int u = str[i] - 'a';
        if (!son[p][u]) return 0;
        p = son[p][u];
    }
    return cnt[p];
}
```

### 最大异或对

> 在给定的 $N$ 个整数 $A_1，A_2……A_N$中选出两个进行 $xor$（异或）运算，得到的结果最大是多少？

异或=不进位加法

**暴力做法**

```cpp
int res = 0;
for(int i = 0; i < n; i ++)
{
	for(int j = 0; j < i; j ++)
    {
		res = max(res, a[i] ^ a[j]);
    }
}
```

第二层循环是针对于$a_i$，从$a_0$到$a_{i-1}$中寻找一个数$a_j$，使得$a[i]\bigoplus a[j]$最大

**Tire树优化**

要使得数尽可能大，就要让==高位的数尽量大==，所以先判断高位

对于每个数，先在Tire树中查找异或后最大的数，再插入进Trie树中

要使得异或值最大，就要每一位都尽量不一样，顺着在Trie树中找相反的位

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = (1e5 + 10) * 32;

int n, res, x;
int son[N][2], idx;
// N 代表一个数字的二进制串最长有多少位

void insert(int x)
{
    int p = 0;
    for(int i = 30; i >= 0; i --)
    {
        int u = x >> i & 1;		/// 取X的第i位的二进制数
        if(!son[p][u])  son[p][u] = ++ idx;
        p = son[p][u];
    }
}

int query(int x)
{
    int p = 0, res = 0;
    for(int i = 30; i >= 0; i --)
    {
        int u = x >> i & 1;
        if(son[p][!u])  	// 如果相反的位存在的话，就尽量寻找相反的位
        {
            p = son[p][!u];
            res = res * 2 + !u;
        }
        else
        {
            p = son[p][u];
            res = res * 2 + u;
        }
    }
    return res;
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    cin >> n;
    while(n --)
    {
        cin >> x;
        insert(x);
        int p = query(x);
        res = max(res, p ^ x);
    }
    cout << res << endl;
    return 0;
}
```

## 并查集

1. 将两个集合合并
2. 询问两个元素是否在一个集合里面

o(1)

基本原理：

1. 用树的形式来维护集合，根节点的编号就是集合的编号
2. 每个节点都存储父节点,p[x]表示父节点

问题：

1. 如何判断树根：if(p[x] == x)
2. 如何求x的集合编号：while(p[x] != x)    x = p[x];
3. 如何合并两个集合：p[x]为x的集合编号，p[y]为y的集合编号，p[x] = y

**优化：**

1. 找某个节点的根节点的时候，顺手把整条路径上的点的父节点都标记为根节点（路径压缩）

**注意：**

1. ==对于一些要读入单个字母的情况，我们可以声明字符数组然后读入字符串，取字符数组的0位置的元素==
2. ==当要用到x的祖宗节点时，不要直接使用p[x], 要用find(x)状态压缩更新一下祖宗节点==

**朴素并查集算法模板：**

```cpp
int p[N]; //存储每个点的祖宗节点

// p[x] == x代表这个点是树根

// 返回x的祖宗节点 + 路径压缩
int find(int x)
{
    if (p[x] != x) p[x] = find(p[x]);
    return p[x];
}

// 初始化，假定节点编号是1~n
for (int i = 1; i <= n; i ++ ) p[i] = i;

// 合并a和b所在的两个集合：
p[find(a)] = find(b);
```

**维护size(每个集合的元素个数)的并查集算法模板：**

```cpp
int p[N], siz[N];
// p[]存储每个点的祖宗节点, siz[]只有祖宗节点的有意义，表示祖宗节点所在集合中的点的数量

// 返回x的祖宗节点
int find(int x)
{
    if (p[x] != x) p[x] = find(p[x]);
    return p[x];
}

// 初始化，假定节点编号是1~n, siz[]也要初始化为1
for (int i = 1; i <= n; i ++ )
{
    p[i] = i;
    siz[i] = 1;
}

// 合并a和b所在的两个集合：
// 有的情况下要判断a和b是否在一个集合里面，假如在一个集合里面还要操作的话集合元素就会翻倍
if(find(a) == find(b))	continue;
else
{
	siz[find(b)] += siz[find(a)];	// 注意这里是将a集合合并到b集合
	p[find(a)] = find(b);
}

// 查询所在集合元素数量
siz[find(a)]
```

**维护到祖宗节点距离的并查集算法模板：**

```cpp
int p[N], d[N];
//p[]存储每个点的祖宗节点, d[x]存储x到p[x]的距离

// 返回x的祖宗节点
int find(int x)
{
    if (p[x] != x)
    {
        int u = find(p[x]);
        d[x] += d[p[x]];
        p[x] = u;
    }
    return p[x];
}

// 初始化，假定节点编号是1~n
for (int i = 1; i <= n; i ++ )
{
    p[i] = i;
    d[i] = 0;
}

// 合并a和b所在的两个集合：
p[find(a)] = find(b);
d[find(a)] = distance; // 根据具体问题，初始化find(a)的偏移量
```

### 食物链

> A 吃 B，B 吃 C，C 吃 A。现有 N 个动物，以 1∼N 编号。每个动物都是 A,B,C 中的一种，但是我们并不知道它到底是哪一种。
>
> 第一种说法是 `1 X Y`，表示 X 和 Y 是同类。第二种说法是 `2 X Y`，表示 X 吃 Y。
>
> 此人对 N 个动物，用上述两种说法，一句接一句地说出 K 句话，这 K 句话有的是真的，有的是假的。
>
> 当一句话满足下列三条之一时，这句话就是假话，否则就是真话。
>
> 1. 当前的话与前面的某些真的话冲突，就是假话；
> 2. 当前的话中 X 或 Y 比 N 大，就是假话；
> 3. 当前的话表示 X 吃 X，就是假话。
>
> 输出假话的总数

用每个点到根节点之间的距离来表示和根节点的关系

模3余1：可以吃根节点

模3余2：可以被根节点吃

模3余0：和根节点是同类

```cpp
#include <iostream>

using namespace std;

const int N = 50010;

int n, m;
int p[N], d[N];
// p[]表示元素的根节点,d[]表示到父节点的距离

int find(int x)		// 询问根节点
{
    if (p[x] != x)	// 若自身不是根节点
    {
        int t = find(p[x]);		
        // 首先通过find(p[x])来找到父节点的根节点t
        d[x] += d[p[x]];
        // d[x]表示x到父节点p[x]的距离,d[p[x]]通过前面一步已经更新为p[x]到根节点的距离
        // 在通过这一步将d[x]更新为到根节点的距离
        p[x] = t;
        // 更新父节点为根节点
    }
    return p[x];
}

int main()
{
    scanf("%d%d", &n, &m);

    for (int i = 1; i <= n; i ++ ) p[i] = i;

    int res = 0;
    while (m -- )
    {
        int t, x, y;
        scanf("%d%d%d", &t, &x, &y);

        if (x > n || y > n) res ++ ;
        else
        {
            int px = find(x), py = find(y);
            if (t == 1)		// x和y是同类
            {
                if (px == py && (d[x] - d[y]) % 3) res ++ ;
                // 如果x和y在同一集合内,且(d[x] - d[y]) % 3 == 0,那么已经是同一类了
                else if (px != py)	// 不在同一集合内就需要更新到同一集合里面
                {
                    p[px] = py;		// 将x所在集合合并到y所在集合
                    d[px] = d[y] - d[x];
                    // p[x]是x的根节点,p[y]是y的根节点,d[x]是x到p[x]的距离,d[y]是y到p[y]的距离
                    // d[px]是x所在集合到y所在集合的根节点py的距离
                    // x与y同类,那么合并后(d[x] - d[y]) % 3 == 0
                    // 然而合并的时候d[x] = d[x] + d[px]
                    // 所以(d[x] + d[px] - d[y]) % 3 == 0
                    // d[px] = d[y] - d[x]
                }
            }
            else			// x吃y
            {
                if (px == py && (d[x] - d[y] - 1) % 3) res ++ ;
                // 如果x和y在同一集合内,且(d[x] - d[y] - 1) % 3 == 0,那么可以表示x吃y
                else if (px != py)
                {
                    p[px] = py;
                    d[px] = d[y] + 1 - d[x];
                    // 合并后(d[x] - d[y] - 1) % 3 == 0
                    // 合并的时候d[x] = d[x] + d[px]
                    // 所以(d[x] + d[px] - d[y] - 1) % 3 == 0
                    // d[px] = d[y] - d[x] + 1
                }
            }
        }
    }

    printf("%d\n", res);

    return 0;
}
```

## 堆

手写一个堆需要能实现哪些操作：

1. 插入一个数
2. 求集合当中的最小值
3. 删除最小值
4. 删除任意元素(STL无法实现)
5. 修改任意元素(STL无法实现)



堆的底层是完全二叉树

小根堆：每个点都是小于等于左右儿子的值（根节点就是最小值）

存储方式：用数组存，1号点为根节点，x节点的左儿子节点下标为2x， x节点的右儿子节点下标为2x+1



更新的时候，每个点和左右儿子的较小值进行交换，以实现up和down操作

插入：heap[++ size] = x;  up(size)   在堆的最后位置放x，up里面传要进行操作的位置

堆中的最小值：heap[1]

删除最小值： heap[1] = heap[size];    size -- ;     down(1);     用最后一个元素来覆盖最小的元素

删除任意元素：heap[k] = heap[size];	 size --;	down(k);	up(k);

修改任意元素:   heap[k] = x;	down(k);	up(k);

带映射的堆不是很常用，即不用实现$heap\_swap$操作，一般只需要实现最基本的堆即可

**堆算法模板：**

```cpp
#include <string.h>
#incldue <algorithm>

// h[N]存储堆中的值, h[1]是堆顶，x的左儿子是2x, 右儿子是2x + 1
// ph[k]存储第k个插入的点在堆中的位置
// hp[k]存储堆中下标是k的点是第几个插入的
int h[N], ph[N], hp[N], siz;

// 交换两个点，及其映射关系
void heap_swap(int a, int b)
{
    swap(ph[hp[a]],ph[hp[b]]);
    swap(hp[a], hp[b]);
    swap(h[a], h[b]);
}

void down(int u)
{
    int t = u;
    if (u * 2 <= siz && h[u * 2] < h[t]) t = u * 2;
    if (u * 2 + 1 <= siz && h[u * 2 + 1] < h[t]) t = u * 2 + 1;
    if (u != t)
    {
        heap_swap(u, t);
        // 当对于第几个插入没有执念的时候，直接swap(h[u], h[t])也可以
        down(t);
    }
}

void up(int u)
{
    while (u / 2 && h[u] < h[u / 2])
    {
        heap_swap(u, u / 2);
        u >>= 1;
    }
}

// O(n)建堆
for (int i = n / 2; i; i -- ) down(i);

// 向堆中插入元素
h[++ siz] = x;  
up(siz);

// 向堆中插入第k个元素x
h[++ siz] = x;
ph[++ m] = siz, hp[siz] = m;
up(siz);

// 堆中的最小值
h[1]
    
// 删除堆的最小值
h[1] = h[siz];
// 或者heap_swap(1, siz);
siz --;     
down(1);

// 删除任意元素
h[k] = h[siz];	 
siz --;	
down(k), up(k);

// 删除第k个插入的元素，注意先把ph[k]先存起来
k = ph[k];
heap_swap(k, siz);
siz --;
down(k), up(k);

// 修改任意元素
h[k] = x;	
down(k), up(k);

// 修改第k个插入的元素为x
k = ph[k];
h[k] = x;
down(k), up(k);
```

## Hash表

### 存储结构

1. 开放寻址法
2. 拉链法

将一些比较复杂的数据映射到0-N这些数，比如将0 - 10<sup>9</sup>映射到0 - 10<sup>5</sup>

将输入x （-10^9 ~ + 10^9）通过哈希函数转换为输出y （0 ~ +10^5） 

问题：

1. 哈希函数一般怎么写？  x mod 10^5 ==一般来说模的数要取成质数，冲突的概率会小一点==
2. 有冲突怎么办？即不同的输入x导致了相同的输出y

**拉链法：**

开一个一维数组来存储所有的哈希值，如果有出现冲突的话就在下面把数拉出来

```cpp
数组a[] : 1 2 3 4 5 6 
           |
    	   23
    	   |
    	   13
```

**开放寻址法：**

开一个一维数组，开到数据范围的2-3倍



**一般哈希算法模板：**

**拉链法**

```cpp
#include <cstring>
#include <iostream>

// N一般为质数,比如1e5 + 7 
// h[]是一条主链,e[]是主链下面拖的一条链
// e[]存放值,ne[]存放指针,idx表示用到了哪些位置
int h[N], e[N], ne[N], idx;

// 向哈希表中插入一个数
void insert(int x)
{
    int k = (x % mod + mod) % mod;
    // k是存放在h中的位置
    e[idx] = x;
    ne[idx] = h[k];
    // h[k]存放的是下面拖的第一个点的下标(类似于头指针)
    // 让新的点的next指针指向h[k],再让h[k]指向新的点
    h[k] = idx ++;
}

// 在哈希表中查询某个数是否存在
bool query(int x)
{
    int k = (x % mod + mod) % mod;
    for (int i = h[k]; i != -1; i = ne[i])
        if (e[i] == x)
            return true;

    return false;
}

// 链表头最开始初始化为-1
memset(h, -1, sizeof(h));
```

**开放寻址法**

```cpp
const int null = 0x3f3f3f3f;
int h[N];	// 一般开到2-3倍
// 质数一般取刚好比N大的质数
// 如果x在哈希表中，返回x的下标；如果x不在哈希表中，返回x应该插入的位置
int query(int x)
{
    int t = (x % mod + mod) % mod;
    while (h[t] != null && h[t] != x)
    {
        t ++ ;
        if (t == N) t = 0;
    }
    return t;
}

memset(h, 0x3f, sizeof(h));
// memset按照字节去分配,而一个int为4字节,所以将0x3f3f3f3f拆分成0x3f
int k = query(x);
// 插入
h[k] = x;
// 查找
if(h[k] != null)	cout << "Yes" << endl;
else	cout << "No" << endl; 
```

### 字符串哈希

字符串前缀哈希法

Str = "ABCABCDEYXCAcwing"

预处理所有前缀的哈希

h[0] = 0

h[1] = "A"的哈希值

h[2] = "AB"的哈希值

1. 把字符串看成是p进制的数

   ABCD

   (1234)<sub>p</sub>

   哈希值 = (1 * p^3 + 2 * p^2 + 3 * p^1 + 4 * p^0 ) mod q

2. ==不能映射成0==

3. 此处假定不存在冲突 



可以利用前缀哈希求出所有子串的哈希值

计算子串 str[l ~ r] 的哈希值：

```cpp
1         l-1 l           r
r-1	      l   l-1         0
    
h[1 - r] = h[r]
h[1 - l-1] = h[l-1] * p^(r-l+1)
```

从$l$到$r$这段的哈希值$h[R]-h[L-1]\times p^{R-L+1}$

用于判断两个子串是否相同 

**字符串哈希算法模板：**

```cpp
// 核心思想：将字符串看成P进制数，P的经验值是131或13331，取这两个值的冲突概率低
// 小技巧：取模的数用2^64，这样直接用unsigned long long存储，溢出的结果就是取模的结果

typedef unsigned long long ULL;
const int P = 13331, N = 1e5 + 10;
// const ULL q = pow(2,64);
ULL h[N], p[N]; // h[k]存储字符串前k个字母的哈希值, p[k]存储 P^k mod 2^64
string str;

str = " " + str;
// 初始化
p[0] = 1;
for (int i = 1; i <= n; i ++ )
{
    h[i] = h[i - 1] * P + str[i];		// 计算前h位的哈希值
    p[i] = p[i - 1] * P;				// 计算p的指数次方
}

// 计算子串 str[l ~ r] 的哈希值
ULL get(int l, int r)
{
    return h[r] - h[l - 1] * p[r - l + 1];
}
```

## STL

**C++ STL算法模板**

### vector

```cpp
vector, 变长数组，倍增的思想
    // 定义
    vector<int> a;
	vector<int> a(n);
	vector<int> a(10, 3);	//定义一个长度为10的vector，并初始化为3

	// 函数
    a.size()  返回元素个数
    a.empty()  返回是否为空
    a.clear()  清空
    a.front()	a.back()
    a.push_back()	a.pop_back()
    a.begin()	a.end()	// 迭代器

    // 遍历
	for(auto x : a)				cout << x << endl;
	for(vector<int>::iterator i = a.begin(); i != a.end(); i ++)		cout << *i << endl;
    for(auto i = a.begin(); i != a.end(); i ++)		cout << *i << endl;
    for(int i = 0; i < a.size(); i ++)

    []		// 支持随机化访问

    支持比较运算，按字典序
    vector<int> a(4, 3), b(3,4);
    cout << (a < b) << endl;	// ans = 1
```

### pair

```cpp
pair<int, int> p;
    p.first, 第一个元素
    p.second, 第二个元素
    支持比较运算，以first为第一关键字，以second为第二关键字（字典序）
        
    pair<int, pair<int,int> >
    // 初始化
    p = make_pair(10, "ab");
    p = {20, "ac"};
```

### string

```cpp
string，字符串
    string s;
    s.size() / s.length()  返回字符串长度
    s.empty()
    s.clear()
    s += str

    s.substr(起始下标，(子串长度))  返回子串		
    // s.substr(1, 2);
    s.substr(1)		//返回从1开始的所有子串

    s.c_str()  返回字符串所在字符数组的起始地址
    scanf("%s", str.c_str());
    printf("%s\n", str.c_str());
```

### queue

```cpp
queue, 队列
    // 没有clear()
    queue<int> q;
	// 清空 q = queue<int>();
    q.size()
    q.empty()
    q.push()  向队尾插入一个元素
    q.front()  返回队头元素
    q.back()  返回队尾元素
    q.pop()  弹出队头元素
```

### priority_queue

```cpp
priority_queue, 优先队列，默认是大根堆 就是大的排在前面
    #include <queue>
    priority_queue<int> q;
	q.clear();
    size()
    empty()
    push()  插入一个元素
    top()  返回堆顶元素
    pop()  弹出堆顶元素
    定义成小根堆的方式：priority_queue<int, vector<int>, greater<int> > q; // great<int> > 这边最好用空格分隔开
```

### stack

```cpp
stack, 栈
    size()
    empty()
    push()  向栈顶插入一个元素
    top()  返回栈顶元素
    pop()  弹出栈顶元素
```

### deque

```cpp
deque, 双端队列
    size()
    empty()
    clear()
    front()		back()	
    push_back()		pop_back()
    push_front()	pop_front()
    begin()		end()	//迭代器
    []		// 支持随机访问
```

### set+map

```cpp
set, map, multiset, multimap, 基于平衡二叉树（红黑树），动态维护有序序列
    size()
    empty()
    clear()
    begin()/end()
    ++, -- 返回前驱和后继，时间复杂度 O(logn)

    set/multiset
    	// set里面没有重复元素,multiset里面有重复元素 
    	set<int> s;
		multiset<int> MS;
        insert()  插入一个数
        find()  查找一个数	// 不存在的话返回end()迭代器
        count()  返回某一个数的个数
        erase()
            (1) 输入是一个数x，删除所有x   O(k + logn)
            (2) 输入一个迭代器，删除这个迭代器
        lower_bound() / upper_bound()
            lower_bound(x)  返回大于等于x的最小的数的迭代器
            upper_bound(x)  返回大于x的最小的数的迭代器
            
    map/multimap
        
        insert()  插入的数是一个pair
        erase()  输入的参数是pair或者迭代器
        find()
        []  注意multimap不支持此操作。 时间复杂度是 O(logn)
        // map<string, int> a;
        // a["yxc"] = 1;	插入
        // cout << a["yxc"] << endl;	查询
        lower_bound()/upper_bound()
```

### unordered

```cpp
unordered_set, unordered_map, unordered_multiset, unordered_multimap, 基于哈希表
    和上面类似，增删改查的时间复杂度是 O(1)
    内部无序, 不支持 lower_bound()/upper_bound()， 迭代器的++，--
```

### bitset

```cpp
bitset, 圧位
    bitset<10000> s;
	// 是正常的bool数组内存的1/8
    ~, &, |, ^
    >>, <<
    ==, !=
    []

    count()  返回有多少个1

    any()  判断是否至少有一个1
    none()  判断是否全为0

    set()  把所有位置成1
    set(k, v)  将第k位变成v
    reset()  把所有位变成0
    flip()  等价于~
    flip(k) 把第k位取反
```
