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
    int set(int p,int pos,int l,int r,Info x)
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
    int operation(int p,int pos,int l,int r,Info x)
    {
        auto t = info[p];
        info.emplace_back(t);
        p = info.size() - 1;
        if (l == r) {info[p].v = x;return p;}
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
    void set(int pos,Info val) {root[0] = set(root[0],pos,min_,max_,val);}
    void modify(int pos,Info val) {
        int newroot = operation(root.back(),pos,min_,max_,val);
        root.emplace_back(newroot);
    }
    Info query(int q,int l,int r){return query(root[q],min_,max_,l,r);}
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


