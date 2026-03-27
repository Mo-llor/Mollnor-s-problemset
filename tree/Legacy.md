[Legacy](https://codeforces.com/contest/786/problem/B)

线段树优化建图的板喵，[这个blog带图有助于理解喵](https://www.luogu.com.cn/article/boza3v30)

我一开始还想着在一棵树上建双边，发现这种情况很坏喵，因为从源点出去，只要进树，就可以到达所有的节点，这很不对喵，所以我们要开两棵树，一棵树往上走，另一棵树往下走喵。这样的建好图之后，我们跑一遍dij就能过了喵

```cpp
struct segtree
{
	int min_,max_,n_;
	segtree(int n):min_(1),max_(n),n_(n){}
private:
	void in_build(int i,int l,int r,vector<vector<pii>>& vec)//上树
	{
		if (l == r)
		{
			vec[l].emplace_back(i + n_,0);
			return;
		}
		vec[2 * i + n_].emplace_back(i + n_,0);
		vec[2 * i + 1 + n_].emplace_back(i + n_,0);
		int mid = (l + r)/2;
		in_build(2 * i,l,mid,vec);
		in_build(2 * i + 1,mid + 1,r,vec);
	}
	void q_in(int cur,int val,int i,int x,int y,int l,int r,vector<vector<pii>>& vec)
	{
		if (x <= l && r <= y)
		{
			vec[i + n_].emplace_back(cur,val);
			return;
		}
		int mid = (l + r) / 2;
		if (y <= mid) q_in(cur,val,2 * i,x,y,l,mid,vec);
		else if (x > mid) q_in(cur,val,2 * i + 1,x,y,mid + 1,r,vec);
		else
		{
			q_in(cur,val,2 * i,x,y,l,mid,vec);
			q_in(cur,val,2 * i + 1,x,y,mid + 1,r,vec);
		}
		return;
	}
	void out_build(int i,int l,int r,vector<vector<pii>>& vec)//下树
	{
		if (l == r)
		{
			vec[i + 5 * n_].emplace_back(l,0);
			return;
		}
		vec[i + 5 * n_].emplace_back(2 * i + 5 * n_,0);
		vec[i + 5 * n_].emplace_back(2 * i + 1 + 5 * n_,0);
		int mid = (l + r)/2;
		out_build(2 * i,l,mid,vec);
		out_build(2 * i + 1,mid + 1,r,vec);
	}
	void q_out(int cur,int val,int i,int x,int y,int l,int r,vector<vector<pii>>& vec)
	{
		if (x <= l && r <= y)
		{
			vec[cur].emplace_back(i + 5 * n_,val);
			return;
		}
		int mid = (l + r) / 2;
		if (y <= mid) q_out(cur,val,2 * i,x,y,l,mid,vec);
		else if (x > mid) q_out(cur,val,2 * i + 1,x,y,mid + 1,r,vec);
		else
		{
			q_out(cur,val,2 * i,x,y,l,mid,vec);
			q_out(cur,val,2 * i + 1,x,y,mid + 1,r,vec);
		}
		return;
	}
public:
	void query(int cur,int val,int l,int r,vector<vector<pii>>& vec){return q_out(cur,val,1,l,r,min_,max_,vec);}
	void invquery(int l,int r,int val,int cur,vector<vector<pii>>& vec){return q_in(cur,val,1,l,r,min_,max_,vec);}
	void in_build(vector<vector<pii>>& vec){return in_build(1,min_,max_,vec);}
	void out_build(vector<vector<pii>>& vec){return out_build(1,min_,max_,vec);}
};


void solve()
{	
	int n,q,s;
	cin>>n>>q>>s;
	vector<vector<pii>> g(9 * n + 1);
	segtree sgt(n);
	sgt.in_build(g);
	sgt.out_build(g);
	while(q--)
	{
		int typ;
		cin>>typ;
		if (typ == 1)
		{
			int v,u,w;
			cin>>v>>u>>w;
			g[v].emplace_back(u,w);
		}
		else
		{
			int l,r,v,w;
			cin>>v>>l>>r>>w;
			if (typ == 2) sgt.query(v,w,l,r,g);
			else if (typ == 3) sgt.invquery(l,r,w,v,g);
		}
	}
	vector<int> ans(9 * n + 1,INF);
	ans[s] = 0;
	priority_queue<pii,vector<pii>,greater<pii>> prpr;
	prpr.emplace(0,s);
	while(!prpr.empty())
	{
		auto [val,cur] = prpr.top();
		prpr.pop();
		if (val > ans[cur]) continue;
		for (const auto& [nxt,v] : g[cur])
		{
			if (ans[nxt] > val + v)
			{
				ans[nxt] = val + v;
				prpr.emplace(ans[nxt],nxt);
			}
		}
	}
	for (int i = 1; i <= n; i++)
	{
		if (ans[i] == INF)
		{
			cout<<-1<<' ';
		}
		else cout<<ans[i]<<' ';
	}
    return;
}
```
