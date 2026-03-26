你知道的，我对分类讨论向来不擅长，对于这种简单的分类讨论还是做不明白喵

这道题其实不难来着……构造好像都能这么说喵

我们其实可以把所有的边都拿偶数条出来，因为这样一定是对称的。之后我们就要开始讨论了喵

1. 只在剩下的边里面挑两条，要把剩下的数量为1的边存到d里面，按长度排序喵，我们挑相邻的两条边进行检查，如果 $sumn + d_i > d_{i - 1}$ 拿我们就立即输出 $sumn + d_i + d_{i - 1}$ 喵，如果没有满足的就看下一条喵。
2. 在剩下的边里面挑一条边，同样我们遍历一遍进行检查就好了喵
3. 如果上面的都不满足，拿我就一条边也不拿，检查是否有 $n - d.size() >= 3$ 喵，如果这个条件都不符合的话，我们就只能输出0了喵

如果情况 2 能够在某条单边 $b_k$ 上成立（即 $sumn > b_k$），那么对于情况 1，$sumn + b_{k+1} > b_k$ 这个不等式绝对是成立的（因为 $b_{k+1} > 0$ 喵）！这就意味着，只要情况 2 能成，情况 1 必定会在同一个位置或更早的位置成立，而且周长更大！


代码喵
```cpp
void solve()
{	
	int n;
	cin>>n;
	vector<int> a(n);
	map<int,int> mp;
	for (int i = 0; i < n; i++) 
	{
		cin>>a[i];
		mp[a[i]]++;
	}
	int sumn = 0;
	vector<int> b;
	for (const auto&[num,cnt] : mp)
	{
		if (cnt & 1)
		{
			b.emplace_back(num);
		}
		sumn += (cnt / 2) * num * 2;
	}
	ranges::sort(b,greater{});
	int le = b.size();
	for (int i = 1 ; i < le ; i++)
	{
		if (sumn + b[i] > b[i - 1])
		{
			cout<<sumn + b[i] + b[i - 1]<<'\n';
			return;
		}
	}
	for (const auto& i : b)
	{
		if (sumn > i)
		{
			cout<<sumn + i<<'\n';
			return;
		}
	}
	if (n - le >= 3)
	cout<<sumn<<'\n';
	else
	{
		cout<<0<<'\n';
	}
	return;
}
```
