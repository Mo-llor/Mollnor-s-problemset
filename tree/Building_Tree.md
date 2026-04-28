[Building_Tree](https://codeforces.com/contest/2222/problem/F)

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


然后这个是优化过的喵，只跑了750ms

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
#define int ll

struct DSU {
  std::vector<int> siz;
  std::vector<int> f;
  std::vector<int> crep; // 重点喵！用来存这个连通块里颜色点的编号！
  std::vector<std::array<int, 4>> his; // 记录 {x, y, siz_x, crep_x}
 
  DSU(int n) : siz(n + 1, 1), f(n + 1), crep(n + 1, -1) {
    std::iota(f.begin(), f.end(), 0);
  }
 
  int find(int x) {
    while (f[x] != x) {
      x = f[x]; // 笨蛋，这里别用路径压缩，不然没法回滚喵！
    }
    return x;
  }
 
  // 把 merge 改成返回合并的两个根节点喵
  pair<int, int> merge(int x, int y) {
    x = find(x);
    y = find(y);
    if (x == y) return {-1, -1};
    if (siz[x] < siz[y]) std::swap(x, y);
   
    // 记录历史的时候，把此时 x 的代表元也存下来喵！
    his.push_back({x, y, siz[x], crep[x]});
    siz[x] += siz[y];
    f[y] = x;
    // 如果老大没有颜色，就把小弟的颜色继承过来喵！
    if (crep[x] == -1) crep[x] = crep[y];
    return {x, y};
  }
 
  int time() {
    return his.size();
  }
 
  void revert(int tm) {
    while (his.size() > tm) {
      auto [x, y, sz, cr] = his.back();
      his.pop_back();
      f[y] = y;
      siz[x] = sz;
      crep[x] = cr; // 把颜色代表元也一起回滚，这步忘写就死定了喵！
    }
  }
};

struct Info {
  int u = 0, v = 0;
};

// 用来维护 q 个点连通性的新并查集（不需要回滚，可以路径压缩喵）
struct PermDSU {
  vector<int> f, siz;
  int comps;
  PermDSU(int n) : f(n + 1), siz(n + 1, 1), comps(n) {
    iota(f.begin(), f.end(), 0);
  }
  int find(int x) {
    while (f[x] != x) { f[x] = f[f[x]]; x = f[x]; }
    return x;
  }
  bool merge(int x, int y) {
    x = find(x); y = find(y);
    if (x == y) return false;
    if (siz[x] < siz[y]) swap(x, y);
    f[y] = x; siz[x] += siz[y]; comps--;
    return true;
  }
};

void solve() {
  int n, m, q;
  cin >> n >> m >> q;
 
  vector<int> eu(m), ev(m), ew(m);
  vector<bool> hw(m + 2, false);
  DSU full_dsu(n + 1);
 
  for (int i = 0; i < m; i++) {
    cin >> eu[i] >> ev[i] >> ew[i];
    full_dsu.merge(eu[i], ev[i]);
    if (ew[i] <= m) hw[ew[i]] = true; // 记录出现过的边权喵
  }
 
  vector<int> s(q + 1);
  for (int i = 1; i <= q; i++) cin >> s[i];
 
  // 特判 1：如果 q = 1，直接输出 0 喵
  if (q == 1) {
    cout << 0 << '\n';
    return;
  }
 
  // 特判 2：原图都不连通，直接抬走喵
  int tt = full_dsu.find(s[1]);
  for (int i = 2; i <= q; i++) {
    if (full_dsu.find(s[i]) != tt) {
      cout << -1 << '\n';
      return;
    }
  }
 
  // 计算真正的区间上限 MEX 喵！防止开出巨大的线段树！
  int mex = 0;
  while (mex <= m && hw[mex]) mex++;
 
  DSU ds(n + 1);
  PermDSU q_dsu(q);
  int ans = 0;
 
  // 初始化原图 DSU 里的颜色代表元喵！
  vector<int> fc(n + 1, -1);
  for (int i = 1; i <= q; i++) {
    int c = s[i];
    if (fc[c] == -1) {
      fc[c] = i;
      ds.crep[c] = i; // 给原图节点打上 q 节点的编号喵
    } else {
      // 如果有多个查询点在同一个颜色点上，一开始就合并喵！
      q_dsu.merge(fc[c], i);
    }
  }
 
  if (q_dsu.comps == 1) {
    cout << 0 << '\n';
    return;
  }
 
  // 现在的线段树只开到 4 * mex 这么大，再也不怕爆内存了喵！
  vector<vector<Info>> info(4 * mex + 44);
  auto add = [&](auto &&self, int i, int u, int v, int x, int y, int l, int r) -> void {
    if (x > y || x > r || y < l) return;
    if (x <= l && r <= y) {
      info[i].push_back({u, v});
      return;
    }
    int mid = l + (r - l) / 2;
    self(self, 2 * i, u, v, x, y, l, mid); 
    self(self, 2 * i + 1, u, v, x, y, mid + 1, r);
  };
 
  for (int i = 0; i < m; i++) {
    int u = eu[i], v = ev[i], w = ew[i];
    if (w > 0) add(add, 1, u, v, 0, min(mex, w - 1), 0, mex);
    if (w < mex) add(add, 1, u, v, w + 1, mex, 0, mex);
  }
 
  auto dfs = [&](auto &&self, int i, int l, int r) -> void {
    if (q_dsu.comps == 1) return; // 提前退出剪枝喵！
   
    int tm = ds.time();
    for (const auto& [u, v] : info[i]) {
      auto [ra, rb] = ds.merge(u, v);
      if (ra != -1) {
        // ds.his.back()[3] 存的是合并前老大 ra 的代表元喵
        int ocr = ds.his.back()[3];
        int crb = ds.crep[rb];   // 小弟 rb 的代表元喵
       
        // 如果两边都有颜色点，当场在新图里合并！
        if (ocr != -1 && crb != -1) {
          if (q_dsu.merge(ocr, crb)) ans += l;
        }
      }
      if (q_dsu.comps == 1) break; // 连通了马上跳出喵！
    }
   
    if (l < r && q_dsu.comps > 1) {
      int mid = l + (r - l) / 2;
      self(self, 2 * i, l, mid);
      if (q_dsu.comps > 1) self(self, 2 * i + 1, mid + 1, r);
    }
    ds.revert(tm);
  };
 
  dfs(dfs, 1, 0, mex);
 
  if (q_dsu.comps > 1) cout << -1 << "\n";
  else cout << ans << "\n";
}

signed main() {
  std::ios::sync_with_stdio(false);
  std::cin.tie(0);
  ll _ = 1;
  std::cin >> _;
  while (_--) solve();
  return 0;
}
```
