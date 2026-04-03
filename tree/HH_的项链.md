[HH_的项链](https://www.luogu.com.cn/problem/P1972)

二维偏序各种奇奇怪怪的东西喵，你永远都不知道什么会变成要维护的两个变量喵

就比如说这道题喵，如果范围是1e5我们自然可以拿莫队去草喵，但是很显然喵，给的是1e6喵，很可怕喵

那我们要怎么维护喵？

我们发现，当一个区间里面，如果有很多颜色一样的球的话，我们只要记录这个区间的第一个就好了喵，放一个pre数组来存这个数字的上一个是什么喵？所以就有了 $l \leq x \leq r,pre[x_i] < l$ 喵，这样我们就可以很轻松地去使用主席树来在线维护了喵

不过时间卡得很死喵，要赌评测机波动才能过去喵

```cpp
int pre[N];
void solve()
{	
	int n;
	cin>>n;
	PersistentSegtree sgt(0,n);
	for (int i = 1 ; i <= n ; i++)
	{
		int x;
		cin>>x;
		sgt.modify(pre[x],{1});
		pre[x] = i;
	}
	int q;
	cin>>q;
	for (int i = 1 ; i <= q; i++)
	{
		int l,r;
		cin>>l>>r;
		cout<<sgt.query_reg(l - 1,r,0,l - 1)<<'\n';
	}
	cout<<'\n';
	return;
}
```
