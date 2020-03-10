# 线段树

[TOC]

## 引言

​	线段树是一种二叉树形式的数据结构，它的每一个节点都保存一条线段，主要用于高效解决在连续区间的动态查询问题，且由于其接近于一棵满二叉树，所以每个操作的时间复杂度为O(logn)级别。

​	线段树的每个节点保存一个区间，而它的左右孩子分别保存父节点的左右半区间，而叶子节点它的左右区间相等，即只保存一个值。

![线段树](D:\Note\pictures\SegmentTree\线段树.png)

## 线段树的实现

​	线段树有多种应用，例如求最值，求和，求K值等，此处以用线段树求范围和为例。

###线段树的初始化

​	因为线段树是一棵二叉树，而二叉树是一个天然的递归结构，所以我们在构建线段树也是适用递归的方式来进行构建。

**代码：**

``` java
public class SegmentTree {
        private Node[] Tree;//使用数组表达线段树
        private int[] arry;//传入的数组

        private class Node {
            int left, right;//左右区间
            int value;//区间和
        }

        public SegmentTree(int[] arry, int l, int r) {//初始化
            Tree = new Node[(r - l) * 4];
            for (int i = 0; i < Tree.length; i++) {
                Tree[i] = new Node();
            }
            this.arry = arry;
            build(0, l, r);
        }

        /**
         * 构建线段树
         *
         * @param rt 当前节点索引
         * @param l  区间起点
         * @param r  区间结尾
         */
        private void build(int rt, int l, int r) {
            //初始化该节点的区间值
            Tree[rt].left = l;
            Tree[rt].right = r;
            if (l == r) {//如果是叶子节点则赋值
                Tree[rt].value = arry[l];
            } else {
                //否则，构建它的左右子区间
                int mid = (l + r) / 2;
                build(rt * 2 + 1, l, mid);
                build(rt * 2 + 2, mid + 1, r);
                Tree[rt].value = Tree[rt * 2 + 1].value + Tree[rt * 2 + 2].value;
            }
        }
}

```

### 线段树查询

​	线段树查询类似于二分查找，但也有一些不一样，因为二分查找要找的是一个数，而我们查询的确实一个区间，这就要考虑区间的范围，如果我们当前访问的节点恰好落在要查询的区间内，则我们可以直接将节点的值返回，否则就是要么全部不在查询区间里，这种情况直接不考虑，还有一种就比较麻烦，只有一部分落在查询区间内。

​	在有一部分落入查询区间的情况下，我们就将查询区间分开来看，如果查询区间有一部分在左区间，则到左节点继续查找，如果查询区域有一部分在右区间，则到右节点继续查找，最后结合两部分查找的值，对其处理后返回。

**代码：**

```java
/**
 * 查询操作
 *
 * @param rt 当前节点索引
 * @param l  查询区域左边边界
 * @param r  查询区域右边边界
 * @return 查询的结果
 */
public int query(int rt, int l, int r) {

    if (Tree[rt].left >= l && Tree[rt].right <= r) {
        //查询区间在当前节点的区间内
        return Tree[rt].value;
    }

    int mid = Tree[rt].left + ((Tree[rt].right - Tree[rt].left) / 2);
    int res = 0;
    if (l <= mid) {//如果查询区有一部分在左半区间
        res += query(rt * 2 + 1, l, r);//到该节点的左半区间查找
    }

    if (r > mid) {//如果查询区有一部分在右半区间
        res += query(rt * 2 + 2, l, r);//到该节点的右半区间查找
    }
    return res;//返回查询结果

}
```

### 单点更新

​	线段树的更新分为单点更新和区间更新，这里我们先实现单点更新。

```java
/**
 * 单点更新
 *
 * @param i    更新的点
 * @param data 更新的值
 * @param rt   当前节点索引
 */
public void update_one(int i, int data, int rt) {
    if (Tree[rt].left == Tree[rt].right && Tree[rt].left == i) {//找到了要更新的节点
        Tree[rt].value += data;
        return;
    }
    int mid = Tree[rt].left + ((Tree[rt].right - Tree[rt].left) / 2);
    if (i <= mid) {
        //左子树
        update_one(i, data, rt * 2 + 1);
    } else {
        //右子树
        update_one(i, data, rt * 2 + 2);
    }
    Tree[rt].value = Tree[rt * 2 + 1].value + Tree[rt * 2 + 2].value;//回溯处理
}
```

### 区间更新

​	其实我们完全可以使用多个单点更新来实现区间更新，但这样会导致许多不必要的时间浪费，因为这样会导致在回溯处理的时候要处理的非叶子节点非常多，而如果一次性全部更新完，那么该操作的时间复杂度绝对高于O(logn),所以，此处我们要引入一种机制来延迟更新那些暂时用不到的节点，在我们需要用到它们的时候再进行更新操作，为此我们要在Node节点加入一个标记，用以标记是否要更新。

**代码：**

```java
 private class Node {//新的Node
        int left, right;//左右区间
        int value;//区间最值
        int add;//标记
    }

/**	
 * 将当前节点的更新应用到左右子节点上
 *
 * @param rt 当前节点
 */

private void pushDown(int rt) {
    if (Tree[rt].add != 0) {
        Tree[rt * 2 + 2].add += Tree[rt].add;//给左右子节点加上要更新的值，因为孩子节点可能多次延迟更新，所以要+=
        Tree[rt * 2 + 1].add += Tree[rt].add;
        Tree[rt * 2 + 1].value += (Tree[rt * 2 + 1].right - Tree[rt * 2 + 1].left + 1) * Tree[rt].add;//将这个区间整体的值加上data
        Tree[rt * 2 + 2].value += (Tree[rt * 2 + 2].right - Tree[rt * 2 + 2].left + 1) * Tree[rt].add;
        Tree[rt].add = 0;//标记清除
    }
}

/**
 * 更新区间
 *
 * @param L    要更新区间的左值
 * @param R    要更新区间的右值
 * @param data 要更新的值
 * @param rt   当前节点
 */
public void update(int L, int R, int data, int rt) {
    if (L <= Tree[rt].left && R >= Tree[rt].right) {//当前区间在要更新的区间内部
        Tree[rt].add += data;
        Tree[rt].value += (Tree[rt].right - Tree[rt].left + 1) * data;//将这个区间整体的值加上data
        return;
    }
    pushDown(rt);//将标记下传
    int mid = Tree[rt].left + ((Tree[rt].right - Tree[rt].left) / 2);
    if (L <= mid) {
        //更新左子树部分
        update(L, R, data, rt * 2 + 1);
    }

    if (R > mid) {
        //更新右子树部分
        update(L, R, data, rt * 2 + 2);
    }

    Tree[rt].valu e = Tree[rt * 2 + 1].value + Tree[rt * 2 + 2].value;//回溯处理
}

public int query(int rt, int l, int r) {

        if (Tree[rt].left >= l && Tree[rt].right <= r) {
            //查询区间在当前节点的区间内
            return Tree[rt].value;
        }
        pushDown(rt);//在查询的时候查看是否需要将节点更新（新添）
        int mid = Tree[rt].left + ((Tree[rt].right - Tree[rt].left) / 2);
        int res = 0;
        if (l <= mid) {//如果查询区有一部分在左半区间
            res += query(rt * 2 + 1, l, r);//到该节点的左半区间查找
        }

        if (r > mid) {//如果查询区有一部分在右半区间
            res += query(rt * 2 + 2, l, r);//到该节点的右半区间查找
        }
        return res;//返回查询结果

    }
```

## 线段树优化——Zkw树

​	*暂时还未看懂，先把代码放上*

**代码：**

```java
public class ZKW {
    private int[] tree;
    private int M;

    public ZKW(int n, int arr[]) {

        int j = 0;
        tree = new int[n << 2];
        for (M = 1; M <= n + 1; M <<= 1) ;
        for (int i = M + 1; i <= M + n; i++) tree[i] = arr[j++];
        for (int i = M - 1; i != 0; i--) pushUp(i);
    }

    private void pushUp(int rt) {
        tree[rt] = tree[rt << 1] + tree[rt << 1 | 1];
    }

    public void update(int p, int data) {
        p += M;
        tree[p] = data;
        for (p >>= 1; p != 0; p >>= 1) pushUp(p);
    }

    public int query(int l, int r) {
        int res = 0;

        for (l = l + M - 1, r = r + M + 1; (l ^ r ^ 1) != 0; l >>= 1, r >>= 1) {
            if ((~l & 1) != 0) {
                res += tree[l ^ 1];
            }
            if ((r & 1) != 0) {
                res += tree[r ^ 1];
            }
        }

        return res;

    }

}

```