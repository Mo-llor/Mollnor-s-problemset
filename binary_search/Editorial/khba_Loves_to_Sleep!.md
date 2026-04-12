这个二分是真神秘啊[khba_Loves_to_Sleep!](https://codeforces.com/contest/2167/problem/E)

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef unsigned long long ull;
typedef __int128 i128;
typedef pair<int,int> pii;
typedef pair<ll,ll> pll;
const int MOD = 998244353;
const ll INF = 0x3f3f3f3f3f3f3f3f;
const int N = 1e6 + 10;
#define int ll

mt19937_64 rng(chrono::steady_clock::now().time_since_epoch().count());
ll rand(ll l, ll r) {return uniform_int_distribution<ll>(l, r)(rng);}

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
signed main()
{
	std::ios::sync_with_stdio(false);
	std::cin.tie(0);
	ll _ = 1;
	std::cin>>_;
	while (_--)
	{
		solve();
	}
	return 0;
}
```
