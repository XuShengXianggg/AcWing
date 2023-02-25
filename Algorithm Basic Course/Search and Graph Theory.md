# 搜索与图论

## DFS

深度优先搜索会优先进行深度上的搜索，当搜索到叶子节点的时候回溯到有叶子节点未被搜索过的父亲节点，再继续搜索

数据结构：stack                                空间O(n) 

回溯、剪枝

搜索完回溯的时候要注意 恢复现场，方便接下来的搜索

### 排列数字

> 按字典序输出1-n的所有排列方式

通过DFS的方式，先搜索第一个位置1的情况，然后深度+1，到第二个位置，先第二个位置2的情况，再深度+1，到第三个位置为3，然后回溯到第二个位置2的情况，到第二个位置3的情况，再深度+1，到第三个位置2的情况，再回溯到第一个位置1的情况，类推

对于恢复现场的解释：当我们搜索到第u个位置的时候，我们将u填1，向下搜索，搜索完成后回溯到这个点，要将之前的操作恢复

```cpp
#include <iostream>

using namespace std;

const int N = 10;

int n;
int path[N];	// 存储状态
bool st[N];	    // 存储当前数字是否被用过

void dfs(int u)
{
    if(u == n)		// u=0时,搜索第一个位置,那么u=n时,就已经把所有位置搜索完了
    {
        for(int i = 0; i < n; i ++)    printf("%d ", path[i]);
        printf("\n");
        return ;
    }
    for(int i = 1; i <= n; i ++)
    {
        if(!st[i])
        {
            path[u] = i;	// 第u个位置填i
            st[i] = true;	// 将i标记为已用
            dfs(u + 1);		// 搜索下一个位置
            st[i] = false;      // 递归结束后及时恢复原位
        }
    }
}

int main()
{
    scanf("%d", &n);
    dfs(0);
    return 0;
}
```

### n-皇后问题

> n皇后问题：在棋盘上放n个皇后，不能在同一行、同一列、同一斜线上，输出所有方案

剪枝：在搜索的过程中发现某种方案不可行，就将基于该种方案的所有叶子都剪掉，回溯到上一节点

```cpp
#include <iostream>

using namespace std;

const int N = 20;

int n;
char g[N][N];
bool col[N], dg[N], udg[N];		// dg表示正对角线,udg表示反对角线(对角线个数为2*n-1)

void dfs(int u)
{
    if (u == n)
    {
        for(int i = 0; i < n; i ++)
        {
            for(int j = 0; j < n; j ++)
            {
                printf("%c", g[i][j]);
            }
            printf("\n");
        }
        printf("\n");
        return ;
    }

    for (int i = 0; i < n; i ++ )
    {
        // 反对角线:y = x + b 	正对角线:y = -x + b
        // 	       b = y - x 		   b = y + x
        // 			 = i - u + n		 = i + u	
        if (!col[i] && !dg[u + i] && !udg[n - u + i])
        {
            g[u][i] = 'Q';
            col[i] = dg[u + i] = udg[n - u + i] = true;
            dfs(u + 1);
            col[i] = dg[u + i] = udg[n - u + i] = false;
            g[u][i] = '.';
        }
    }
}

int main()
{
    cin >> n;
    for (int i = 0; i < n; i ++ )
        for (int j = 0; j < n; j ++ )
            g[i][j] = '.';

    dfs(0);

    return 0;
}
```

还可以从最上角开始搜索，判定每个位置是否放皇后

```cpp
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 25;   // 对角线条数，*2

int n;
char g[N][N];
bool row[N], col[N], dg[N], udg[N];     // 存储行列两个对角线上是否放过皇后

void dfs(int x, int y, int s)   // 从(x, y)开始遍历,此时有s个皇后
{
    if(y == n)  y = 0, x ++;    // y到了最右边,就换到下面一行
    if(x == n)  // 最下面一行已经遍历完了
    {   
        if(s ==n)
        {
            for(int i = 0; i < n; i++)    printf("%s\n", g[i]);
            printf("\n");
        }
        return ;
    }
    // 这个位置不放皇后
    dfs(x, y + 1, s);
    // 这个位置能放皇后并且准备放皇后
    // 正对角线 y = - x + b, 反对角线 y = x + b
    // b = x + y                      b = y - x + n    
    if(!row[x] && !col[y] && !dg[x + y] && !udg[y - x + n])
    {
        g[x][y] = 'Q';  // 放皇后
        row[x] = col[y] = dg[x + y] = udg[y - x + n] = true;    // 标记行列对角线上放过了
        dfs(x, y + 1, s + 1);
        g[x][y] = '.';
        row[x] = col[y] = dg[x + y] = udg[y - x + n] = false;
    }
}

int main()
{
    cin >> n;
    for(int i = 0; i < n; i ++)
        for(int j = 0; j < n; j ++)
            g[i][j] = '.';
    dfs(0, 0, 0);
    return 0;
}
```

## BFS

一层一层的进行搜索

数据结构：queue                              空间O(2<sup>n</sup>)

第一次扩展到的点具有最短路性质

1. 初始状态入队
2. while 队列不空 取出队头扩展队头

### 走迷宫

> 给定一个 n×m 的二维整数数组，用来表示一个迷宫，数组中只包含 0 或 1，其中 0 表示可以走的路，1 表示不可通过的墙壁。
>
> 最初，有一个人位于左上角 (1,1) 处，已知该人每次可以向上、下、左、右任意一个方向移动一个位置。
>
> 请问，该人从左上角移动至右下角 (n,m) 处，至少需要移动多少次。

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

typedef pair<int, int> PII;
const int N = 110;

int n, m;
int g[N][N], d[N][N];
PII q[N * N];
// PII pre[N][N];

void bfs()
{
    int hh = 0, tt = 0;
    q[0] = {1, 1};
    memset(d, -1, sizeof(d));
    d[1][1] = 0;
    
    int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};
    
    while(hh <= tt)
    {
        auto p = q[hh ++];
        for(int i = 0; i < 4; i ++)
        {
            int x = p.first + dx[i], y = p.second + dy[i];
            if(x >= 1 && x <= n && y >= 1 && y <= m && g[x][y] == 0 && d[x][y] == -1)
            {
                d[x][y] = d[p.first][p.second] + 1;
                // pre[x][y] = p;
                q[++ tt] = {x, y};
            }
        }
    }
    printf("%d\n", d[n][m]);
    /*	输出路径
    int x = n, y = m;
    while(x || y)
    {
        printf("%d %d\n", x, y);
        auto t = pre[x][y];
        x = t.first, y = t.second;
    }
    */
}

int main()
{
    cin >> n >> m;
    for(int i = 1; i <= n; i ++)
        for(int j = 1; j <= m; j ++)
            cin >> g[i][j];
    bfs();
    return 0;
}
```

### 八数码

> 在一个 3×3 的网格中，1∼8 这 8 个数字和一个 `x` 恰好不重不漏地分布在这 3×3 的网格中。
>
> 例如：
>
> ```
> 1 2 3
> x 4 6
> 7 5 8
> ```
>
> 在游戏过程中，可以把 `x` 与其上、下、左、右四个方向之一的数字交换（如果存在）。
>
> 我们的目的是通过交换，使得网格变为如下排列（称为正确排列）：
>
> ```
> 1 2 3
> 4 5 6
> 7 8 x
> ```
>
> 例如，示例中图形就可以通过让 `x` 先后与右、下、右三个方向的数字交换成功得到正确排列。
>
> 交换过程如下：
>
> ```
> 1 2 3   1 2 3   1 2 3   1 2 3
> x 4 6   4 x 6   4 5 6   4 5 6
> 7 5 8   7 5 8   7 x 8   7 8 x
> ```
>
> 现在，给你一个初始网格，请你求出得到正确排列至少需要进行多少次交换。

我们可以用`1 2 3 x 4 6 7 5 8`字符串来表示每个状态，再利用`map<string, int>`来表示状态的距离

在BFS中将字符串恢复成`3×3`的矩阵

1、题目的目标

![八数码目标.JPG](https://cdn.acwing.com/media/article/image/2020/06/22/31041_b4125f44b4-%E5%85%AB%E6%95%B0%E7%A0%81%E7%9B%AE%E6%A0%87.JPG)

求最小步数 -> 用BFS

2、移动情况

![移动方式.JPG](https://cdn.acwing.com/media/article/image/2020/06/22/31041_c5a2fa20b4-%E7%A7%BB%E5%8A%A8%E6%96%B9%E5%BC%8F.JPG)

移动方式：

![转移方式2.JPG](https://cdn.acwing.com/media/article/image/2020/06/22/31041_ce16ebf8b4-%E8%BD%AC%E7%A7%BB%E6%96%B9%E5%BC%8F2.JPG)

转以后：a = x + dx[i], b = y + dy[i].

思想：将每一种情况作为1个节点，目标情况即为终点

从初始状况移动到目标情况 —> 求最短路

3、问题

第一点：怎么表示一种情况使其能作为节点？

第二点：如何记录每一个状态的“距离”（即需要移动的次数）？

第三点：队列怎么定义，dist数组怎么定义？

4、解决方案

将 “3*3矩阵” 转化为 “字符串”

如：

![表示方法.JPG](https://cdn.acwing.com/media/article/image/2020/06/22/31041_f1e23b96b4-%E8%A1%A8%E7%A4%BA%E6%96%B9%E6%B3%95.JPG)

所以：

```cpp
队列可以用 queue<string>
//直接存转化后的字符串
dist数组用 unordered_map<string, int>
//将字符串和数字联系在一起，字符串表示状态，数字表示距离
```

5、矩阵与字符串的转换方式

![矩阵转换.JPG](https://cdn.acwing.com/media/article/image/2020/06/22/31041_f74b6cb0b4-%E7%9F%A9%E9%98%B5%E8%BD%AC%E6%8D%A2.JPG)

6、代码

```cpp
#include <iostream>
#include <algorithm>
#include <queue>
#include <unordered_map>

using namespace std;

int bfs(string start)
{
    //定义目标状态
    string end = "12345678x";
    //定义队列和dist数组
    queue<string> q;
    unordered_map<string, int> d;
    //初始化队列和dist数组
    q.push(start);
    d[start] = 0;
    //转移方式
    int dx[4] = {1, -1, 0, 0}, dy[4] = {0, 0, 1, -1};

    while(q.size())
    {
        auto t = q.front();
        q.pop();
        //记录当前状态的距离，如果是最终状态则返回距离
        int distance = d[t];
        if(t == end) return distance;
        //查询x在字符串中的下标，然后转换为在矩阵中的坐标
        int k = t.find('x');
        int x = k / 3, y = k % 3;

        for(int i = 0; i < 4; i++)
        {
            //求转移后x的坐标
            int a = x + dx[i], b = y + dy[i];
            //当前坐标没有越界
            if(a >= 0 && a < 3 && b >= 0 && b < 3)
            {
                //转移x
                swap(t[k], t[a * 3 + b]);
                //如果当前状态是第一次遍历，记录距离，入队
                if(!d.count(t))
                {
                    d[t] = distance + 1;
                    q.push(t);
                }
                //还原状态，为下一种转换情况做准备
                swap(t[k], t[a * 3 + b]);
            }
        }
    }
    //无法转换到目标状态，返回-1
    return -1;
}

int main()
{
    string start = "";
    for(int i = 0; i < 9; i ++)
    {
        char c;
        cin >> c;
        start += c;
    }
    cout << bfs(start) << endl;

    return 0;
}
```

## 图的存储

树是一种特殊的图(无环连通图)，与图的存储方式相同。
对于无向图中的边ab，存储两条有向边a->b, b->a。
因此我们可以只考虑有向图的存储。

(1) 邻接矩阵：g\[a\]\[b\] 存储边a->b

(2) 邻接表：

```cpp
// 对于每个点k，开一个单链表，存储k所有可以走到的点。
// h[k]存储这个单链表的头结点，e[k]表示该点在图中对应的编号
// 有向图中，边的数量最多是点的数量的2*n-2，即e和ne要开到h的两倍大
int h[N], e[N], ne[N], idx;

// 添加一条边a->b
void add(int a, int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++ ;
}

// 初始化
idx = 0;
memset(h, -1, sizeof(h));
```

## 图上DFS

时间复杂度 O(n+m), n 表示点数，m 表示边数

**深度优先遍历算法模板**

```cpp
bool st[N];

void dfs(int u)
{
    st[u] = true; // st[u] 表示点u已经被遍历过

    for (int i = h[u]; i != -1; i = ne[i])
    {
        int j = e[i];		// 第i个点对应的图上编号
        if (!st[j]) dfs(j);
    }
}
```

### 树的重心

> 给定一颗树，树中包含 n 个结点（编号 1∼n）和 n−1 条无向边。
>
> 请你找到树的重心，并输出将重心删除后，剩余各个连通块中点数的最大值。
>
> 重心定义：重心是指树中的一个结点，如果将这个点删除后，剩余各个连通块中点数的最大值最小，那么这个节点被称为树的重心。

对于某一个节点，删除后，可以分为子树和根两个部分，根所在的连通块中点的数量等于点总数-以要删除的节点为根的树中点的数量

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 1e5 + 10; //数据范围是10的5次方
const int M = 2 * N; //以有向图的格式存储无向图，所以每个节点至多对应2n-2条边

int h[N]; //邻接表存储树，有n个节点，所以需要n个队列头节点
int e[M]; //存储元素
int ne[M]; //存储列表的next值
int idx; //单链表指针
int n; //题目所给的输入，n个节点
int ans = N; //表示重心的所有的子树中，最大的子树的结点数目

bool st[N]; //记录节点是否被访问过，访问过则标记为true

//a所对应的单链表中插入b  a作为根 
void add(int a, int b) {
    e[idx] = b, ne[idx] = h[a], h[a] = idx++;
}

//返回以u为根的子树中节点的个数，包括u节点
int dfs(int u) {
    int res = 0; //存储 删掉某个节点之后，最大的连通子图节点数
    st[u] = true; //标记访问过u节点
    int sum = 1; //存储 以u为根的树 的节点数, 包括u，如图中的4号节点

    //访问u的每个子节点
    for (int i = h[u]; i != -1; i = ne[i]) {
        int j = e[i];
        //因为每个节点的编号都是不一样的，所以 用编号为下标 来标记是否被访问过
        if (!st[j]) {
            int s = dfs(j);  // u节点的单棵子树节点数 如图中的size值
            res = max(res, s); // 记录最大联通子图的节点数
            sum += s; //以j为根的树 的节点数
        }
    }

    //n-sum 如图中的n-size值，不包括根节点4；
    res = max(res, n - sum); // 选择u节点为重心，最大的 连通子图节点数
    ans = min(res, ans); //遍历过的假设重心中，最小的最大联通子图的 节点数
    return sum;
}

int main() {
    memset(h, -1, sizeof h); //初始化h数组 -1表示尾节点
    cin >> n; //表示树的结点数

    // 树中是不存在环的，对于有n个节点的树，必定是n-1条边
    for (int i = 0; i < n - 1; i++) {
        int a, b;
        cin >> a >> b;
        add(a, b), add(b, a); //无向图
    }

    dfs(1); //可以任意选定一个节点开始 u<=n

    cout << ans << endl;

    return 0;
}
```

## 图上BFS

时间复杂度 O(n+m), n 表示点数，m 表示边数

**宽度优先遍历算法模板**

```cpp
queue<int> q;
st[1] = true; // 表示1号点已经被遍历过
q.push(1);

while (q.size())
{
    int t = q.front();
    q.pop();

    for (int i = h[t]; i != -1; i = ne[i])
    {
        int j = e[i];
        if (!st[j])
        {
            st[j] = true; // 表示点j已经被遍历过
            q.push(j);
        }
    }
}

// 手动实现队列 
void bfs()
{
    int hh = 0, tt = 0;
    q[0] = 1;
    
    memset(d, -1, sizeof(d));
    
    d[1] = 0;
    while(hh <= tt)
    {
        int t = q[hh ++];
        for(int i = h[t]; i != -1; i = ne[i])
        {
            int j = e[i];
            if(d[j] == -1)
            {
                d[j] = d[t] + 1;
                q[++ tt] = j;
            }
        }
    }
    return d[n];
}
```

### 图中点的层次

> 给定一个 n 个点 m 条边的有向图，图中可能存在重边和自环。
>
> 所有边的长度都是 1，点的编号为 1∼n。
>
> 请你求出 1 号点到 n 号点的最短距离，如果从 1 号点无法走到 n 号点，输出 −1。

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 1e5 + 10;

int n, m, a, b;		// n个点，m条边
int e[N], h[N], ne[N], idx;	
// idx为边的条数，h[]存储每个点通往哪些点，e[]存储点的编号，ne存储下一个与h[]相连的点	
int d[N], q[N];		// d为距离1号点距离， q为队列

void add(int a, int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
}

int bfs()
{
    int hh = 0, tt = 0;
    q[0] = 1;
    d[1] = 0;
    while(hh <= tt)
    {
        int t = q[hh ++];	// t为起始点的编号
        for(int i = h[t]; i != -1; i = ne[i])
        {
            int j = e[i];	// j为起始点所连边的编号
            if(d[j] == -1)	
            {
                d[j] = d[t] + 1;
                q[++ tt] = j;	// 将j点扔进队列
            }
        }
    }
    
    return d[n];
}

int main()
{
    memset(h, -1, sizeof(h));		// 头节点赋为-1
    memset(d, -1, sizeof(d));		// 距离初始化为-1
    cin >> n >> m;
    for(int i = 0; i < m; i ++)
    {
        cin >> a >> b;
        add(a, b);
    }
    cout << bfs() << endl;
    return 0;
}
```

## 拓扑排序

> 若一个由图中所有点构成的序列 A 满足：对于图中的每条边 (x,y)，x 在 A 中都出现在 y 之前，则称 A 是该图的一个拓扑序列。

有向图才有拓扑序列

拓扑序列里面的点所有的边都是从前指向后的

不是所有图都有拓扑序，只要有环就不可能存在拓扑序，有向无环图(DAG)又被称为拓扑图，拓扑序不唯一

度数：入度、出度，入度是指有多少条边指向自己，出度是指从自己有几条边出去

一个有向无环图，一定至少有一个入度为0的点



入度为0意味着没有一条边指向自己，那么自然而然就在最前面，所以拓扑排序第一步就是将所有入度为0的点入队

随后就是宽搜的过程，枚举队头的所有出边t->j，删掉这条边，那么j的入度-1

如果j的入度等于0了，就说明j前面的点都排好序了，j就可以入队了

**拓扑排序算法模板**

时间复杂度 O(n+m), n表示点数，m 表示边数

```cpp
int q[N], d[N];	// d存储入度,q存储拓扑序

bool topsort()
{
    int hh = 0, tt = -1;

    // d[i] 存储点i的入度
    for (int i = 1; i <= n; i ++ )
        if (!d[i])
            q[ ++ tt] = i;

    while (hh <= tt)
    {
        int t = q[hh ++ ];

        for (int i = h[t]; i != -1; i = ne[i])
        {
            int j = e[i];
            if (-- d[j] == 0)
                q[ ++ tt] = j;
        }
    }

    // 如果所有点都入队了，说明存在拓扑序列；否则不存在拓扑序列。
    return tt == n - 1;
}
```

### 有向图的拓扑序列

> 给定一个 n 个点 m 条边的有向图，点的编号是 1 到 n，图中可能存在重边和自环。
>
> 请输出任意一个该有向图的拓扑序列，如果拓扑序列不存在，则输出 −1。

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>

using namespace std;

const int N = 1e5 + 10;

int h[N], e[N], ne[N], idx;
int n, m, a, b;
int q[N], d[N];     // d[]存储入度

void add(int a, int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
}

int topsort()
{
    int hh = 0, tt = -1;
    
    for(int i = 1; i <= n; i ++)
    {
        if(!d[i])   q[++ tt] = i;
    }
    while(hh <= tt)
    {
        int t = q[hh ++];
        for(int i = h[t]; i != -1; i = ne[i])
        {
            int j = e[i];
            if(-- d[j] == 0)    q[++ tt] = j;
        }
    }
    return tt == n - 1;
}

int main()
{
    memset(h, -1, sizeof(h));
    cin >> n >> m;
    for(int i = 0; i < m; i ++)
    {
        cin >> a >> b;
        add(a, b);
        d[b] ++;
    }
    if(topsort())
    {
        for(int i = 0; i < n; i ++) printf("%d ", q[i]);
    }
    else    printf("-1");
    return 0;
}
```

## 最短路问题

n 为点的数量，m 为边的数量

m大约和$n^2$一个级别为稠密图，m大约和n一个级别为稀疏图

稠密图用邻接矩阵存储，稀疏图用邻接表存储



难点在如何建图

1. 单源最短路

   从一个点到其他所有点的最短距离

   * 所有边权都是正数
     * 朴素Dijkstra算法	$O(n^2)$    适用于稠密图
     * 堆优化版的Dijkstra算法    $O(mlogn)$    适用于稀疏图
   * 存在负权边
     * Bellman-Ford    $O(nm)$		求不超过k条边的最短路用Bellman-Ford
     * SPFA    一般$O(m)$，最坏是$O(nm)$ 

2. 多源汇最短路

   源点：起点   汇点：终点

   * Fylod $O(n^3)$

### 朴素Dijkstra

S集合为已经确定最短距离的点的集合

1. 先初始化距离，`dist[1] = 0`，`dist[i] = INF`

2. `for i : 1 ~ n ` 

   找到不在S中的距离最近的点t	$O(n^2)$

   将点t加入到S中去，用t更新其他点的距离	$O(n)$

   就是看点x到一号点的距离能否用t到一号点的距离更新，即`dist[s] > dist[t] + w`就更新`dist[s]` 	$O(m)$

   就可以确定每个点到起点的最短距离了

**朴素dijkstra算法算法模板**

时间复杂是 O(n<sup>2</sup>+m), n 表示点数，m 表示边数

```cpp
int g[N][N];  // 存储每条边
int dist[N];  // 存储1号点到每个点的最短距离
bool st[N];   // 存储每个点的最短路是否已经确定

// 求1号点到n号点的最短路，如果不存在则返回-1
int dijkstra()
{
    memset(dist, 0x3f, sizeof(dist));
    dist[1] = 0;

    for (int i = 0; i < n - 1; i ++ )
    {
        int t = -1;     // 在还未确定最短路的点中，寻找距离最小的点
        for (int j = 1; j <= n; j ++ )
            if (!st[j] && (t == -1 || dist[t] > dist[j]))
                t = j;

        // 用t更新其他点的距离
        for (int j = 1; j <= n; j ++ )
            dist[j] = min(dist[j], dist[t] + g[t][j]);

        st[t] = true;
    }

    if (dist[n] == 0x3f3f3f3f) return -1;
    return dist[n];
}

memset(g, 0x3f, sizeof(g));
// 自环不存储，重边只计算最短的那条
```

### 堆优化版Dijkstra

稀疏图优化

每次寻找不在S中的距离起点最近的点t需要$O(n^2)$的复杂度，最耗时，因此可以用堆来寻找最小值，但是修改的复杂度就变成了$logn$

找到不在S中的距离最近的点t	$O(n)$

将点t加入到S中去，用t更新其他点的距离	$O(n)$

就是看点x到一号点的距离能否用t到一号点的距离更新，即`dist[s] > dist[t] + w`就更新`dist[s]` 	$O(mlogn)$

**堆优化版dijkstra算法模板**

时间复杂度 O(mlogn), n 表示点数，m 表示边数

```cpp
typedef pair<int, int> PII;

int n;      // 点的数量
int h[N], w[N], e[N], ne[N], idx;       // 邻接表存储所有边
int dist[N];        // 存储所有点到1号点的距离
bool st[N];     // 存储每个点的最短距离是否已确定

void add(int a, int b, int c)
{
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx ++;
}

// 求1号点到n号点的最短距离，如果不存在，则返回-1
int dijkstra()
{
    memset(dist, 0x3f, sizeof dist);
    dist[1] = 0;
    priority_queue<PII, vector<PII>, greater<PII>> heap;
    heap.push({0, 1});      // first存储距离，second存储节点编号

    while (heap.size())
    {
        auto t = heap.top();
        heap.pop();

        int ver = t.second, distance = t.first;	 // ver节点到起点的距离为distance

        if (st[ver]) continue;	// 如果最短距离已经确定了就跳出
        st[ver] = true;

        for (int i = h[ver]; i != -1; i = ne[i])	// ver节点到j节点
        {
            int j = e[i];
            if (dist[j] > distance + w[i])	 // w[i]为ver到j的最短距离
            {
                dist[j] = distance + w[i];
                heap.push({dist[j], j});
            }
        }
    }

    if (dist[n] == 0x3f3f3f3f) return -1;
    return dist[n];
}

memset(dist, 0x3f, sizeof dist);
memset(h, -1, sizeof(h));
add(a, b, c);
```

### Bellman-Ford

迭代n次，每次遍历所有边（从a到b的权重为w的边），`dist[b] = min(dist[b], dist[a] + w)`

第k次迭代表示==不超过k条边的最短距离==

若第n次仍然存在更新，就说明存在负权回路

迭代遍历结束后，必定存在三角不等式$dist[b] \leq dist[a] + w$ ，更新过程被称为松弛操作

若存在负权回路，则无法求最短路

**Bellman-Ford算法算法模板**

时间复杂度 O(nm), n 表示点数，m 表示边数
注意在模板题中需要对下面的模板稍作修改，加上备份数组，详情见模板题。

```cpp
int n, m;       // n表示点数，m表示边数
int dist[N], backup[N];        // dist[x]存储1到x的最短路距离,packup[x]为备份数组

struct Edge     // 边，a表示出点，b表示入点，w表示边的权重
{
    int a, b, w;
}edges[M];

// 求1到n的最短路距离，如果无法从1走到n，则返回-1。
void bellman_ford()
{
    memset(dist, 0x3f, sizeof dist);
    dist[1] = 0;

    // 如果第n次迭代仍然会松弛三角不等式，就说明存在一条长度是n+1的最短路径，由抽屉原理，路径中至少存在两个相同的点，说明图中存在负权回路。
    for (int i = 0; i < n; i ++ )	// 若求不超过k条边，将n改为k
    {
        memcpy(backup, dist, sizeof(dist));
        for (int j = 0; j < m; j ++ )
        {
            int a = edges[j].a, b = edges[j].b, w = edges[j].w;
            dist[b] = min(dist[b], backup[a] + w);
        }
    }
}

if(dist[n] > 0x3f3f3f3f / 2) printf("impossible\n");
else    printf("%d\n", dist[n]);
// 如果在里面判断的画，会有一些最短距离为-1无法输出
```

#### 有边数限制的最短路

> 给定一个 n 个点 m 条边的有向图，图中可能存在重边和自环， **边权可能为负数**。
>
> 请你求出从 1 号点到 n 号点的最多经过 k 条边的最短距离，如果无法从 1 号点走到 n 号点，输出 `impossible`。
>
> 注意：图中可能 **存在负权回路** 。

在迭代的过程中，dist数组会随时改变

备份数组可以在求不超过k条边的最短路的情况下，防止原来的距离被改变，

用备份数组可以保证在这次迭代的时候只用上一次迭代的结果，存储上一次迭代后结果的数组就是备份数组

```cpp
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 510, M = 10010;

int n, m, k, a, b, w;
int dist[N], backup[N];

struct Edge{
  int a, b, w;  
}edges[M];

void bellman_ford()
{
    memset(dist, 0x3f, sizeof(dist));
    dist[1] = 0;
    
    for(int i = 0; i < k; i ++)
    {
        memcpy(backup, dist, sizeof(dist));
        for(int j = 0; j < m; j ++)
        {
            int a = edges[j].a, b = edges[j].b, w = edges[j].w;
            dist[b] = min(dist[b], backup[a] + w);
        }
    }
}

int main()
{
    scanf("%d%d%d", &n, &m, &k);    
    for(int i = 0; i < m; i ++)
    {
        scanf("%d%d%d", &a, &b, &w);
        edges[i].a = a, edges[i].b = b, edges[i].w = w;        
    }
    bellman_ford();
    if(dist[n] > 0x3f3f3f3f / 2) printf("impossible\n");
    else    printf("%d\n", dist[n]);
    return 0;
}
```

### SPFA

只有a的dist变小了，b的dist才会变小，因此可以在迭代中用宽搜做优化

在队列中存入所有变小了的节点，用这些节点来更新后面的节点

当队列不空时，取队头，更新队头的所有出边，更新成功就将其加入队列，

#### spfa求最短路

**spfa 算法（队列优化的Bellman-Ford算法）算法模板**

时间复杂度 平均情况下 O(m)，最坏情况下 O(nm), n 表示点数，m 表示边数

```cpp
int n;      // 总点数
int h[N], w[N], e[N], ne[N], idx;       // 邻接表存储所有边
int dist[N];        // 存储每个点到1号点的最短距离
bool st[N];     // 存储每个点是否在队列中

// 求1号点到n号点的最短路距离，如果从1号点无法走到n号点则返回-1
int spfa()
{
    memset(dist, 0x3f, sizeof dist);
    dist[1] = 0;

    queue<int> q;
    q.push(1);
    st[1] = true;		// 存储当前点是否在队列中

    while (q.size())
    {
        auto t = q.front();
        q.pop();

        st[t] = false;	

        for (int i = h[t]; i != -1; i = ne[i])
        {
            int j = e[i];
            if (dist[j] > dist[t] + w[i])
            {
                dist[j] = dist[t] + w[i];
                if (!st[j])     // 如果队列中已存在j，则不需要将j重复插入
                {
                    q.push(j);
                    st[j] = true;
                }
            }
        }
    }

    return dist[n];
}

int t = spfa();
if (t == 0x3f3f3f3f) puts("impossible");
else printf("%d\n", t);
```

#### spfa判断负环

`dist[x]`存储当前从1号点到x号点的最短路径的长度

`cnt[x]`存储当前最短路边的数量

`dist[x] = dist[t] + w[i], cnt[x] = cnt[t] + 1` 如果`cnt[x] >= n`，又因为我有n条边，就会有n+1个点，就会有点是重复的，就可以判断存在负环

**spfa判断图中是否存在负环算法模板**

时间复杂度是 O(nm), n表示点数，m 表示边数

```cpp
int n;      // 总点数
int h[N], w[N], e[N], ne[N], idx;       // 邻接表存储所有边
int dist[N], cnt[N];        // dist[x]存储1号点到x的最短距离，cnt[x]存储1到x的最短路中经过的点数
bool st[N];     // 存储每个点是否在队列中

// 如果存在负环，则返回true，否则返回false。
bool spfa()
{
    // 不需要初始化dist数组
    // 原理：如果某条最短路径上有n个点（除了自己），那么加上自己之后一共有n+1个点，由抽屉原理一定有两个点相同，所以存在环。

    queue<int> q;
    for (int i = 1; i <= n; i ++ )
    {
        q.push(i);
        st[i] = true;
    }

    while (q.size())
    {
        auto t = q.front();
        q.pop();

        st[t] = false;

        for (int i = h[t]; i != -1; i = ne[i])
        {
            int j = e[i];
            if (dist[j] > dist[t] + w[i])
            {
                dist[j] = dist[t] + w[i];
                cnt[j] = cnt[t] + 1;
                if (cnt[j] >= n) return true;       // 如果从1号点到x的最短路中包含至少n个点（不包括自己），则说明存在环
                if (!st[j])
                {
                    q.push(j);
                    st[j] = true;
                }
            }
        }
    }

    return false;
}
```

### Floyd

**floyd算法算法模板**

时间复杂度是 O(n<sup>3</sup>), n 表示点数

```cpp
初始化：
    for(int i = 1; i <= n; i ++ )
        for(int j = 1; j <= n; j ++ )
            if(i == j) d[i][j] = 0;
            else d[i][j] = 0x3f3f3f3f;

// 算法结束后，d[a][b]表示a到b的最短距离
void floyd()
{
    for (int k = 1; k <= n; k ++ )
        for (int i = 1; i <= n; i ++ )
            for (int j = 1; j <= n; j ++ )
                d[i][j] = min(d[i][j], d[i][k] + d[k][j]);
}

for(int i = 0; i < m; i ++)
{
    int a, b, c;
    scanf("%d%d%d", &a, &b, &c);
    d[a][b] = min(d[a][b], c);
}

if(d[x][y] > 0x3f3f3f3f / 2)    printf("impossible\n");
else    printf("%d\n", d[x][y]);
```

## 最小生成树

给定一张边带权的无向图 $G=(V,E)$，其中 $V$ 表示图中点的集合，$E$ 表示图中边的集合，$n=|V|$，$m=|E|$。

由 $V$ 中的全部 $n$ 个顶点和 $E$ 中 $n−1$ 条边构成的无向连通子图被称为 $G$ 的一棵生成树，其中边的权值之和最小的生成树被称为无向图 $G$ 的最小生成树。

对应无向图

* Prim算法
  * 朴素版Prim算法	$O(n^2)$    稠密图
  * 堆优化版Prim算法    $O(mlogn)$    不常用

* Kruskal算法    $O(mlogm)$  稀疏图

### 朴素Prim

集合S表示当前已经在连通块中的所有点

1. 初始化所有点到集合的距离为正无穷
2. n次迭代，每次迭代先找到不在集合中的距离最小的点t，用t来更新到其他点到==集合==的距离，将t加到集合中去

**朴素版prim算法算法模板**

时间复杂度是 O(n<sup>2</sup>+m), n 表示点数，m 表示边数

```cpp
int n;      // n表示点数
int g[N][N];        // 邻接矩阵，存储所有边
int dist[N];        // 存储其他点到当前最小生成树的距离
bool st[N];     // 存储每个点是否已经在生成树中


// 如果图不连通，则返回INF(值是0x3f3f3f3f), 否则返回最小生成树的树边权重之和
int prim()
{
    memset(dist, 0x3f, sizeof dist);

    int res = 0;		// 存储最小生成树中边的最小长度和
    for (int i = 0; i < n; i ++ )
    {
        int t = -1;
        for (int j = 1; j <= n; j ++ )
            if (!st[j] && (t == -1 || dist[t] > dist[j]))
                t = j;		// t存储当前距离最小的点

        if (i && dist[t] == 0x3f3f3f3f) return 0x3f3f3f3f;

        if (i) res += dist[t];
        st[t] = true;

        for (int j = 1; j <= n; j ++ ) dist[j] = min(dist[j], g[t][j]);
    }

    return res;
}

// 有重边肯定保留较短的那一条
memset(g, 0x3f, sizeof(g));
g[a][b] = g[b][a] = min(g[a][b], c);	// 无向图的存储

int  t = prim();
if(t == 0x3f3f3f3f)	printf("impossible\n");
else	printf("%d\n", t);
```

### Kruskal

1. 将所有边按照权重从小到大进行排序
2. 枚举每条a到b的权重为c的边，若a和b不连通，就将这条边加到集合中去

**Kruskal算法算法模板**

时间复杂度是 O(mlogm), n 表示点数，m 表示边数

```cpp
int n, m;       // n是点数，m是边数
int p[N];       // 并查集的父节点数组

struct Edge     // 存储边
{
    int a, b, w;

    bool operator< (const Edge &W)const
    {
        return w < W.w;
    }
}edges[M];

int find(int x)     // 并查集核心操作
{
    if (p[x] != x) p[x] = find(p[x]);
    return p[x];
}

int kruskal()
{
    sort(edges, edges + m);

    for (int i = 1; i <= n; i ++ ) p[i] = i;    // 初始化并查集

    int res = 0, cnt = 0;
    for (int i = 0; i < m; i ++ )
    {
        int a = edges[i].a, b = edges[i].b, w = edges[i].w;

        a = find(a), b = find(b);
        if (a != b)     // 如果两个连通块不连通，则将这两个连通块合并
        {
            p[a] = b;
            res += w;
            cnt ++ ;
        }
    }

    if (cnt < n - 1) return 0x3f3f3f3f;
    return res;
}

edges[i] = {a, b, w};

int t = kruskal();
if(t == 0x3f3f3f3f) printf("impossible\n");
else    printf("%d\n", t);
```

## 二分图

二分图就是可以把所有点划分到两个集合，使得集合内部没有边，所有边都存在于两个集合之间

一个图是二分图当且仅当图中不含奇数环（一个环中边的数量是奇数）

* 染色法判定二分图    $O(m+n)$
* 匈牙利算法    最坏情况$O(nm)$，实际运行时间一般远小于$O(mn)$

### 染色法判定二分图

`for(int i = 1; i <= n; i ++)`   `if i未染色`	`dfs(i, 1)` 将i号点染成1号颜色

**染色法判别二分图算法模板**

时间复杂度是 O(n+m), n 表示点数，m 表示边数

```cpp
int n, m;      // n表示点数
int h[N], e[M], ne[M], idx;     // 邻接表存储图
int color[N];       // 表示每个点的颜色，-1表示未染色，0表示白色，1表示黑色

void add(int a, int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
}

// 参数：u表示当前节点，c表示当前点的颜色
bool dfs(int u, int c)
{
    color[u] = c;
    for (int i = h[u]; i != -1; i = ne[i])
    {
        int j = e[i];
        if (color[j] == -1)
        {
            if (!dfs(j, !c)) return false;
        }
        else if (color[j] == c) return false;
    }

    return true;
}

bool check()
{
    memset(color, -1, sizeof(color));
    bool flag = true;
    for (int i = 1; i <= n; i ++ )
        if (color[i] == -1)
            if (!dfs(i, 0))
            {
                flag = false;
                break;
            }
    return flag;
}

memset(h, -1, sizeof(h));
```

### 匈牙利算法

给定一个二分图，其中左半部包含 $n_1$ 个点（编号 $1∼n_1$），右半部包含 $n_2$ 个点（编号 $1∼n_2$），二分图共包含 $m$ 条边。

二分图的匹配：给定一个二分图 $G$，在 $G$ 的一个子图 $M$ 中，$M$ 的边集 $\{E\}$ 中的任意两条边都不依附于同一个顶点，则称 $M$ 是一个匹配。

二分图的最大匹配：所有匹配中包含边数最多的一组匹配被称为二分图的最大匹配，其边数即为最大匹配数。

任意一条边的两个端点都不可能在同一部分中。求出二分图的最大匹配数。

待嫁闺中，据为己有；名花有主，求他放手。

左半部点集中的点 $u$ 和右半部点集中的点 $v$ 之间存在一条边

只需要存左半边到右半边的边即可

**匈牙利算法算法模板**

时间复杂度是 O(nm), n表示点数，m 表示边数

```cpp
int n1, n2, m;     // n1表示第一个集合中的点数，n2表示第二个集合中的点数
int h[N], e[M], ne[M], idx;     // 邻接表存储所有边，匈牙利算法中只会用到从第一个集合指向第二个集合的边，所以这里只用存一个方向的边
int match[N];       // 存储第二个集合中的每个点当前匹配的第一个集合中的点是哪个
bool st[N];     // 表示第二个集合中的每个点是否已经被遍历过

bool find(int x)
{
    for (int i = h[x]; i != -1; i = ne[i])
    {
        int j = e[i];
        if (!st[j])
        {
            st[j] = true;
            if (match[j] == 0 || find(match[j]))
            {
                match[j] = x;
                return true;
            }
        }
    }
    return false;
}

memset(h, -1, sizeof(h));

// 求最大匹配数，依次枚举第一个集合中的每个点能否匹配第二个集合中的点
int res = 0;
for (int i = 1; i <= n1; i ++ )
{
    memset(st, false, sizeof(st));
    if (find(i)) res ++ ;
}

printf("%d\n", res);
```

