对于这个线段树，按道理来讲，把两个函数拿出来然后在solve里面写lambda是一点影响没有的，但是，毕竟调用的时候要传l和r喵，但是线段树毕竟封装好的写多了喵，这样写着舒服一点喵。

# 解法喵

首先，你把这道题转化成图论模型想想喵。在国家 $i$ 拿到护照，就可以去 $[L_i, R_i]$ 范围内的任何国家，对吧喵？

这就相当于在图论里，从节点 $i$ 向区间 $[L_i, R_i]$ 里的每一个节点都连一条权值为 1 的有向边喵！

但是！如果你用最蠢的暴力方法，直接一个个去建边，仔细看看数据范围，题目里的 $N$ 最大可是 $2 \times 10^5$ 喵！如果按你的笨办法，边的数量最多会达到 $O(N^2)$ 级别。

你那破代码交上去肯定会直接 TLE（超时）爆掉，甚至 MLE（内存超限）的，笨蛋喵！这时候该谁登场了？当然是线段树优化建图啦喵！你想想，线段树最擅长处理什么？不就是区间操作吗喵！

这题和线段树的关系，就在于如何优雅地把点和区间连起来喵：

- 区间拆分： 我们可以建一棵线段树，树上的叶子节点代表每一个国家。当你需要从 $i$ 向区间 $[L_i, R_i]$ 连边的时候，不需要连几万条边，只需要在线段树上找到覆盖这个区间的 $O(\log N)$ 个节点，把 $i$ 连向它们就好了喵！

- 树内连边： 线段树内部，父节点再向子节点连权值为 0 的有向边喵。

这样，只要你能走到线段树上的某个大区间，就等价于你能零成本走到它包含的所有小区间和叶子节点（具体的国家）喵！

通过这种方式，点向区间连边的时间和空间复杂度就从 $O(N)$ 华丽丽地降到了 $O(\log N)$，总边数也变成了 $O(N \log N)$ 级别。

在这个优化好的图上，你再去跑最短路算法（比如 Dijkstra），就不会卡死了，是不是很巧妙喵？

当然啦，这题的完整解法还需要你再动点脑子喵。你需要分别求出所有点向左走到国家 1 的最短路，以及向右走到国家 $N$ 的最短路，然后再把这两个代价合并起来作为初始状态，在你的线段树建图上再跑一次最短路求出最终答案喵。

# 建图喵

为了搞定这道题，我们实际上需要建一个 **反图(Reversed Graph)** 喵。

因为你想想，我们要从任意一个未知的国家出发去国家 $1$ 和国家 $N$，这等价于什么？

等价于我们从国家 $1$ 和国家 $N$ 出发，反向探索能到达哪些国家喵！在原图里，拥有国家 $i$ 的护照，是从点 $i$ 走向区间 $[L_i, R_i]$。那么在反图里，就是从区间 $[L_i, R_i]$ 走向点 $i$ 喵。

所以，我们需要用线段树来优化这种**“区间向单点连边”**的操作，具体步骤如下喵：

第一步：分配节点编号，把树建起来喵

你需要两类节点：

- 真实国家节点： 就是 $1$ 到 $N$ 这 $N$ 个叶子节点喵。
- 线段树上的虚拟区间节点： 为了包含这些叶子节点，线段树会额外产生差不多 $4N$ 个节点喵。

所以你要准备一个能容纳 $5N$ 个节点的邻接表喵。

- 真实国家节点： 直接用 $1$ 到 $N$ 喵。
- 线段树上的虚拟区间节点： 为了不和真实国家冲突，如果线段树节点的原始编号是 $p$ （通常从 $1$ 开始），那我们在图里的统一编号就设为 $p + N$ 喵！

第二步：线段树内部连边（自底向上喵！）

既然是反图，也就是区间向点连边，我们的线段树必须要从子节点向父节点连边，并且边权全都是 0 喵！

- 为什么是 0？ 因为如果你在反图中能到达某个小区间 (比如只包含国家 $2$ 的叶子节点)，那你当然也就理所应当能到达包含它的更大的区间(比如区间 $[1, 3]$ )，这不需要消耗额外的护照，懂了吗笨蛋喵？

- 做法： 递归建树的时候，让左儿子和右儿子都向它们的父亲节点连一条有向边，权重设为 0 喵。

第三步：把护照的限制加进去（区间向点连边喵！）

这是最关键的一步喵！对于每一个国家 $i \ (1 \le i \le N)$，它签发的护照可以去 $[L_i, R_i]$。

- 在线段树上，你写一个区间查询的函数，找到完全覆盖 $[L_i, R_i]$ 的那 $O(\log N)$ 个线段树节点喵。
- 然后，从这 $O(\log N)$ 个线段树节点，分别向真实的国家节点 $i$ （也就是那个代表国家 $i$ 的叶子节点）连一条有向边，边权设为 1 喵！因为在反图里，走到这代表你“消耗了 1 本护照，从这些地方退回了国家 $i$”喵。

通过这三步，原本 $O(N^2)$ 的边数就被用魔法压缩到了 $O(N \log N)$ 的级别，空间和时间都不会爆炸了喵！现在的图里有 $O(N)$ 个点和 $O(N \log N)$ 条边，你只需要在这个图上跑最短路算法就可以了喵。

另外，我们要把实际的n个节点连到线段树上那虚拟的n个节点喵，这样才算是整图连在一起了喵

# 跑最短路

那问题来了喵，不会跑最短路怎么办喵？

$dikstra$ 会跑吧我们只要重复三次 $dijkstra$ 就好了喵

- 第一遍：以国家 $1$ 对应的叶子节点为起点，在反图上跑最短路（Dijkstra 或 0-1 BFS），得到距离数组 $D_1$ 喵。$D_1[i]$ 就代表从国家 $i$ 走到国家 $1$ 最少需要消耗多少护照喵。
- 第二遍：以国家 $N$ 对应的叶子节点为起点，在反图上再跑一次最短路，得到距离数组 $D_N$ 喵。$D_N[i]$ 代表从国家 $i$ 走到国家 $N$ 最少需要多少护照喵。

  听到这里，你那单细胞的脑子是不是想说：“啊！那答案直接就是 $D_1[X_j] + D_N[X_j]$ 啦！”？错啦大笨蛋喵！如果你直接加起来，两条路径在开头重合的部分，就被你把护照多算了一遍喵！

  假设我们在国家 $k$ 拿了护照后分头行动，一条路去 $1$，一条路去 $N$ 喵。那么从国家 $k$ 出发，分别满足两个目标的代价和是 $D_1[k] + D_N[k]$ 喵。

  但是！在我们的反图里，进入国家 $k$ 的那条边（也就是意味着使用了国家 $k$ 的护照）的权值是 $1$，它在 $D_1[k]$ 和 $D_N[k]$ 里都被算了一次，所以我们必须减去这个重复计算的 $1$ 喵！

  真正的合并代价应该是 $D_1[k] + D_N[k] - 1$ 喵！（当然，如果 $D_1[k]$ 或 $D_N[k]$ 是无穷大，就说明走不到，不用管它喵。）所以，这就是第三遍最短路出场的时候了喵！
  
- 第三遍：我们将所有的真实国家节点 $k \ (1 \le k \le N)$ 作为多源最短路的起点，开一个全新的距离数组 $D_{all}$，把它们的初始距离设为 $D_{all}[k] = D_1[k] + D_N[k] - 1$ 喵。然后把这些节点和对应的初始距离统统丢进优先队列里，在反图上跑最后一次 Dijkstra 喵！

为什么要再跑一次？因为某个国家 $x$ 可能自己不作为分界点，而是先花几本护照走到国家 $k$，然后再从 $k$ 分头去 $1$ 和 $N$ 喵！这第三次最短路，就是把这种“在 $k$ 分头行动的最优解”通过反图传播给所有能到达 $k$ 的其他国家喵！

# 一些小问题喵

为什么终点是1,n喵？

那是因为我们从一个点出发的时候，对方能去的区间一定是连续的喵，题目里面有这句话喵

- 这里保证旅行家能够进入其签证的签发国。形式上地说, $L_i≤i≤R_i$ 必然成立。

所以说喵，能去的区间一定是连续的，如果要遍历所有区间的话，我们一定能到1和n两个点喵。所以把1和n两个点当作是终点是对的喵

另外，我们一定要从叶子节点开始跑 $dijkstra$ 而不是从实际的节点跑喵，

$d1[i]$ 的严格定义是：从国家 $i$ 出发到达国家 $1$，且必须包含“捡起国家 $i$ 护照”这一个动作的总花费喵！

所以合并的时候，国家 $i$ 的护照被算了两次，我们才理直气壮地用 $dx[i] = d1[i] + dn[i] - 1$ 给减掉一次喵。

在叶子节点上面跑的原因就是要把每个点都计算两次喵。

1. 起点在叶子 $1$，距离为 $0$。真实国家 $1$ 现在的距离是无穷大 INF 喵！
2. 波纹从叶子 $1$ 顺着树内部的 $0$ 权边往上爬。题目保证了护照 $1$ 必然包含国家 $1$ ($L_1 \le 1 \le R_1$)。
3. 所以，波纹一定会顺着国家 $1$ 的护照边（权值为 $1$），狠狠地砸在“真实国家 1”的脑袋上喵！此时， $d1[1]$ 被更新为了 $1$ 喵！

从1开始跑最短路的时候被算了一次，从n开始跑最短路的时候又被算了一次，这样1这个节点就被算了整整两次喵。这样子的话，在最后算 $d_{all}$ 的时候就不用特判了

不然的话，我们当然可以先特判1和n的喵，之后再用常规的公式去算其他节点的值喵

```cpp
struct SegTree {
    int min_, max_,n_;
    SegTree(int n): min_(1), max_(n) , n_(n){}
private:
    void build(int i,int l,int r,vector<vector<pii>>& vec)
    {
        if (l == r)
        {
            vec[l].emplace_back(i + n_,0);
            return;
        }
        int mid = (l + r) / 2;
        vec[2 * i + n_].emplace_back(i + n_,0);
        vec[2 * i + 1 + n_].emplace_back(i + n_,0);
        build(2 * i,l,mid,vec);
        build(2 * i + 1,mid + 1,r,vec);
    }
    void query(int x, int y, int i, int l, int r,vector<vector<pii>>& vec,int cur) {
        if (l > y) return;
        if (l >= x && r <= y) {vec[i + n_].emplace_back(cur,1);return;}
        if (y <= l + (r - l) / 2) query(x, y, 2 * i, l, l + (r - l) / 2,vec,cur);
        else if (x > l + (r - l) / 2) query(x, y, 2 * i + 1, l + (r - l) / 2 + 1, r,vec,cur);
        else
        {
            query(x, y, 2 * i, l, l + (r - l) / 2,vec,cur);
            query(x, y, 2 * i + 1, l + (r - l) / 2 + 1, r,vec,cur);
        }
    }
public:
    void query(int l, int r,vector<vector<pii>> &vec,int cur) { return query(l, r, 1, min_, max_,vec,cur); }
    void build(vector<vector<pii>>& vec){build(1,min_,max_,vec);}
};

void solve()
{
    int n;
    cin>>n;
    SegTree sgt(n);
    vector<vector<pii>> g(5 * n + 2);
    sgt.build(g);
    for (int i = 1 ; i <= n ; i++)
    {
        int l,r;
        cin>>l>>r;
        sgt.query(l,r,g,i);
    }
    vector<int> d1(5 * n + 1,INF),dn(5 * n + 1,INF);
    priority_queue<pii,vector<pii>,greater<pii>> prpr;
    for (const auto &[nxt,len] : g[1])
    {
        prpr.emplace(0,nxt);
        d1[nxt] = 0;
    }
    while(!prpr.empty())
    {
        auto [dis,cur] = prpr.top();
        prpr.pop();
        if (dis > d1[cur]) continue;
        for (const auto& [num,len] : g[cur])
        {
            if (dis + len < d1[num])
            {
                d1[num] = dis + len;
                prpr.emplace(d1[num],num);
            }
        }
    }
    for (const auto &[nxt,len] : g[n])
    {
        prpr.emplace(0,nxt);
        d1[nxt] = 0;
    }
    while(!prpr.empty())
    {
        auto [dis,cur] = prpr.top();
        prpr.pop();
        if (dis > dn[cur]) continue;
        for (const auto& [num,len] : g[cur])
        {
            if (dis + len < dn[num])
            {
                dn[num] = dis + len;
                prpr.emplace(dn[num],num);
            }
        }
    }
    vector<int> dx(5 * n + 1,INF);
    for (int i = 1; i <= n; i++)
    {
        if (d1[i] != INF && dn[i] != INF)
        {
            dx[i] = d1[i] + dn[i] - 1;
            prpr.emplace(dx[i],i);
        }
    }
    while(!prpr.empty())
    {
        auto [dis,cur] = prpr.top();
        prpr.pop();
        if (dis > dx[cur]) continue;
        for (const auto& [num,len] : g[cur])
        {
            if (dis + len < dx[num])
            {
                dx[num] = dis + len;
                prpr.emplace(dx[num],num);
            }
        }
    }
    int q;
    cin>>q;
    while(q--)
    {
        int x;
        cin>>x;
        if (dx[x] == INF)
        {
            cout<<-1<<'\n';
            continue;
        }
        cout<<dx[x]<<'\n';
    }
    return;
}
```

从1和n开始跑最短路的代码喵

```cpp
d1[1] = 0;
prpr.emplace(0,1);
while(!prpr.empty())
{
    auto [dis,cur] = prpr.top();
    prpr.pop();
    if (dis > d1[cur]) continue;
    for (const auto& [num,len] : g[cur])
    {
        if (dis + len < d1[num])
        {
            d1[num] = dis + len;
            prpr.emplace(d1[num],num);
        }
    }
}
dn[n] = 0;
prpr.emplace(0,n);
while(!prpr.empty())
{
    auto [dis,cur] = prpr.top();
    prpr.pop();
    if (dis > dn[cur]) continue;
    for (const auto& [num,len] : g[cur])
    {
        if (dis + len < dn[num])
        {
            dn[num] = dis + len;
            prpr.emplace(dn[num],num);
        }
    }
}
vector<int> dx(5 * n + 1,INF);
dx[1] = d1[1] + dn[1];
dx[n] = d1[n] + dn[n];
prpr.emplace(dx[1],1);
prpr.emplace(dx[n],n);
for (int i = 2; i < n; i++)
{
    if (d1[i] != INF && dn[i] != INF)
    {
        dx[i] = d1[i] + dn[i] - 1;
        prpr.emplace(dx[i],i);
    }
}
```
