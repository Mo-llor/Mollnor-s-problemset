[[JOISC 2013]_礼物_Presents](https://www.luogu.com.cn/problem/P14442) 

基环树，你知道的，这是我做的第一道基环树题喵

# 基环树的概念

基环树，是一个n条边，n个点的图喵，但是只有这一个条件的话，不一定只有一个基环树喵。正如这道题，基环树森林，还是难搞喵

# 为什么是基环树喵？

因为对于每个点来说，它们只具有唯一的一条出边喵，有 n 个点，n个边，所以这是个基环树森林喵

# 要怎么写喵？

对于一般基环树来说，我们一般都要先拓扑排序找到那个环再处理剩下的事情喵。至于剩下的事情，我们当然可以去做基环树上dp。但是对于这道题来说，如果用基环树上dp的话未免有些小题大作了喵，我们明明只要指定每条边究竟是相同还是不同就好了喵。不需要[0][0],[0][1],[1][0],[1][1]这样的状态转移喵。对于不在环上的树枝，我们只要贪心地取最大就好了喵，对于在环上的点，我们只要保证“不同”的个数是偶数就好了喵。

如果是奇数个"不同"的话，就会出现起点自相矛盾的现象喵。

如果是奇数的话，如果是奇数的话，我们自然是要把一条边反转一下喵，那我们就要贪心地去反转差值最小的那条边喵，这样我们选出来的结果就一定是最大的喵

```cpp
#define int ll
void solve()
{	
    int n;
    cin>>n;
    vector<int> a(n + 1),b(n + 1),c(n + 1),d(n + 1);
    for (int i = 1; i < n + 1; i++)
    {
        cin>>a[i]>>b[i]>>c[i]>>d[i];
    }
    vector<vector<int>> g(n + 1);
    vector<int> deg(n + 1);
    for (int i = 1 ; i <= n ; i++) 
    {
        deg[a[i]]++;
    }
    int ans = 0;
    queue<int> q;
    for (int i = 1 ; i <= n ; i++)
    {
        if (deg[i] == 0)
        {
            q.emplace(i);
        }
    }
    while(!q.empty())
    {
        int x = q.front();
        q.pop();
        int i = a[x];
        ans += max(c[i] * b[x],d[i] * b[x]);
        deg[i]--;
        if (deg[i] == 0)
        {
            q.emplace(i);
        }
    }
    bitset<100009> vis;
    for (int i = 1 ; i <= n ; i++)
    {
        if (deg[i] != 0 && !vis[i])
        {
            int cur = i;
            int diff = 0;
            int minn = INF;
            while (!vis[cur])
            {
                int nxt = a[cur];
                vis[cur] = 1;
                int num_diff =  d[nxt] * b[cur];
                int num_yy = c[nxt] * b[cur];
                minn = min(minn,abs(num_diff - num_yy));
                if (num_diff > num_yy)
                {
                    diff++;
                    ans += num_diff;
                }
                else
                {
                    ans += num_yy;
                }
                cur = nxt;
            }
            if (diff & 1)
            {
                ans -= minn;
            }
        }
    }
    cout<<ans<<'\n';
    return;
}
```
