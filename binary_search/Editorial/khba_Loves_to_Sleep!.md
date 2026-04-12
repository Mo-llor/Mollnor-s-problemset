这个二分是真神秘啊[khba_Loves_to_Sleep!](https://codeforces.com/contest/2167/problem/E)

```cpp
void solve()
{	
	int n,k,x;
	cin>>n>>k>>x;
	vector<int> a(n);
	for (int i = 0 ; i < n ; i++)
	{
		cin>>a[i];
	}
	ranges::sort(a);
	struct qj
	{
		int ls = 0,rs = 0;
	};
	
	auto check = [&](int mid)
	{
		vector<qj> xx;
		for (const auto& i : a)
		{
			int l = max(0ll,i - mid + 1);
			int r = min(x,i + mid - 1);
			if (l <= r) xx.emplace_back(l,r);
		}
		ranges::sort(xx,{},&qj::ls);
		int pn = 0;
		int prel = 0,prer = -1;
		for (const auto& [ls,rs] : xx)
		{
			if (ls > prer)
			{
				pn += prer - prel + 1;
				prel = ls;
				prer = rs;
			}
			else
			{
				prer = max(rs,prer);
			}
		}
		pn += prer - prel + 1;
		if (x + 1 - pn >= k) return true;
		else return false;
	};
	int l = 0,r = x,ans = 0;
	while(l <= r)
	{
		int mid = (l + r) / 2;
		if (check(mid))
		{
			ans = mid;
			l = mid + 1;
		}
		else r = mid - 1;
	}
	vector<qj> xx;
		for (const auto& i : a)
		{
			int l = max(0ll,i - ans + 1);
			int r = min(x,i + ans - 1);
			if (l <= r) xx.emplace_back(l,r);
		}
	vector<qj> xq;
	for (const auto& [ls,rs] : xx)
	{
		if (xq.empty() || xq.back().rs < ls)
		{
			xq.emplace_back(ls,rs);
		}
		else
		{
			xq.back().rs = max(xq.back().rs,rs);
		}
	}
	int cnm = 0,cx = 0,cur = 0;
	while(cx < k && cnm <= x)
	{
		if (cur < xq.size() && cnm >= xq[cur].ls)
		{
			cnm = max(xq[cur].rs + 1,cnm);
			cur++;
		}
		else
		{
			cout<<cnm<<' ';
			cnm++;
			cx++;
		}
	}
	cout<<'\n';
	return;
}
```
