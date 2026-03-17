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
