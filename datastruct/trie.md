字典树，是个好东西，思来想去，在string和ds这两个分支里思索片刻，最后还是决定把这个东西放进ds里面，string那边，等我学了ac自动机之后再放个传送门过来喵

字典树，用来查找字符串只是最基本的功能喵

# 普通trie

就是下面的这个版本喵,可以更改info里面维护的信息来进行不同信息的查找喵。现在的是用来查 $t$ 是多少个模式串 $s$ 的前缀喵

```cpp
struct trie
{
    struct Info
    {
        int c[63] = {0};
        int pr = 0;
        //bool is_end = false 这个是查有没有
    };
    vector<Info> info;
    trie(){info.emplace_back();}
    void insert(const string& s)
    {
        int u = 0;
        for (const auto& i : s)
        {
            int nxt;
            if (i <= 'z' && i >= 'a') nxt = i - 'a';
            else if (i <= 'Z' && i >= 'A') nxt = i - 'A' + 26;
            else if (i <= '9' && i >= '0') nxt = i - '0' + 52;
            if (!info[u].c[nxt])
            {
                info[u].c[nxt] = info.size();
                info.emplace_back();
            }
            info[u].pr++;
            u = info[u].c[nxt];
        }
        info[u].pr++;
    }
    int search(const string& s)
    {
        int u = 0;
        for (const auto& i : s)
        {
            int nxt;
            if (i <= 'z' && i >= 'a') nxt = i - 'a';
            else if (i <= 'Z' && i >= 'A') nxt = i - 'A' + 26;
            else if (i <= '9' && i >= '0') nxt = i - '0' + 52;
            if (!info[u].c[nxt]) return 0;
            u = info[u].c[nxt];
        }
        return info[u].pr;
    }
};
```

# 01trie

这个就是用来查血异或相关的东西了喵

```cpp
struct trie
{
    struct Info
    {
        int c[2] = {0};
    };
    vector<Info> info;
    int bit;
    trie(int n):bit(n){info.emplace_back();}
    void insert(int x)
    {
        int u = 0;
        for (int i = bit ; i >= 0 ; i--)
        {
            int bits = ((x >> i) & 1);
            if (!info[u].c[bits])
            {
                info[u].c[bits] = info.size();
                info.emplace_back();
            }
            u = info[u].c[bits];
        }
    }
    int max_xor(int x)
    {
        int ans = 0;
        int u = 0;
        for (int i = bit ; i >= 0 ; i--)
        {
            int bits = ((x >> i)& 1);
            if (info[u].c[bits ^ 1])
            {
                ans |= (1ll<<i);
                u = info[u].c[bits ^ 1];
            }
            else if (info[u].c[bits])
            {
                u = info[u].c[bits];
            }
            else break;
        }
        return ans;
    }
};
```

最经典的用处当然是查询最大异或对了喵，也就是[这道题喵](https://www.luogu.com.cn/problem/P10471)

只要稍作变换就能做到查询异或和 $\leq k$ 的数量喵，就像是这道题喵，[Beautiful Subarrays](https://codeforces.com/contest/665/problem/E)

字典树除了能查询最大异或对以外，最小异或对也可以喵，当然只要不像是 [这个](/bit/Editorial/cf106259d.md) 就行喵，因为显然最小的异或对一定是排完序之后相邻的那两个喵，但是我们并不是总能排完序之后遍历一遍喵，所以像是[虚空输电](https://acm.aiecnu.cn/contest/737/problem/10059)的情况，字典树的做法要比排序方便一点喵
