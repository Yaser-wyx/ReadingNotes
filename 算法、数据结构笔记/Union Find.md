# 并查集

[TOC]

## 引言

​	并查集(Union Find)是一种树形的数据结构，它是专门用来处理不相交集合的合并及查询问题的，它主要支持两个操作，一个是union操作，即将两个不相交的集合的合并，另一个是find操作，即查找该元素属于哪一个集合，以此衍生出来的一个操作是isConnected，也就是判断两个元素是否连接，即是否同属于一个集合中。

## 并查集的存储结构及实现##

​	并查集的存储结构可以使用数组也可使用链表，在这里我们使用数组作为实现的方式，即定义一个数组用于存储并查集的相关信息。

​	我们先实现并查集的简单版本，该版本的查找，也就是find操作是O(1)级别的，但union操作耗时较长，是O(N)级别的，但我们暂时先不管这些，先将并查集的思路理解了，再实现真正的并查集。

### 并查集简易版###


​	在我们的简易版并查集中，定义一个叫id的数组，该数组用于存放所有的并查集元素。数组的索引表示元素，而数组的值代表了其所属的集合。

**图示：**![quick_find](D:\Note\pictures\Union Find\quick_find.png)

​		*在上面的图片里，`0,2,4,6,8`属于同一个集合，集合为0，`1,3,5,7,9`属于同一个集合，集合为1*

**代码定义：**

```java
public class Quick_Find implements Union_Find{
    private int[] id;//所有集合元素用数组表示，索引表示元素，值表示所属的集合名
    private int count;//元素个数

    public Quick_Find(int count) {//初始化
        this.count = count;
        id = new int[count];
        for (int i = 0; i < count; i++) {//初始时，每一个元素自身就是一个集合
            id[i] = i;
        }
    }
    public int find(int p) {//查找p这个元素所属的集合
    }

    public boolean isConnected(int p, int q) {//判断p和q这两个元素是否能连接
    }

    public void union(int p, int q) {//将p这个元素所属的所有集合数据合并到q元素所属的集合里面
    }
}
```

#### find实现####

​	find操作就是查找元素所属的集合，在我们这个简易版本中，这一步十分简单，直接返回该索引的值就可以了

```java
public int find(int p) {//查找p这个元素所属的集合
        assert p >= 0 && p < count;
        return id[p];//返回所属的集合
    }
```

#### isConnected实现####

​	isConnected操作就是用于判断两个元素是否同属于一个集合中，如果在一个集合中就返回`true`，否则为`false`。而我们要判断这两个是否在一个集合只需要先找到两个元素所属的集合，然后判断即可。

```java
public boolean isConnected(int p, int q) {//判断p和q这两个元素是否能连接
        int pId = find(p);//查找p所属的集合
        int qId = find(q);//查找q所属的集合
        return pId == qId;//判断啷个集合是否一样
    }
```

#### union实现####

​	union实现可能稍微复杂一点，首先我们要找到两个要合并元素的集合是哪个，如果两者所属同一个集合，则什么也不做，否则将其中一个元素所在集合中所有与之连接的元素，合并到另一个元素所在的集合中。

​	至于为什么需要一个集合的所有元素都改为另一个集合，是因为如果我们在合并两个元素时，只修改其中一方到另一方的集合中，那么在未修改前与该元素连接的其他元素，在修改后都不再和它属于一个集合了，这明显不符合逻辑，因为我们要的是这两个元素合并的同时，这两个元素属于的两个不同集合中的其他元素，可以通过这两个元素的合并也连接起来，这才是并查集的核心所在。

**图示：**![union](D:\Note\pictures\Union Find\union.png)

**代码：**

```java
 public void union(int p, int q) {//将p这个元素所属的所有集合数据合并到q元素所属的集合里面
        assert p >= 0 && p < count && q >= 0 && q < count;	
        int qId = find(q);
        int pId = find(p);
        if (qId == pId) {//如果已经在一个集合里面了就不需要合并操作了
            return;
        }

        for (int i = 0; i < count; i++) {//否则，将所有与p连接的元素合并到q所在的集合里面，顺序可调
            if (id[i] == pId) {
                id[i] = qId;
            }
        }
    }
```

#### 小结####

​	以上就是简单版本的并查集，因为是简单的，所以效率上不怎么样，甚至可以用糟糕一词来形容，虽然我们的find操作是O(1)的，但是union操作却是O(n)级别的，一次合并我们就需要将所有的数据都遍历一遍才行，这显然是低效的，那有没有更好的实现方法呢，当然，这就是我们接下去要介绍的另一种实现方式，使用父节点的引用来实现。

### 并查集###

​	在我们新的实现中，我们使用树形结构，即将每一个元素视为一个节点，节点的值指向它的父节点，而一个集合中的根节点指向它自己本身，换句话说，只要是一个集合内的元素，它们的根节点都是同一个，虽然这样会使find操作变复杂，但union比之前效率高出许多。

**图示：**

![union_find](D:\Note\pictures\Union Find\union_find.png)

**代码定义：**

```java
public class Quick_Union implements Union_Find {
    private int[] parent;//所有集合元素用数组表示，索引表示元素，值表示该元素的父节点(不同处)
    private int count;//元素个数

    public Quick_Union(int n) {
        count = n;
        parent = new int[count];
        for (int i = 0; i < count; i++) {//初始时，每一个元素自身就是一个集合
            parent[i] = i;

        }
    }

    public int find(int p) {//查找p这个元素所属的集合
    }

    public void union(int p, int q) {//将p这个元素所属的所有集合数据合并到q元素所属的集合里面
    }	

    public boolean isConnected(int p, int q) {
    }
}

```

​		*以上的代码定义与简易版的完全一致，仅仅是id数组改名为了parent，只是为了更好地区分含义*

#### find实现####

​	在新的find里面，我们不断向上搜索要查找元素的父节点，直到根节点，因为在一个集合内，每一个元素的根节点都是唯一的，所以我们可以把一个集合的根节点做为该集合的唯一标识，最后我们只需要将这个根节点返回就可以了。

**代码：**

```java
    public int find(int p) {//查找p所在的集合
        assert p >= 0 && p < count;
        int p_parent = parent[p];//p的父节点
        while (parent[p_parent] != p_parent) {//不断地向上查找父节点，直到根节点，因为根节点指向自己
            p_parent = parent[p_parent];//将p_parent的父节点赋值给p_parent，从而可以不断向上搜索
        }
        return p_parent;//此时p_parent已经指向了p的根节点
    }
```

####isConnected实现

​	isConnected的实现和简易版本完全一致。

**代码：**

``` java
    public boolean isConnected(int p, int q) {
        return find(q) == find(p);//判断是否连接
    }
```

#### union实现####

​	在新的union中的实现要比之前的简单许多，我们只需要将要合并的两个元素的根节点先找到，然后判断二者是否同属于一个集合，即它们的根节点是否一致，如果不一致，我们只需要将任意一方的根节点赋给另一方就可以了，因为这样操作之后，之前是根节点的节点也变成了另一个根节点的子节点了，那么之前为根节点的节点现在它的根节点不再指向自己，而是另一个根节点。（这可能有点绕，大家还是看图吧.. ）

**图示：**

![union2](D:\Note\pictures\Union Find\union2.png)

**代码：**

```java
public void union(int p, int q) {//合并两个集合
        int p_Parent = find(p);//找到p元素的根节点
        int q_Parent = find(q);//找到q元素的根节点

        if (p_Parent != q_Parent) {//如果不在一个集合内
            parent[p_Parent] = q_Parent;//将一方的根节点赋值给另一方
        }
    }
```

#### 小结####

​	该并查集先比之前的简易版效率上大概提高了一些，但还是不尽人意，这是为什么呢。其实我们在合并的时候没有考虑并查集每一棵树的深度和子节点的个数。

**图示：**

![bad](D:\Note\pictures\Union Find\bad.png)

​										*`（糟糕的情况）`*

​	我们在合并的时候是选择任意一方直接挂载在了另一方下面了，而对于树的深度和子节点的个数不做要求，那么考虑这么一种情况，我们在合并的时候一直都是将树的深度或子节点的个数大的一方作为树的深度和子节点的个数小的一方的子树而不断附加上去，则这时候，该树形结构又变成了我们之前实现的链式结构了，此时我们的find操作退化成了O(N)，那么我们有没有好的方法优化呢，是有的，而且有好几种方式，它们主要都是对子节点个数和树深度做的优化，下面我们依次介绍一下。

### 并查集基于size的优化###

​	在该版本的优化中，我们是对union操作进行优化，之前我们在做union操作时没有判断哪一方节点数更多，那么我们现在把这一判断加上，即在合并前先判断哪一方树的子节点数多，那么我们把子节点数少的一方附加在子节点数多的一方就可以了。

**图示：**

![size优化](D:\Note\pictures\Union Find\size优化.png)

**代码定义：**

```java
public class Quick_Union implements Union_Find {
    private int[] parent;//所有集合元素用数组表示，索引表示元素，值表示该元素的父节点(不同处)
    private int count;//元素个数
  	private int[] size;//新加的数组，该数组表示每一个节点，若它作为根节点，则它的子节点数有多少
  
    public Quick_Union(int n) {
        count = n;
        parent = new int[count];
      	size = new int[count];
        for (int i = 0; i < count; i++) {//初始时，每一个元素自身就是一个集合
            parent[i] = i;
           	size[i] = 1;//初始化时，每一个节点如果作为根节点，则子节点只有它自己。
        }
    }

   public int find(int p) {//查找p所在的集合
        assert p >= 0 && p < count;
        int p_parent = parent[p];//p的父节点
        while (parent[p_parent] != p_parent) {//不断地向上查找父节点，直到根节点，因为根节点指向自己
            p_parent = parent[p_parent];//将p_parent的父节点赋值给p_parent，从而可以不断向上搜索
        }
        return p_parent;//此时p_parent已经指向了p的根节点
    }

    public void union(int p, int q) {//将p这个元素所属的所有集合数据合并到q元素所属的集合里面
      //要重写
    }

     public boolean isConnected(int p, int q) {
        return find(q) == find(p);//判断是否连接
    }
}

```

​	在新的定义代码中，我们要新加一个名为size的数组，该数组表示每一个节点，若它作为根节点，则它的子节点数有多少个，基于这个数组，那我们在union操作的时候就可以判断哪一方子节点数更少了。

####union优化的实现####

​	在新的实现中，我们永远将节点数少的一方附加到节点数多的一方，这样就可以保证不会出现链式结构了。

```java
 public void union(int p, int q) {
        int p_Parent = find(p);//先找到p元素的根节点
        int q_Parent = find(q);//先找到q元素的根节点
   
        if (p_Parent != q_Parent) {//如果不在一个集合内
            if (size[p_Parent] < size[q_Parent]) {//如果p_Parent的子节点数更少
                parent[p_Parent] = q_Parent;//将p_Parent的根节点改为q_Parent的根节点。
                size[q_Parent] += size[p_Parent];//更新size值
            } else {//如果q_Parent的子节点数更少
                parent[q_Parent] = p_Parent;//将q_Parent的根节点改为p_Parent的根节点。
                size[p_Parent] += size[q_Parent];//更新size值
            }
        }
    }
```

​	因为我们将一棵树附加在了另一棵树上，所以被附加的那棵树的子节点增加的，就是附加的那棵树的节点数。

###并查集基于rank的优化###

​	在基于size的优化中，我们还有一些可以优化的地方，我们来考虑这么一种情况，假如我们现在要合并的两个元素p和q，p元素的子节点数远大于q节点的子节点数，但是，这p这棵树的深度只有1，而q的深度等于节点数，而按照我们的size优化，我们是把q加在了p上，但其实应该是p加在q上，因为虽然q的子节点多，但它的深度小。

***例子***

**图示：**

![bad2](D:\Note\pictures\Union Find\bad2.png)

​	我们将4与2合并，按照size优化，我们是将4所在的树附加在了2上。

![bad3](D:\Note\pictures\Union Find\bad3.png)

​	而我们其实应该把2所在的树附加在4上。

![bad4](D:\Note\pictures\Union Find\bad4.png)

​	这样树的深度更少，搜索起来就更快了。

**代码定义：**

```java
public class Quick_Union3 {
    private int[] parent;
    private int count;
    private int[] rank;//只是名字改了一下，rank就是指以该元素为根节点，该树的深度是多少

    public Quick_Union3(int n) {
        count = n;
        rank = new int[count];
        parent = new int[count];
        for (int i = 0; i < count; i++) {
            parent[i] = i;
            rank[i] = 1;//初始化时，树的深度为1
        }
    }
	//不变
    public int find(int p) {
        while (parent[p] != p) {
            p = parent[p];
        }
        return p;
    }

    public void union(int p, int q) {
		//重写
    }
	
  	//不变
    public boolean isConnected(int p, int q) {
        return find(q) == find(p);
    }
}

```

​	在基于rank的优化里，定义与size其实完全一样，唯一的改变只是改了个变量名，在这里就不在赘述了。

#### union优化的实现####

​	在新的union优化里，我们不再以节点个数为判断的基准，而是以树的深度作为判断的基准。我们只需将树深度小的一方加到树深度大的一方即可。

**代码：**

```java
  public void union(int p, int q) {

        int p_Parent = find(p);//找到p的根节点
        int q_Parent = find(q);//找到q的根节点

        if (p_Parent != q_Parent) {//如果不在一个集合内部
            //判断哪一棵树的深度大
            if (rank[p_Parent] < rank[q_Parent]) {
                parent[p_Parent] = q_Parent;//p的深度小于q，则将p附加在q上
            } else if (rank[p_Parent] > rank[q_Parent]) {
                parent[q_Parent] = p_Parent;//p的深度大于q，则将q附加在p上
            } else {
                parent[q_Parent] = p_Parent;//两者深度一样，附加方式随意
                rank[p_Parent]++;//这里，我们选择将q附加在p上，所以更新p的深度，加一。
            }
        }
    }
```

​	注意此处因为增加的是rank，即树的深度，而当一棵树的深度小于另一棵的时候，我们将深度小的加到深度大的上，附加后整棵树的深度是不变的，还是原来深度较大的那棵树的深度！只有两棵树深度一样的时候，在附加是才需要将深度加一。

**图示： ** *两棵树的深度都是3，但在合并之后就是4了*

![rank](D:\Note\pictures\Union Find\rank.jpg)

### 并查集优化之路径压缩###

​	这是我们要介绍的最后一种优化思路，名字十分高大上，而且该优化理论上是最优的，但实现异常简单，下面我们先说一下该算法的思路。

#### 思路####

​	前两种优化我们都是在union操作中进行的，那我们可否在find里面进行优化呢？当然可以，这就是路径压缩的优化点。我们在对元素做find操作的时候，如果该元素的父节点不是自己，那么，我们将该元素的父节点更改为父节点的父节点，形象点说就是，将该节点的爷爷变成了自己的父亲，这样在下一次find的时候就跳过了该节点的父节点。

**图示：**

![路径压缩](D:\Note\pictures\Union Find\路径压缩.png)

​	就以图中所示，我们在对4做find操作的时候，发现它的父节点不是自己，那么我们将4的父节点改为4父节点的父节点即2，然后将4放到2的子节点处，接着我们就是find节点2的父节点，然后发现2的父节点也不是自己，则将2父节点的父节点改为0，然后将2放在0这个节点下面，最后我们find节点0，发现已经是自己了，结束find操作。

####find优化的实现####

​	以上步骤看似很复杂，但是实现上只需要添加一行代码。

**代码：**

```java
    public int find(int p) {
        while (parent[p] != p) {
            parent[p] = parent[parent[p]];//添加的一行代码，该行代码就表示将p节点父亲的父亲变为p的父亲。
            p = parent[p];
        }
        return p;
    }
```



## 总结##

​	进过我们这一系列的优化，并查集操作的时间复杂度近乎为O(1)，但不等于O(1)。

​	*在同时使用路径压缩、按秩合（rank）并优化的程序每个操作的平均时间仅为![O(\alpha (n))](https://wikimedia.org/api/rest_v1/media/math/render/svg/0f2f9c5bf5571b12dd0907f0a1ef917e1c082201)，其中![\alpha (n)](https://wikimedia.org/api/rest_v1/media/math/render/svg/74c1642d9e2f86b9e5c86c0f18ee5377507da827) 是 ![{\displaystyle n=f(x)=A(x,x)}](https://wikimedia.org/api/rest_v1/media/math/render/svg/5bcc6ab1771bb643699c39ff45998c876e788261) 的反函数，![A](https://wikimedia.org/api/rest_v1/media/math/render/svg/7daff47fa58cdfd29dc333def748ff5fa4c923e3) 是急速增加的[阿克曼函数](https://zh.wikipedia.org/wiki/%E9%98%BF%E5%85%8B%E6%9B%BC%E5%87%BD%E6%95%B0)。因为 ![\alpha (n)](https://wikimedia.org/api/rest_v1/media/math/render/svg/74c1642d9e2f86b9e5c86c0f18ee5377507da827) 是其反函数，故 ![\alpha (n)](https://wikimedia.org/api/rest_v1/media/math/render/svg/74c1642d9e2f86b9e5c86c0f18ee5377507da827) 在 ![n](https://wikimedia.org/api/rest_v1/media/math/render/svg/a601995d55609f2d9f5e233e36fbe9ea26011b3b) 十分巨大时还是小于 5。因此，平均运行时间是一个极小的常数。*

​	以上一段是百度的，其实就是想说并查集是一种极高效的数据结构，它可以解决许多连接问题，因此也是一种常用的数据结构。