# 说到树，怎么能没有线段树呢？

最最基础的线段树，能够维护区间和，rmq等等，性能十分强劲喵，还能进行区间加，区间乘法等一系列区间操作，只要处理好对应的lazy就完全没有问题喵

但是，只会这么多的话，就只能会做带修的前缀和，带修的rmq,但是很显然现在数值这么膨胀，完全不会考这种板板的题目喵

那线段树还能用来干什么呢？

# 线段树，顾名思义，维护线段和区间合并的操作特别好用喵

比如 [Wine Factory (Easy Version)](https://codeforces.com/contest/1919/problem/F1) 这道题喵，
就是用线段树来处理区间合并的喵，而它的 [hard version](https://codeforces.com/contest/1919/problem/F2) 同样也可以使用线段树来进行处理,但是相应的，复杂很多。

# 用上线段树，可以优化很多东西喵

1. 线段树优化dp
2. 线段树优化建图

我才疏学浅，目前只见过这两种优化喵呜呜

## 线段树优化dp

我见过的这个典题还是在atc的dp题单里的喵，但是我感觉我现在还没有理解透彻喵 (W - Intervals)[https://atcoder.jp/contests/dp/tasks/dp_w] 虽然看见区间操作我就往线段树上面想了，但是很显然我的dp算是最废物的那一档了喵

## 线段树优化建图

都是小巧思唉唉，([JOIST 2023] 护照 / Passport)[https://www.luogu.com.cn/problem/P9331]
