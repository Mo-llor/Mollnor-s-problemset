主席树最最经典的内容当然就是维护区间第k大了喵

[可持久化线段树1](https://www.luogu.com.cn/problem/P3919)

[可持久化线段树2](https://www.luogu.com.cn/problem/P3834)

上面两个板题喵，就是用到了主席树最基础的两个作用喵，可持久化，和区间第k小喵

还有一个用到了可持久化的题目就是 [Enchanted](https://codeforces.com/gym/105139/problem/F) 其实这道题也挺板的来着，只是线段树处理数据的那部分有点难写喵

下面是代码

```cpp
struct PersistentSegtree
{
    struct Info
    {
        ll val;
        Info operator+(const Info&x)
        {
            return {val + x.val};
        }
        void operator+=(const Info&x){*this = *this + x;}
    };
    const ll ie = 0;//所谓单位元
    //根节点应该是单位元喵如果是求和就是0，求max就是-inf
    struct Node
    {
        int ls,rs;
        Info v;
    };
    int max_,min_;
    vector<int> root;
    vector<Node> info;
    struct lazy {/* data */};
    PersistentSegtree(int l,int r):max_(r),min_(l),root(1){info.push_back({0,0,{ie}});}//数据范围喵
private:
    void pull(int p){info[p].v = info[info[p].ls].v + info[info[p].rs].v;}
    int set(int p,int pos,int l,int r,Info x)//这个是用来设置初始值的喵
    {
        if (!p)
        {
            p = info.size();
            info.emplace_back(0,0,Info{ie});           
        }
        if (l == r) {info[p].v = x;return p;}
        int mid = l + (r - l) / 2;
        if (pos <= mid) 
        {
            int newls = set(info[p].ls,pos,l,mid,x);
            info[p].ls = newls;
        }
        else 
        {
            int newrs = set(info[p].rs,pos,mid + 1,r,x);
            info[p].rs = newrs;
        }
        pull(p);
        return p;
    }
    int operation(int p,int pos,int l,int r,Info x)//这个是修改喵
    {
        auto t = info[p];
        info.emplace_back(t);
        p = info.size() - 1;
        if (l == r) {info[p].v = x;return p;}//这里就是具体修改的地方喵
        int mid = l + (r - l) / 2;
        if (pos <= mid)
        {
            int newls = operation(info[p].ls,pos,l,mid,x);
            info[p].ls = newls;
        }
        else
        {
            int newrs = operation(info[p].rs,pos,mid + 1,r,x);
            info[p].rs = newrs;
        }
        pull(p);
        return p;
    }
    Info query(int p,int l,int r,int ql,int qr)
    {
        if (!p || ql > qr) return {ie};
        int mid = l + (r - l) / 2;
        if (ql <= l && qr >= r) return info[p].v;
        if (qr <= mid) return query(info[p].ls,l,mid,ql,qr);
        else if (ql > mid) return query(info[p].rs,mid + 1,r,ql,qr);
        else return query(info[p].ls,l,mid,ql,qr) + query(info[p].rs,mid + 1,r,ql,qr);
    }
    int ksmall(int k,int u,int v,int l,int r)
    {
        //如果题目有要求的话，可以在这里加上防呆l>r
        ll mid = l + (r - l) / 2;
        if (l == r) return l;
        ll cnt = info[info[v].ls].v.val-info[info[u].ls].v.val;
        if (k <= cnt) return ksmall(k,info[u].ls,info[v].ls,l,mid);
        else return ksmall(k - cnt,info[u].rs,info[v].rs,mid + 1,r);
    }
public:
    void set(int pos,Info val) {root[0] = set(root[0],pos,min_,max_,val);}//这里是不增加新版本的设置初始值喵
    void modify(int pos,Info val) {//这个是增加新版本的修改喵
        int newroot = operation(root.back(),pos,min_,max_,val);
        root.emplace_back(newroot);
    }
    Info query(int q,int l,int r){return query(root[q],min_,max_,l,r);}//查询某个版本的区间喵
    int ksmall(int k,int l,int r){return ksmall(k,root[l - 1],root[r],min_,max_);}
};
```

当然主席树能维护的可不只有区间第k小或者区间第k大，主席树还能维护区间mex

### 这里是主席树维护区间mex的函数喵

```cpp
    int mex(int lv,int rv,int l,int r)
    {
        //如果题目有要求的话，可以在这里加上防呆l>r
        ll mid = l + (r - l) / 2;
        if (l == r) return l;
        ll mp = info[info[rv].ls].v.val;
        if (mp < lv) return mex(lv,info[rv].ls,l,mid);
        else return mex(lv,info[rv].rs,mid + 1,r);
    }
```
代码很短喵，这时候主席树维护的还是权值线段树，知识里面存的是**每个元素出现的最大的下标**喵，整个树维护的是下标的min，同样是对数组建前缀树，知识因为维护的值不可减法，所以我们对于[l,r]这个区间，我们只能去找第r个版本的主席树，这时候呢喵，我们就要去看看它的左儿子，如果左儿子的val小于L的话，那mex肯定是在左边喵，反之就是在右边。

如此在线段树上二分就能够找到我们的mex了喵。

到这里还是没完，主席树不止能够维护眼前的苟且，我们还能找到在区间中离给定值x最近的值喵

# 二维偏序

[换根区间lca](/tree/cf105911l.md)这题的在线做法就用到了这个东西喵

主席树还能维护二维区间上的动态修改问题喵，就像是这个[Sensors](https://codeforces.com/gym/105385/problem/E) [题解喵](/tree/cf105385E.md)然后这道题还能用线段树来写喵；然后，还有这道题喵[Unusual Entertainment](https://codeforces.com/contest/1899/problem/G) [思路喵](/tree/cf1899.md)也是要转化才行喵，似乎主席树维护这样的二维问题还算是挺常见的喵

[HH 的项链](https://www.luogu.com.cn/problem/P1972) [题解喵](/tree/HH_的项链.md)这个也是差不多的思路喵，但是因为我找错了二维偏序的两个变量，导致我想了两个小时没想出来喵，正解是离线写的喵，但我毕竟是在练主席树喵，所以就写了在线的写法喵

[Hidden Knowledge of the Ancients](https://codeforces.com/contest/2149/problem/E) 其实双指针能写，但是我拿主席树草过去了，感觉自己有点过拟合了喵

# 差分

主席树有差分的性质喵，只要维护的是可减的信息，我们就能通过运算，算出那一段上面的线段树，之后再进行线段树的操作就好搞了喵，就比如说这个 [树链上第k小喵](https://www.luogu.com.cn/problem/P2633) 还有[一些要注意的小问题喵](/tree/Count_on_a_tree.md) 我们就需要用到这个性质喵，用主席树求区间第k小也是看在他可差分的性质上面喵

会普通的树上差分了[Odd_Mineral_Resource](https://codeforces.com/contest/1479/problem/D) 那来异或吧喵，因为异或有差分的性质喵，好玩喵，很显然这和校赛的那题是差不多的难度喵
