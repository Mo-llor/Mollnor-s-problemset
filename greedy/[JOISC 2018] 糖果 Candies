我原本以为反悔贪心就只是简单的吧之前贪过的数据放进一个优先队列里面，如果后面找到更优的就会把前面最劣的答案丢掉，但是很显然，这道题不仅仅是这样子的喵

反悔贪心的优先队列是要有的喵，这题也不例外，但是这道题里面存的是什么喵？直接存之前贪过的对应值吗？这显然不对。因为题目中有说，我们不能拿相邻的糖果，
如果我们之后反悔的话，还要考虑怎么把它左右的糖果拿上，这样的实现写起来会很有难度的喵

所以，我们就要另辟蹊径喵

我们要找到一种构造方法，让我们只要反悔贪心就好了

```cpp
void solve()
{
    int n;
    cin>>n;
    struct data
    {
        int l = 0,r = 0,val = 0;
    };
    vector<data> a(n + 2);
    for (int i = 1 ; i <= n ; i++)
    {
        cin>>a[i].val;
        a[i].l = i - 1;
        a[i].r = i + 1;
    }
    a[0].l = 0;
    a[0].val = -INF;
    a[0].r = 1;
    a[n + 1].l = n;
    a[n + 1].val = -INF;
    a[n + 1].r = n + 1;
    priority_queue<pll> prpr;
    for (int i = 1 ; i <= n ; i++)
    {
        prpr.emplace(a[i].val,i);
    }
    bitset<2000000> del;
    int sumn = 0;
    for (int i = 1 ; i <= (n + 1) / 2 ; i++)
    {
        while(!prpr.empty() && del[prpr.top().second])prpr.pop();
        auto [val,idx] = prpr.top();
        prpr.pop();
        sumn += val;
        auto [le,re,v] = a[idx];
        del[le] = 1;
        del[re] = 1;
        v = a[re].val + a[le].val - v;
        a[a[le].l].r = a[le].r;
        a[a[re].r].l = a[re].l;
        a[idx] = {a[le].l,a[re].r,v};
        prpr.emplace(v,idx);
        cout<<sumn<<'\n';
    }
    return;
}
```
