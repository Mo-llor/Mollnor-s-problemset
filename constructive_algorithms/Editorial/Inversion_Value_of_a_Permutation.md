因为考虑这个逆序值会很难想周全喵，所以我们决定正难则反，考虑所有不包含逆序值，因为 $k$ 是所有的，包含了逆序对的子数组喵，
所以设子数组的总数量 $|S|$ ， $|S| - k$ 就是所有上升子数组的数量喵，考虑一个长度为 $m$ 的子数组喵，他对整个数组的贡献就是 $\frac{m \times (m - 1)}{2}$
所以说，我们最后想要维护的就是一个长度为 $n$ 的排列，我们要把他们分成若干个升序的排列来让这个数组的总贡献是 $|S| - k$ 喵

于是，我们考虑dp的做法，因为数据量很小，所以我们可以考虑直接带着dp的过程一起转移喵

```cpp
void solve()
{	
	int n,k;
	cin>>n>>k;
	int tt = n * (n - 1) / 2 - k;
	vector<vector<vector<int>>> dp(n + 1,vector<vector<int>>(tt + 1));
	vector<vector<bool>> vis(n + 1,vector<bool>(tt + 1));
	vis[0][0] = 1;
	for (int i = 1 ; i <= n ; i++)
	{
		for (int j = 0 ; j <= tt ; j++)
		{
			for (int k = 1 ; k <= i ; k++)
			{
				if (j - k * (k - 1) / 2 >= 0)
				{
					if (vis[i - k][j - k * (k - 1) / 2])
					{
						dp[i][j] = dp[i - k][j - k * (k - 1) / 2];
						vis[i][j] = 1;
						dp[i][j].emplace_back(k);
					}
				}
			}
		}
	}
	int cur = n;
	if (!vis[n][tt])
	{
		cout<<0<<'\n';
		return;
	}
	for (const auto& i : dp[n][tt])
	{
		for (int j = cur - i + 1 ; j <= cur ; j++)
		{
			cout<<j<<' ';
		}
		cur -= i;
	}
	cout<<'\n';
	return;
}
```
