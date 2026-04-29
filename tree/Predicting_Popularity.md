[Predicting Popularity](https://codeforces.com/contest/2145/problem/E)

在这里我们设

$$
x_i = max(a_i−ac,0)+max(d_i−dr,0)
$$

当且仅当 $S(x) - x_i > 0$ 的时候，人家才会去看这个电影喵，(s(x)是前面所有看过的人的个数喵) 而只要前面有一个人不满足这个条件，那后面的人肯定都看不了了

所以我们的目标是找到第一个 $S(x) - x_i > 0$ 的位置喵，这里就是我们要求的p喵，对于这个信息，我们可以考虑用线段树维护喵，对于 $x_i$ 如果他要是看的话，显然他对于后面的所有的可能的值要 +1喵

在这个线段树里面，我们要维护一个树上二分的东西喵

```cpp
    int binarysearch(int i,int l,int r)
    {
        if (l == r) return l;
        push(i,l,r);
        int mid = (l + r) / 2;
        if (info[2 * i].val <= 0) return binarysearch(2 * i,l,mid);
        else return binarysearch(2 * i + 1,mid + 1,r);
    }
```


```cpp
void solve()
{
    int ac = 0,dr = 0;
    int n;
    cin>>ac>>dr;
    cin>>n;
    LazySegTree sgt(0,2000009);
    vector<int> a(n + 1),d(n + 1);
    for (int i = 1 ; i <= n ; i++) cin>>a[i];
    for (int i = 1 ; i <= n ; i++) cin>>d[i];
	for (int i = 1 ; i <= 2000000 ; i++)
	{
		sgt.set(i,{-i});
	}
	for (int i = 1 ; i <= n ; i++)
	{
		sgt.rangeOperation(max(a[i] - ac,0LL) + max(d[i] - dr,0LL),sgt.max_,{1});
	}
    int q;
    cin>>q;
    while(q--)
    {
        int x,ai,di;
        cin>>x>>ai>>di;
        sgt.rangeOperation(max(a[x] - ac,0ll) + max(d[x] - dr,0ll),sgt.max_,{-1});
        sgt.rangeOperation(max(ai - ac,0ll) + max(di - dr,0ll),sgt.max_,{1});
        a[x] = ai,d[x] = di;
		cout<<sgt.binarysearch(1,sgt.min_,sgt.max_)<<'\n';
    }
    return;
}
```
