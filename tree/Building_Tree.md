这是一个跑得很慢很慢的代码喵

本质是 $m * q$ 的暴力喵，但是给我减减枝就过去了喵

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef long double ld;
#define int ll
typedef unsigned long long ull;
typedef __int128 i128;
typedef pair<int,int> pii;
typedef pair<ll,ll> pll;
const int MOD = 998244353;
const ll INF = 0x3f3f3f3f3f3f3f3f;
const int N = 1e6 + 10;

mt19937_64 rng(chrono::steady_clock::now().time_since_epoch().count());
ll rand(ll l, ll r) {return uniform_int_distribution<ll>(l, r)(rng);}

struct DSU {
    std::vector<int> siz;
    std::vector<int> f;
    std::vector<std::array<int, 2>> his;
    
    DSU(int n) : siz(n + 1, 1), f(n + 1) {
        std::iota(f.begin(), f.end(), 0);
    }
    
    int find(int x) {
        while (f[x] != x) {
            x = f[x];
        }
        return x;
    }
    
    bool merge(int x, int y) {
        x = find(x);
        y = find(y);
        if (x == y) {
            return false;
        }
        if (siz[x] < siz[y]) {
            std::swap(x, y);
        }
        his.push_back({x, y});
        siz[x] += siz[y];
        f[y] = x;
        return true;
    }
    
    int time() {
        return his.size();
    }
    
    void revert(int tm) {
        while (his.size() > tm) {
            auto [x, y] = his.back();
            his.pop_back();
            f[y] = y;
            siz[x] -= siz[y];
        }
    }
};

struct Info
{
	int u = 0 ,v = 0;
};


void solve()
{
	int n,m,q;
	cin>>n>>m>>q;
	DSU ds(n + 1);
	vector<vector<Info>> info(4 * m + 1);
	int min_ = 0,max_ = m;
	auto add = [&](auto &&self,int i,int u,int v,int x,int y,int l,int r) -> void
	{
		if (x > y) return;
		if (x <= l && r <= y)
		{
			info[i].emplace_back(u,v);
			return;
		}
		int mid = l + (r - l) / 2;
		if (y <= mid) return self(self,2 * i,u,v,x,y,l,mid);
		if (x > mid) return self(self,2 * i + 1,u,v,x,y,mid + 1,r);
		self(self,2 * i,u,v,x,y,l,mid);	
		self(self,2 * i + 1,u,v,x,y,mid + 1,r);
	};
	vector<int> s(q + 1);
	DSU full_dsu(n + 1);
	for (int i = 1 ; i <= m ; i++)
	{
		int u,v,w;
		cin>>u>>v>>w;
		full_dsu.merge(u,v);
		add(add,1,u,v,0,w - 1,min_,max_);
		add(add,1,u,v,w + 1,m,min_,max_);
	}
	for (int i = 1 ; i <= q ; i++) cin>>s[i];
	int ans = 0;
	int tt = full_dsu.find(s[1]);
	for (int i = 1 ; i <= q ; i++)
	{
		if (full_dsu.find(s[i]) != tt)
		{
			cout<<-1<<'\n';
			return;
		}
	}
	DSU q_dsu(q + 1); // 维护 q 个点的新并查集喵
    vector<int> vis(n + 1, -1);
    vector<int> head(n + 1, 0);
	auto dfs = [&](auto &&self,int i,int l,int r) -> void //枚举mex
	{
		int tm = ds.time();
		for (const auto& [u,v] : info[i])
		{
			ds.merge(u,v);
		}
		if (q_dsu.siz[q_dsu.find(1)] == q)
		{
			return;
		}
		if (l == r)
		{
			for (int j = 1; j <= q; j++) {
                int comp = ds.find(s[j]);
                if (vis[comp] != l) {
                    vis[comp] = l; // 记录这个连通块在时间 l 第一次遇到的 q 节点喵
                    head[comp] = j;
                } else {
                    if (q_dsu.merge(head[comp], j)) { // 合并新图中的点喵
                        ans += l; // 代价就是当前的 mex，也就是 l 喵
                    }
                }
            }
		}
		else
		{
			int mid = l + (r - l) / 2;
			self(self,2 * i,l,mid);
			self(self,2 * i + 1,mid + 1,r);
		}
		ds.revert(tm);
		return;
	};
	dfs(dfs,1,min_,max_);
	int q_comps = 0;
    for (int j = 1; j <= q; j++) {
        if (q_dsu.find(j) == j) q_comps++;
    }
    if (q_comps > 1) {
        cout << -1 << "\n";
    } else {
        cout << ans << "\n";
    }
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
