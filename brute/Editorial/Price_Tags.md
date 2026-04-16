[Price_Tags](https://codeforces.com/contest/2144/problem/D)

考虑到除完 $x$ 向上取整后得到一个值 $k + 1$ 我们可以知道 当且仅当 $kx < a_i \leq (k + 1)x$

所以根据这个想法，我们可以考虑直接枚举所有可能的x 时间复杂度是 $O(nlogn)$
