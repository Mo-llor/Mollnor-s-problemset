[Dinner_Invitation](https://qoj.ac/contest/3387/problem/15423) 

因为是第一次见到这样的写法，所以还是很有纪念意义的喵

```cpp
void solve()
{
    int n;
    cin>>n;
    struct data
    {
        int w = 0,r = 0;
    };
    
    vector<data> a(n);
    for (int i = 0 ; i < n ; i++) cin>>a[i].w;
    for (int i = 0 ; i < n ; i++) cin>>a[i].r;

    ranges::sort(a,greater{},&data::r);
    priority_queue<int,vector<int>,greater<int>> pq1;
    priority_queue<int> pq2;
    int ans = 0,s = 0;
    for (const auto& [w,r] : a)
    {
        pq1.emplace(w);
        while(!pq2.empty() && pq2.size() * r < s)
        {
            pq1.emplace(pq2.top());
            s -= pq2.top();
            pq2.pop();
        }
        while(!pq1.empty() && !pq2.empty() && pq1.top() < pq2.top())
        {
            int x1 = pq1.top();
            pq1.pop();
            int x2 = pq2.top();
            pq2.pop();
            pq1.emplace(x2);
            pq2.emplace(x1);
            s = s - x2 + x1;
        }
        while(!pq1.empty() && (pq2.size() + 1) * r >= s + pq1.top())
        {
            pq2.emplace(pq1.top());
            s += pq1.top();
            pq1.pop();
        }
        ans = max(ans,(int)pq2.size());
    }
    cout<<ans<<'\n';
    return;
}
```
