# 搜索专题
## 深度优先搜索(DFS)+回溯

`DFS`是一种搜索的策略，简单来说就是一条路走到黑。当走到尽头之后就回退到上一个结点，继续一条路走到黑。回退的过程就叫做**回溯**。
`DFS`一般采用**递归**的方式实现。<!--more-->

> 对递归的详细讲解会在以后出一个独立的专题，小伙伴们可以期待一下噢！
>
> `DFS`可以用一个递归搜索树来形象描述，那这棵树长什么样子呢，小伙伴们继续往下看。
> <img src="https://img-blog.csdnimg.cn/b46f5fe9bde34fbd907b714c6ea1284c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5omT6JOd5qGl5p2v55qE6YCa5L-h5Lq6,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center" alt="在这里插入图片描述" style="zoom: 33%;" />
> 事实上对所有的合法`DFS`求解过程都可以把它画成树的形式，死胡同就相当于叶子结点。分岔口就相当于非叶子结点。  
>
> 对某个DFS类型的题目，不妨把一些状态作为树的结点，问题就变得很直观了。
>
> `DFS`搜索的顺序是A-B-D-E-C-F。从结点A出发，它有两条路可以走，我们选择最左边那一条，到达结点B，结点B有两个分支，选择最左边那个，到达结点D。结点D没有路可走了也就是到达死胡同了，返回上一个结点B，也就是回溯的过程。B还有一条路可走，到达结点E。同理结点E回溯到结点B，结点B没有分支了，回溯到上一个结点A，结点A还有一个分支可以走，到达结点C,结点C有一个分支到达结点F，结点F是死胡同。至此所有的结点都访问了，搜索结束。
> 其实DFS的搜索顺序和树的先序遍历是一样的。树的先序遍历就是根左右。

从递归树我们就能看出`DFS`的时间复杂度是很高的，是$O(2^n)$。所以我们常说暴力搜索嘛，就是因为它时间复杂度太高了，当数据很大时很容易就`TLE(`超时)的。同时我们还能看出`DFS`会走遍所有的路径，并且走到死胡同就代表一条完整的路径形成。因此深度优先搜索是一种枚举所有路径，遍历所有情况的搜索策略。

***

## DFS模板

> 在备战蓝桥杯的过程中记住一些算法模板还是非常重要滴。

```cpp
#include <iostream>
using namespace std;
bool check()
{
	...
}
void dfs()
{
	if (满足边界条件)
	{
		
		return;
	}
	for (int i = 0; i < 可扩展的路径数; i++)
	{
		if (check())
		{
			修改现场;
			dfs(下一种情况);
			还原现场;
		}
	}
}	
```

> 模板不是固定不变的，我们要根据题目灵活地运用它。



***

## DFS经典例题

### 模板题——迷宫问题

<img src="https://img-blog.csdnimg.cn/76bfafbf85ae400bbf980124689c8b74.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5omT6JOd5qGl5p2v55qE6YCa5L-h5Lq6,size_20,color_FFFFFF,t_70,g_se,x_16" alt="在这里插入图片描述" style="zoom:67%;" />

**题目分析**

> 迷宫问题是拿来练习DFS与BFS很经典的题目。迷宫问题有很多种问法，比如迷宫从起点到终点有没有路径，有几条，最短路径是多少。

求从起点到终点的方案数显而易见也是要用`DFS`，遍历所有的情况。我们要考虑这样一个问题，迷宫里的某点`(x,y)`是否要被访问呢。当这点是障碍物肯定不能访问，该点不在迷宫里面也不能访问，该点访问过了那就不能访问了。(题目中有每个方格最多经过一次)。因此我们需要一个`check()`函数来判断某一点是否合法。合法我们就去访问该点。

> 其实这个过程就是一个剪枝的过程，根据题目条件限制，剪掉一些不可能存在解的分支。

另外我们该如何知道某点是障碍点呢，可以设置一个map数组来表示该迷宫。

* 当`map[x][y]==1`时表示该点是障碍点 
 * `map[x][y]==0`表示该点是正常点

**AC代码**

```cpp
#include <iostream>
using namespace std;

const int N = 100;

int dx[4] = {0, 0, -1, 1};
int dy[4] = {1, -1, 0, 0};//方向数组技巧
int n, m, T;//n行m列T障碍总数
int ans;//记录方案总数
int sx, sy, fx, fy, l, r;//起点坐标(sx,sy)终点坐标(fx,fy)障碍点坐标(l,r)
bool visited[N][N];//记录某点是否被访问过
int map[N][N];//map[i][j] == 1表示是障碍

bool check(int x, int y)//check某点是否合法
{
    if (x < 1 || x > n || y < 1 || y > m) return false;//该点出界不合法
    if (map[x][y]) return false;//该点是障碍点不合法
    if (visited[x][y]) return false;//该点被访问过不合法
    return true;//其他情况访问合法
}

void dfs(int x, int y)//dfs维护点的坐标参数
{
    if (x == fx && y == fy)//满足边界条件，到达终点
    {
        ans++;//方案数+1
        return;
    }
    for (int i = 0; i < 4; i++)//枚举四个方向
    {
        int newx = x + dx[i];
        int newy = y + dy[i];
        if (check(newx, newy))//该点合法
        {
            visited[x][y] = true;//将(x,y)设置成已访问,修改现场
            dfs(newx, newy);//dfs下一个点
            visited[x][y] = false;//回溯,恢复现场
        }
    }
}

int main()
{
    cin >> n >> m >> T;
    cin >> sx >> sy >> fx >> fy;
    while(T--)
    {
        cin >> l >> r;
        map[l][r] = 1;
    }
    dfs(sx, sy);//从起点开始搜索
    cout << ans << endl;
    return 0;
}
```

> 小伙伴们每道例题都要好好看一下AC代码噢，上面有很详细的注释。

> 这道题还可以优化下空间，可以只用maze数组来表示迷宫，同时记录迷宫内的某点是否访问过。具体见下面的代码。

```cpp
//空间优化
#include <iostream>
using namespace std;

const int N = 100;

int dx[4] = {0, 0, -1, 1};
int dy[4] = {1, -1, 0, 0};//方向数组
int n, m, T;
int ans;
int sx, sy, fx, fy, l, r;
//bool visited[N][N];
int map[N][N];//map[i][j] == 1表示是障碍
                                                                                       bool check(int x, int y)//判断某点是否可以访问
{
    if (x < 1 || x > n || y < 1 || y > n) return false;
    if (map[x][y] || map[x][y] == 3 ) return false;//该点是障碍点或已经访问过，不能访问
    //if (visited[x][y]) return false;
    return true;
}

void dfs(int x, int y)
{
    if (x == fx && y == fy)
    {
        ans++;
        return;
    }
    for (int i = 0; i < 4; i++)
    {
        int newx = x + dx[i];
        int newy = y + dy[i];
        if (check(newx, newy))
        {
            map[x][y] = 3;//标记成已访问
            dfs(newx, newy);
            //点(x,y)能访问说明它不是障碍点所以回溯要让map[x][y]=0
            //而不是map[x][y]=1
            map[x][y] = 0;
        }
    }
}
int main()
{
    cin >> n >> m >> T;
    cin >> sx >> sy >> fx >> fy;
    while(T--)
    {
        cin >> l >> r;
        map[l][r] = 1;
    }
    dfs(sx, sy);
    cout << ans << endl;
    return 0;
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/f0d98badf856489392bc3a7bfad24d98.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5omT6JOd5qGl5p2v55qE6YCa5L-h5Lq6,size_20,color_FFFFFF,t_70,g_se,x_16)

### 01背包问题(DFS暴力搜索)

<img src="https://img-blog.csdnimg.cn/597a9a9e51c24576a62a7ad2d74e9b21.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5omT6JOd5qGl5p2v55qE6YCa5L-h5Lq6,size_20,color_FFFFFF,t_70,g_se,x_16" alt="背包问题" style="zoom:67%;" />

**题目分析**



> 01背包问题是一个很经典的问题，它有多种解法。DFS是其中一种，在学习动态规划的时候还会提到它噢。
>
> 第i件物品无非就是选和不选两种情况，在搜索的过程中DFS函数必须要记录当前处理的物品编号`index`，当前背包的容量`sumW`，当前的总价值`sumC`。

* 当不选第`index`个物品时，那么`sumW`,`sumC`是不变的，接着处理第`index+1`个物品，也就是`DFS(index+1, sumW, sumC)`。
* 当选择第`index`个物品时，`sumW`变成`sumW+w[index]`，`sumC`变成`sumC+v[index]`，接着处理第`index+1`个物品，也就是`DFS(index+1, sumW+w[index],sumC+v[index])`。边界条件也就是把最后一件物品也处理完了，即`index=n`（注意默认index从0开始）。

当一条分支结束了该干什么呢，很简单呀就是判断该分支最终满不满足总重量不大于背包容量。即`sumW<=v`。满足的话我们就更新价值`maxvalue`，即`maxvalue=max(maxvalue,sumC)`。

**AC代码**

```cpp
//01背包问题的dfs版本
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 10010;
int n, v, maxvalue;
int w[N], c[N];
//DFS维护三个参数，正在选第index个物品，当前的总体积，当前的总价值
void dfs(int index, int sumW, int sumC)
{
    if (index == n)
    {
        if (sumW <= v)
        {
            maxvalue = max(maxvalue, sumC);
        }
        return;
    }
    dfs(index + 1, sumW, sumC);//不选第index个物品
    dfs(index + 1, sumW + w[index], sumC + c[index]);//选第index个物品
}

int main()
{
    cin >> n >> v;
    for (int i = 0; i < n; i++)
    {
        cin >> w[i] >> c[i];
    }
    dfs(0, 0, 0);//开始时对第1件物品进行选择，此时背包重量0价值也是0
    cout << maxvalue << endl;
    return 0;
}
```

> 当然了01背包问题如果用`DFS`来解决的话，当数据比较大时，是不能`AC`的。这道题会超出时间限制。即便经过剪枝也是无能为力的。

![在这里插入图片描述](https://img-blog.csdnimg.cn/b9204460d99f411dae0c590f14f4196a.png)


### 回溯——[USACO1.5]八皇后

<img src="https://img-blog.csdnimg.cn/e0392de0426f4359962c51f77cf5484f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5omT6JOd5qGl5p2v55qE6YCa5L-h5Lq6,size_20,color_FFFFFF,t_70,g_se,x_16" alt="在这里插入图片描述" style="zoom:67%;" />
<img src="https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/d9ae3a9ad53f44418de0d2a2f06c8c4b.png" alt="在这里插入图片描述" style="zoom:67%;" />

**题目分析**

>八皇后问题是学习回溯很经典的例题，这道题是八皇后问题的扩展，N皇后问题。也就是棋盘的大小是任意的。

这道题`DFS`的思路还是比较清晰的，每行有且只有一个棋子，那么`DFS`可以记录下当前处理的是第几行的棋子。假设当前处理的是第`i`行的棋子，那么要枚举处在该行的棋子位置，判断哪个是合法的。
什么样的位置算是合法的呢，这个位置的列还有左对角线，右对角线位置都不能有棋子。那么又该如何表示这些位置呢？我们采用一维数组来分别表示列，左对角线，右对角线。列很好表示就是`b[j]`，左对角线我们可以发现行减去列的绝对值是恒定的，即`c[i-j+n]`,右对角线行加列是恒定的。即`d[i+j]`。

>小伙伴们可以自己画画图看一看噢~
>
>当某位置放置了棋子，那么就将和它同列同对角线的位置都取为1，即`b[j]=1`,`c[i-j+n]=1`,`d[i+j]=1`。

**AC代码**

```cpp
#include <iostream>
using namespace std;

int a[100], b[100], c[100], d[100];//a[]储存解b列c左斜d右斜
int n;//棋盘大小
int ans;

bool check(int i, int j)//检查(i,j)是否合法
{
    if (!b[j] && !c[j - i + n] && !d[j + i]) return true;//b[j],c[i-j+n],d[i+j]为0说明该点可以放置棋子。
    return false;
}

void dfs(int i)//dfs第i行棋子
{   //边界条件
    if (i > n)//dfs完所有的棋子
    {
     	ans++;
        if (ans <= 3)//只要前三个解
        {
            for (int i = 1; i <= n; i++)//输出解
            {
                cout << a[i] << ' ';
                
            }
            cout << endl;  
        }
        return;     
    }
    
        for (int j = 1; j <= n; j++)//枚举一行的所有位置
        {	//(i,j)点满足放棋子的条件，我们就把棋子放在(i,j)点
            if (check(i, j))
            {
                a[i] = j;//把满足解的列号存在a[i]中
                b[j] = 1;//修改现场
                c[ j - i + n] = 1;
                d[j + i] = 1;
                dfs(i + 1);//处理下一行的棋子
                b[j] = 0;//回溯，恢复现场
                c[j - i + n] = 0;
                d[j + i] = 0;
            }
        }
}
int main()
{
    cin >> n;
    dfs(1);
    cout << ans << endl;
    return 0;
}
```

![在这里插入图片描述](https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/c37e19018b064ee88491028af5bec829.png)

### 递归实现排列型枚举

[题目链接](https://www.acwing.com/problem/content/96/)

<img src="https://img-blog.csdnimg.cn/93f1e3da7444452c827100778284df39.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5omT6JOd5qGl5p2v55qE6YCa5L-h5Lq6,size_20,color_FFFFFF,t_70,g_se,x_16" alt="在这里插入图片描述" style="zoom:67%;" />

**题目分析**

>DFS可以用来处理排列组合问题。
>
>对于全排列问题我们可以这样想，第1个位置可以放`1~n`任意一个数，第2个位置可以放除了放在第1个位置的数以外的任何一个数，以此类推。因此我们可以画出一个递归搜索树，用`map[]`来表示储存当前排列。`DFS`函数要记住当前处理的是第`index`个位置，从1到n进行遍历，看看这个数是否可以放在第`index`个位置，需要有一个判重数组`hashtable[x]`来记录`x`是否在排列里面。

**AC代码**

```cpp
#include <iostream>
using namespace std;

const int N = 11;
//map[N]储存当前的排列，hashtable[N]判断某个数是否在排列里
int map[N];
bool hashtable[N];面。
int n;

void dfs(int index)//当前填第index位置
{
    if (index == n + 1)//已经处理完1~n个位置
    {
        for (int i = 1; i <= n; i++)//输出当前排列
        {
            cout << map[i] << ' ';
        }
        cout << endl;
        return;
    }
    for (int i = 1; i <= n; i++)//枚举1~n
    {
        if (hashtable[i] == false)//当i这个数没有填在map中
        {
            map[index] = i;//第index位置填入i这个数
            hashtable[i] = true;//记i在当前排列
            dfs(index + 1);//处理第index+1位置
            //处理完map[index]=i的子问题，恢复现场
            hashtable[i] = false;
            map[index] = 0;
        }
    }
}
int main()
{
    cin >> n;
    dfs(1);//从1这个数开始搜索
    return 0;
}
```

![在这里插入图片描述](https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/de6462dec44d47daa0afa1023d8a0c89.png)

### 递归实现组合型枚举

<img src="https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/e84e6dee11d64d5688a7e07c419aa54b.png" alt="在这里插入图片描述" style="zoom: 67%;" />
<img src="https://img-blog.csdnimg.cn/36278db485514eff94f4487f54653173.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5omT6JOd5qGl5p2v55qE6YCa5L-h5Lq6,size_20,color_FFFFFF,t_70,g_se,x_16" alt="在这里插入图片描述" style="zoom:67%;" />



**题目分析**



组合和排列的最大区别就是组合不需要考虑顺序，也就是说`123`和`132`是一样的。
由于题目要求同一行的数必须按升序排序，因此可以让`DFS`函数记录当前处理的第`index`个位置，可以选的最小数`start`。

>虽然参数变成了两个，但是不需要判重数组了。

`DFS`的思路是这个样子的，假设当前处理的是第`index`个位置，这个位置可以放置`start~n`其中任意一个数。接着处理第`index+1`个位置，这个位置可以放置的最小数是前一位数的下一个数。即`i+1~n`。


**AC代码**

```cpp
#include <iostream>
using namespace std;

const int N = 30;

int map[N];//存储解
int n, m;
//dfs记录当前处理的第index个位置，可以选的最小数start
void dfs(int index, int start)
{
    if (index > m)//m个位置都处理完了
    {
        for (int i = 1; i <= m; i++)//输出方案
        {
            cout << map[i] << " ";
        }
        cout << endl;
        return;
    }
    //第index个位置可以选择start~n里面的数
    for (int i = start; i <= n; i++)
    {
        map[index] = i;
        //处理index+1位置，能选择的最小数是前面选择的数+1
        dfs(index + 1, i + 1);
        //处理完第index位置放置i这个数的子问题后回溯。
        map[index] = 0;
      
    }
}

int main()
{
    cin >> n >> m;
    dfs(1, 1);
    return 0;
}
```

![在这里插入图片描述](https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/353f2858c0d24affbfb1c9307427a79c.png)

***

## 剪枝思想

前面也提到过剪枝的概念，现在详细说下剪枝这种东西。我们知道`DFS`时间复杂度是很高的，如果不进行一些优化的话，在数据很大的情况下很容易就爆`TLE`。这个时候我们就可以用剪枝这种东西来优化啦~
在进行`DFS`的过程中对某条可以确定不存在解的分支采用直接剪短的策略。这样会大大降低计算量。一个搜索树剪掉一些分支就形象的叫它剪枝了。
==剪枝一般都是利用题目的限制条件来确定哪些分支是不可能存在解的。==

***

## 剪枝思想在DFS中的应用

小伙伴们来回忆下01背包问题，我们是在选择完所有物品后才进行判断，看看背包总体积是否超过背包容量。是不是可以在选择的过程中就加入这一判断，这样就可以减少很多的计算量。当选第`index`件物品时，如果选上它那么背包的总体积就超出背包容量，我们一定不能选了。此时选第`index`件物品的分支就不可能存在解，直接剪掉。

```cpp
//DFS维护三个参数，正在选第index个物品，当前的总体积，当前的总价值
void dfs(int index, int sumW, int sumC)
{
    if (index == n)//选择完所有的物品，最终的sumW一定不大于v
    {
        maxvalue = max(maxvalue, sumC);
        return;
    }
    dfs(index + 1, sumW, sumC);//不选第index个物品
    if (sum + w[index] <= v)//剪枝
    {   //选第index个物品
    	dfs(index + 1, sumW + w[index], sumC + c[index]);
    } 
}
```

小伙伴们再来回忆下前面的组合型枚举问题，我们也可以用到剪枝来优化。可以看出要是剩下的数全都用上也满足不了一共m个数的要求，那么我们直接可以结束这个分支了。即`n - start + index < m`。

```cpp
void dfs(int index, int start)
{
	if (n - start + index < m) return;//剪枝
    if (index > m)//m个位置都处理完了
    {
        for (int i = 1; i <= m; i++)//输出方案
        {
            cout << map[i] << " ";
        }
        cout << endl;
        return;
    }
    //第index个位置可以选择start~n里面的数
    for (int i = start; i <= n; i++)
    {
        map[index] = i;
        //处理index+1位置，能选择的最小数是前面选择的数+1
        dfs(index + 1, i + 1);
        //处理完第index位置放置i这个数的子问题后回溯。
        map[index] = 0;
      
    }
}

```

> 通过下表我们能看出剪枝之后的运行速度确实快了不少。
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/3f184725aed44b37bdb9b40924995a4b.png)

***

## 广度优先搜索（BFS）

>深度优先搜索会优先考虑搜索的深度，就是找不到答案不回头。 当答案在解答树中比较稀疏的时候，DFS会陷入过深的情况，搜索很多无效状态，一时半会找不到解，这时候我们就可以用BFS啦~==在连通性，最短路问题中常优先考虑BFS==。<!--more-->
>
>`BFS`和`DFS`搜索的顺序是不一样的。`DFS`前面已经讲到过，是以深度为第一关键词的，也就是说从某个结点出发总是先选择其中一个分支前进，而不管其他的分支，直到碰到死胡同才停止。然后回溯到上一个结点选择其他的分支。
>而`BFS`搜索的顺序是这样的，从某个结点出发，总是先依次访问这个结点能直接到达的所有结点，然后再访问这些结点所能直接到达的所有结点，依次类推，直到所有的结点都访问完毕。
>下面呢我们看一个迷宫，小伙伴们可能会对`BFS`有一个清晰的认识。
><img src="https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/0b2c88445f9746bd8a91f1c198c15bcd.png" alt="在这里插入图片描述" style="zoom:67%;" />
>`BFS`搜索的顺序是这个样子滴~首先从起点`A`出发，它能到达的所有结点有`B`和`C`（第二层），因此接下来去访问`B`，`B`能直接到达`D`和`E`（第三层），接下来访问`C`，`C`能直接到达`F`和`G`（第三层）。第二层访问结束，接下来访问`D`，`D`能直接到达`I`和`G`和`H`（第四层）。接下来访问`E`，`E`能直接到达`M`和`L`和`K`（第四层），接着访问`F`，`F`是死胡同。接下来访问`G`，`G`是终点，搜索结束。
>至于那些未访问过的结点，我们就不用去管它了。

可以看出层数就是从结点A出发到达其他结点的最小步数。==广度优先搜索会优先考虑每种状一个专题态的和初始状态的距离，也就是与初始状态越接近的就会越先考虑。这就能保证一个状态被访问时一定是采用的最短路径。==

>其实DFS和BFS都可以从树的角度去分析。
><img src="https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/1b178bcc540c44758a7c0b3504204c0f.png" alt="在这里插入图片描述" style="zoom:67%;" />
>`DFS`相当于树的先序遍历，`BFS`相当于树的层次遍历。
>`DFS`搜索顺序是`123456 ` ，`BFS`搜索顺序是`125346`。
>相信小伙伴们很容易就能看出来~

***

## 用队列来实现BFS（模板）

`BFS`的搜索过程很像一个队列，队列的特点就是先进先出。谁入队早就先访问谁。
我们简单的来分析一下队列模拟的过程~

 - 先把起点`A`入队，取出队首`A`，将`A`直接到达的`B`和`C`入队。队列元素有{`B`，`C`}。
 - 再将队首`B`取出，将`B`直接到达的`D`和`E`入队。此时队列元素有{`C`，`D`，`E`}。
 - 再将队首`C`出队，将`C`直接到达的`F`和`G`入队。此时队列元素有{`D`，`E`，`F`，`G`}。
 - 再将队首`D`出队，将`D`直接到达的`I`和`J`和`H`入队。此时队列元素有{`E`，`F`，`G`，`I`，`J`，`H`}。
 - 再将队首`E`出队，将`E`直接到达的`M`和`L`和`K`入队。此时队列元素有{`F`，`G`，`I`，`J`，`H`，`M`，`L`，`K`。
 - 再将队首`F`出队，`F`没有直接相连的新节点，此时队列元素有{`G`，`I`，`J`，`H`，`M`，`L`，`K`}。
 - 再将`G`出队，找到终点，算法结束。

下面给出`BFS`的模板。

>还是那句话多记些模板对比赛是很有帮助的噢~

```cpp
void bfs()
{
	queue<int> q;//一般用stl库中的queue来实现队列比较方便
	q.push(起点S);//将初始状态入队
	标记初始状态已入队。
	while(!q.empty())//队列不为空就执行入队出队操作
	{
		top = q.front();//取出队首
		q.pop();//队首出队
		for (枚举所有可扩展的状态)
		{
			if (check())//状态合法
			{
				q.push(temp);//状态入队
				标记成已入队。
			}
			
		}
	}
```

对模板做几点说明

  1. 建议大家用`stl`中的`queue`表示队列。操作方便。头文件要加`#include<queue>`
  2. 要区分`front()`和`pop()`的区别,`front`的返回值是队首元素，而`pop`没有返回值。
  3. 我们常用一个新变量来储存队首元素，以便在它出队后方便下面的操作。
     `top = q.front()`。

还有一个问题，我们在使用`BFS`时会设一个`inq`数组记录结点有没有入队，为什么不是记录结点有没有访问呢，不知道小伙伴们想没想过这个问题。
区别在于，设置成结点是否已访问，会导致有些点在队列中但是还没有被访问，由于其他结点可以到达它而将这个结点入队，导致很多结点重复入队，这是很不明智的。


> 以后也会专门出一篇讲解比赛中常用的`STL`标准模板库。
> 上面对`BFS`的讲解参考胡凡《算法笔记》并补充了自己对`BFS`的理解。

***

## BFS经典例题

### 模板题——迷宫最短路

<img src="https://img-blog.csdnimg.cn/c246c1cf991841aba4edc48ba8f4781a.png#pic_center" alt="在这里插入图片描述" style="zoom: 80%;" />
<img src="https://img-blog.csdnimg.cn/83334f78d36a4bba82a18100494088a4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5omT6JOd5qGl5p2v55qE6YCa5L-h5Lq6,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center" alt="在这里插入图片描述" style="zoom:67%;" />

**题目分析**



该题是要求从起点到终点的最小步数。我们可以有两种解决方案，`DFS`和`BFS`。

 - 可以`DFS`所有从起点到终点的所有路径，然后取路径最短的那条。
 - `BFS`是很理所当然的，因为求的是最小步数，涉及==最短问题==。

我们先来看一下`BFS`该如何解决这个问题。因为是板子题嘛直接照着模板敲就好了~

 **AC代码（BFS）**

```cpp
//bfs 广度优先搜索
//迷宫问题 求解最短路径
#include <iostream>
#include <queue>

using namespace std;

const int N = 100;
int n, m;//n行m列
char maze[N][N];//迷宫
bool inq[N][N];//记录位置(x,y)是否入队
int x[4] = {-1, 1, 0, 0};//增量数组
int y[4] = {0, 0, -1, 1};

struct node
{
    int x, y;
    int step;
}s, t, Node;//s起点 t终点 Node临时节点

bool test(int x, int y)//判断位置(x,y)是否有效
{ //以下三种情况不能入队
    if (x >= n || x < 0 || y >= m || y < 0) return false;//出界
    if (maze[x][y] == '*') return false;//墙壁
    if (inq[x][y] == true) return false;//已入队
    return true;//其余情况可以将该点入队
}

int bfs()
{
    queue<node> q;//定义队列
    q.push(s);//将起点入队
    while(!q.empty())//队列不为空执行
    {
        node top = q.front();//取出队首元素
        q.pop();//出队
        //inq[s.x][s.y] = true;//将起点设为已入队
        if (top.x == t.x && top.y == t.y)//到达终点
        {
            return top.step;返回终点的层数，即最少步数
        }
        for (int i = 0; i < 4; i++)//枚举四个方向
        {	
            int newX = top.x + x[i];
            int newY = top.y + y[i];
            if (test(newX, newY))//下一个点合法
            {
                Node.x = newX; Node.y = newY;
                Node.step = top.step + 1;//层数加1
                q.push(Node);//将该点入队//或q.push((Node){newX,newY});  
                inq[newX][newY] = true;//标记成已入队
            }
        }
    }
    return 0;
}

int main()
{
    cin >> n >> m;
    for (int i = 0; i < n; i++)
    {
        getchar();//过滤掉每行的换行，不加这句是不能AC的
        for (int j = 0; j < m; j++)
        {
            maze[i][j] = getchar();//读入数据
        }
    }
    cin >> s.x >> s.y >> t.x >> t.y;
    s.step = 0;
    cout << bfs() << endl;
    return 0;
}
```

我们再来看一下如何用`DFS`来解决这个问题。用`DFS`搜索出所有可以从起点到终点的路径，并记录步数。DFS函数记录当前搜索的点的坐标`(x,y)`和所用步数`step`。用一个变量`minsize`更新最小步数`minsize = min(minsize, step)`。其余的操作其实和上一篇文章的迷宫问题求方案数是差不多的啦。小伙伴们还是不懂的话可以去看前一篇文章呀~

**AC代码（DFS）**

```cpp
#include <iostream>
#include <map>
using namespace std;
//用PII来代替，书写方便，记得加头文件#include <map>
typedef pair<int, int> PII;
const int N = 100;
char maze[N][N];
int minsize = 10000;//更新最少步数
int n, m;
int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};
bool visited[N][N];//标记某点是否已访问过
PII s, t;//起点，终点

bool check(int x, int y)//判断位置(x,y)是否有效
{
    if (x >= n || x < 0 || y >= m || y < 0) return false;
    if (maze[x][y] == '*') return false;
    if (visited[x][y] == true) return false;
    return true;
}

void dfs(int x, int y, int step)
{
    if (x == t.first && y == t.second)//到达终点
    {
        minsize = min(minsize, step);/更新最少步数
        return;
    }
    for (int i = 0; i < 4; i++)
    {
        int newx = x + dx[i];
        int newy = y + dy[i];
        if (check(newx, newy))//下一个点合法
        {
            visited[x][y] = true;//修改现场
            dfs(newx, newy, step + 1);//搜索下一个点
            visited[x][y] = false;//还原现场
        }  
    }
}

int main()
{
    cin >> n >> m;
    for (int i = 0; i < n; i++)
    {
        getchar();
        for (int j = 0; j < m; j++)
        {
            maze[i][j] = getchar();
        }
    }
    cin >> s.first >> s.second >> t.first >> t.second;
    dfs(s.first, s.second, 0);
    cout << minsize << endl;
    return 0;
}
```

>对上述代码做几点说明
>
>* 里面用到了`STL`中的`pair`。当想要将两个元素绑在一起作为一个元素时，又不想因此定义一个结构体，那么就可以用`pair`这个小玩意啦~`pair`里面有两个元素，类型是可以自定义的，分别是`first`,`second`。
>  *要注意`minsize`初始化要大一些，这样才能更新最小。
>  *`DFS`求解最短路问题是没有优势的。显而易见，`DFS`会搜索所有的路径，然后求出最小步数，搜索了很多无效状态，效率是非常低的，很容易就`TLE`。

***

### 马的遍历

<img src="https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/a05b6ed800fa4aeaa94f7e05612a2caf.png" alt="在这里插入图片描述" style="zoom:67%;" />

**题目分析**

这道题其实和前面那一道很相似，前面那一道是求从起点到终点的最短步数，而这道是求从起点(马的位置)到各个点(棋盘各个点)的最短步数。
那么该怎么办呢~很简单，从马的位置点`(x, y)`开始`BFS`，用一个`ans`数组来记录起点到达各个点时的层数，也就是最短步数。最后输出ans即可。
题目本身的思路还是很好想滴~还是有些地方需要注意一下。

  1. 棋盘上的一个马的走法是有规则的，俗称”马走日”。

<img src="https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/c2656987a802484e86fbbd4d4692d2f9.png" alt="在这里插入图片描述" style="zoom:67%;" />

一个马可以走的位置已经用圆圈圈住啦~八个偏移量(-2,1),(-2,-1),(-1,2),(-1,-2),(2,1),(2,-1),(1,2),(1,-2)。我们可以用方向数组来表示：

 - `int dx[8]={-2,-2,-1,-1,2,2,1,1};`
 - `int dy[8]={1,-1,2,-2,1,-1,2,-2};`

2. 输出格式要求左对齐，宽5格。

- `printf("%-5d", ans[i][j]);`
- `cout<<left<<setw(5)<<a[i][j]`但要加头文件`#include<iomanip>`

>第二个`cout`的写法大家可以试一试可不可以通过，这是我在洛谷题解里面找到的。还是推荐第一种`printf`的写法。

3. 由于到达不了的地方要输出-1,所以我们要将`ans`数组初始化成-1。技巧`memset(ans, -1,sizeof(ans));`。

>像一些方向数组，初始化数组等一些小技巧小伙伴们要好好整理一下噢，可以提高代码的效率和简洁性。


**AC代码**

```cpp
#include <iostream>
#include <queue>
#include <cstdio>
#include <iomanip>
#include <cstring>
using namespace std;

const int N = 450;
int n, m;
int ans[N][N];//储存到达每点的最少步数
bool inq[N][N];//标记是否已入队
int dx[8]={-2,-2,-1,-1,2,2,1,1};
int dy[8]={1,-1,2,-2,1,-1,2,-2};//坐标偏移量
struct node
{
    int x;
    int y;
}s, Node;

bool check(int x, int y)
{
    if (x <= 0 || x > n || y <= 0 || y > m) return false;//出界
    if (inq[x][y] == true) return false;//已入队
    return true;
}

void bfs()
{
    queue<node> q;
    q.push(s);//起点入队
    inq[s.x][s.y] = true;//标记起点已入队
    while(!q.empty())
    {
        node top = q.front();
        q.pop();
        for (int i = 0; i <= 7; i++)
        {
            int newx = top.x + dx[i];
            int newy = top.y + dy[i];         
            if (check(newx, newy))
            {
                Node.x = newx; Node.y = newy;
                q.push(Node);
                //(Node.x,Node,y)的层数就是队头层数+1，即最短步数。
                ans[Node.x][Node.y] = ans[top.x][top.y] + 1;
                inq[Node.x][Node.y] = true;//标记已入队
            }
        }
    }
}

int main()
{
    memset(ans,-1,sizeof(ans));//将ans数组都设为-1
    cin >> n >> m >> s.x >> s.y;
    ans[s.x][s.y] = 0;
    bfs();
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= m; j++)
        {
          printf("%-5d", ans[i][j]);
        }
        printf("\n");
    }
    return 0;
}
```

<img src="https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/9aa86a15f585415685875f4cd463319f.png" alt="在这里插入图片描述" style="zoom:67%;" />

>当然为了节省空间这道题也可以只开一个`ans`数组。来记录最少步数和是否已入队。
>
>* `ans[x][y] != -1 `说明`(x,y)`点已入队；
>* `ans[x][y] == -1`说明`(x,y)`点未入队。
>  具体代码这里就不再赘述了噢~

***

### 块的个数

<img src="https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/e6222f074a9341f99fc5ab735a2f2658.png" alt="在这里插入图片描述" style="zoom:67%;" />

**题目分析**



>这道题貌似有个名字叫连通块问题。

这道题难了我好长时间，我觉得有些小伙伴肯定也会在这道题上卡一下。主要是题目意思有点难理解。我开始的时候以为块是只十字架结构的1，也就是1的上下左右都是1，然后这些1构成的就是一个块。然后看样例发现块数不太对。样例是4块。

经过长时间的绞尽脑汁的琢磨（原谅我的菜）,我才明白块说的是从某个1开始，看它的上下左右有没有1，有1的话就在看这些1的上下左右有没有1，依次类推，直到找不到1为止。这么说呢可能小伙伴们还是不太懂，那么我们举个例子吧~

<img src="https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/30f34f3483ec4dd78ea62716fda0080c.png" alt="在这里插入图片描述" style="zoom:67%;" />
样例中的四块是用圆圈圈住的这四个。以最上面的那个为例，从最左边的1开始搜索，它的右边有1个1。这个1的右边和下边有1。这些1的上下左右就没有新的1了。因此这些1组成一个块。说到这大家应该明白了许多hh~。

我们还可以发现单独的1也可以是一个块，不然这题答案就是3了。
理解了题意，思路也就很好想了。我们可以`BFS`某个1，找出与它符合块条件的所有1，通过`BFS`将这些1标记下来，进而可以不重复的搜索块的个数。

**AC代码**

```cpp
#include <iostream>
#include <queue>
#include <map>

using namespace std;

const int N = 1000;
typedef pair<int, int> PII;
int m, n;
int maze[N][N];//矩阵
bool inq[N][N];//记录(x,y)是否已入队
int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};
int ans;

bool check(int x, int y)
{
    if (maze[x][y] == 0) return false;//不能访问0
    if (inq[x][y] == true) return false;
    if (x < 1 || x > m || y < 1 || y > n) return false;
    return true;
}

void bfs(int x, int y)//bfs的作用就是将一个块内的所有1标记一下。
{
    PII node;
    node.first = x;
    node.second = y;
    queue<PII> q;
    q.push(node);
    inq[x][y] = true;

    while (!q.empty())
    {
        PII temp = q.front();
        q.pop();
        for (int i = 0; i < 4; i++)
        {
            node.first = temp.first + dx[i];
            node.second = temp.second + dy[i];
            if (check(node.first, node.second))
            {
                q.push(node);
                inq[node.first][node.second] = true;
            }
        }
    }
}

int main()
{
    cin >> m >> n;
    for (int i = 1; i <= m; i++)
    {
        for (int j = 1; j <= n; j++)
        {
            cin >> maze[i][j];
        }
    }
     for (int i = 1; i <= m; i++)
    {
        for (int j = 1; j <= n; j++)
        {	//0肯定不搜索，搜索过的也不搜索
            if (maze[i][j] == 1 && inq[i][j] == false)
            {
                ans++;
                bfs(i, j);
            }
        }
    }
    cout << ans << endl;
}
```

>BFS的做法相对来说还是比较固定的，所以注释有些重复的简单的我就不再写了。
>大家可以尝试用DFS来做一下这道题噢~ 一道题用不同的解法才能看出算法的魅力！
>[DFS解答的代码我放在了这里噢](https://paste.ubuntu.com/p/xXb7Pbqrmb/)~

## 记忆化搜索

>记忆化搜索的目的就是为了解决重复计算。

>通过一个斐波那契数列和小伙伴们来聊一聊什么是记忆化搜索吧~

### 斐波那契数列(三种求解方法)

<img src="https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/c7e404a186454a2d9c02aca46d45a19e.png" alt="在这里插入图片描述" style="zoom:67%;" />
**Answer1. 递归实现**
斐波那契数列的递归实现是比较简单的。就抓住递归的边界和递归式就没问题了。

```cpp
#include <iostream>
using namespace std;

int n;

int fib(int n)
{
    if (n == 1) return 0;
    if (n == 2) return 1;//递归边界
    return fib(n - 1) + fib(n - 2);//递归式
}

int main()
{
    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        cout << fib(i) << ' ';
    }
    cout << endl;
    return 0;
}
```

<img src="https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/27c23b531c4d46ae95b91a8a35d4e5b5.png" alt="在这里插入图片描述" style="zoom:67%;" />
递归的时间复杂度是成指数级增长的，因此如果不进行一些优化的话，求解的`N`很大的话，很容易就`TLE`。我们可以看出fib(3)被重复计算了两次，如果我们将这个数保存起来，再遇到的时候就可以直接使用，大大简化了计算量。这就是记忆化搜索的思想。
**Answer2. 记忆化搜索**

```cpp
#include <iostream>
using namespace std;

const int N = 50;
int n;
int instance[N];//判断某个状态是否算出过解

int dfs_m(int  n)
{
   if (n == 1) return 0;
   if (n == 2) return 1;//递归的边界条件
   if (instance[n]) return instance[n];//某个状态出现过就直接输出该状态的解
   return instance[n] = dfs_m(n - 1) + dfs_m(n - 2);//没算出过解就按递推式正常算
}

int main()
{
   cin >> n;
   for (int i = 1; i <= n; i++)
   {
       cout << dfs_m(i) << ' ';
   }
   cout << endl;
   return 0;
}
```

<img src="https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/d43e3c5a5d944214a73e8d9c724d2f2a.png" alt="在这里插入图片描述" style="zoom:67%;" />
记忆化搜索按照自顶向下的顺序，每求得一个状态时，就把它的解保存下来，以后再次遇到这个状态时就不用重复求解了。

记忆化搜索包含两个要素**记忆化**和**搜索**。

* 沿用搜索的一般模式，本质还是用递归函数实现。
* 在搜索的过程中将已经得到的解保存起来，下次需要时直接用。

记忆化搜索是理解DP思想很重要的基础噢，把记忆化搜索掌握明白，会很容易就入门动态规划噢~

>还有一种递推的方法，时间复杂度只有$O(n)$。递推和递归的区别在于，递推是自底向上，递归是自顶向下。

**Answer3. 递推法**

```cpp
#include <iostream>
using namespace std;

const int N = 50;
int n;
int ans[N];

int main()
{
    cin >> n;
    ans[1] = 0;
    ans[2] = 1;
    for (int i = 3; i <= n; i++)
    {
        ans[i] = ans[i - 1] + ans[i - 2];//递推式
    }
    for (int i =1; i <= n; i++)
    {
        cout << ans[i] << ' ';
    }
    cout << endl;
    return 0;
}
```

## 写在后面

>想到啥就说啥了，给备战蓝桥杯的小伙伴们提些建议。

我不推荐大家去`leetcode`上刷题，因为`leetcode`的形式和蓝桥杯是不太一样的。力扣只需要写函数，输入输出已经内置好了。蓝桥杯的输入输出是需要自己写的。另外呢力扣主要是面向找工作的，题目是面试用的，不是专门打比赛的。因此题目不太适合蓝桥杯竞赛。当然了里面的题目还是很经典的，专门学某一算法是可以刷力扣的。我给大家推荐几个适合的网站还有书籍。

* [ACwing网站](https://www.acwing.com/)
* [洛谷](https://www.luogu.com.cn/)
* [C语言网蓝桥杯题库](https://www.dotcpp.com/oj/train/)
* [蓝桥杯练习系统](http://lx.lanqiao.cn/problemsets.page)
* [蓝桥杯官网题库](https://www.lanqiao.cn/problems/?sort=students_count&category_id=3)
  <img src="https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/da7e876c11b741df8c7d3d5b71ab38f8.png" alt="在这里插入图片描述" style="zoom:67%;" />
  <img src="https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/b811a601fe264db3988a7eca92347a1f.png" alt="在这里插入图片描述" style="zoom:67%;" />
  <img src="https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/df58c6e5577e47d5a9fd70ca73d21e06.png" alt="在这里插入图片描述" style="zoom:67%;" />

<img src="https://cdn.jsdelivr.net/gh/xubenshan/pic-blog@main/img/a799d7696df843f3b8a57695348148cc.png" alt="在这里插入图片描述" style="zoom: 67%;" />

> 这几个网站和书是我一直在用的，真心推荐给大家~