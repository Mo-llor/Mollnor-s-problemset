我主要变动的点就在这两个函数上面喵，我一开始是因为没有传版本号，只传了节点对应下标进去，写挂了好几发（对主席树本质不了解导致的喵

按道理来说理解树上前缀和还有主席树的可差分的性质之后就很好理解这道题了喵，所以也没有什么好讲的了喵（

```cpp
    int ksmall(int k,int du,int dv,int dlca,int dflca,int l,int r)
    {
        //如果题目有要求的话，可以在这里加上防呆l>r
        ll mid = l + (r - l) / 2;
        if (l == r) return l;
        ll cnt = info[info[dv].ls].v.val+info[info[du].ls].v.val - info[info[dlca].ls].v.val - info[info[dflca].ls].v.val;
        if (k <= cnt) return ksmall(k,info[du].ls,info[dv].ls,info[dlca].ls,info[dflca].ls,l,mid);
        else return ksmall(k - cnt,info[du].rs,info[dv].rs,info[dlca].rs,info[dflca].rs,mid + 1,r);
    }
public:
    int ksmall(int k,int du,int dv,int dlca,int dflca){return ksmall(k,root[du],root[dv],root[dlca],root[dflca],min_,max_);}
    void modify(int ver,int pos,Info val) {
        int newroot = operation(root[ver],pos,min_,max_,val);
        root.emplace_back(newroot);
    }
```
