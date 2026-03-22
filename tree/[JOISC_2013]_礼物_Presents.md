基环树，你知道的，这是我做的第一道题喵

# 基环树的概念

基环树，是一个n条边，n个点的图喵，但是只有这一个条件的话，不一定只有一个基环树喵。正如这道题，基环树森林，还是难搞喵

# 为什么是基环树喵？

因为对于每个点来说，它们只具有唯一的一条出边喵，有 n 个点，n个边，所以这是个基环树森林喵

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
