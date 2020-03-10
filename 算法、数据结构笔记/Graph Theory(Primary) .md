# 图论基础(初级)

[TOC]

## 引言	

​	图论(Graph theory)是数学研究领域的一大分支，它以图为研究对象，而图是一种由顶点和边构成的离散数据结构，它的顶点代表事物，边表示各个事物之间的某种特殊关系。图的应用领域十分广泛，几乎所有学科中的问题都可以使用图来建模求解。

##图的定义及分类

图是由顶点和边组成的：(可以无边，但至少包含一个顶点)

- 一组顶点：通常用 V(vertex) 表示顶点集合
- 一组边：通常用 E(edge) 表示边的集合

图可以分为有向图和无向图，在图中：

- (v, w) 表示无向边，即 v 和 w 是互通的
- <v, w> 表示有向边，该边始于 v，终于 w

图可以分为有权图和无权图：

- 有权图：每条边具有一定的权重(weight)，通常是一个数字
- 无权图：每条边均没有权重，也可以理解为权为 1

图又可以分为连通图和非连通图：

- 连通图：所有的点都有路径相连
- 非连通图：存在某两个点没有路径相连

图中的顶点有度的概念：

- 度(Degree)：所有与它连接点的个数之和
- 入度(Indegree)：存在于有向图中，所有接入该点的边数之和
- 出度(Outdegree)：存在于有向图中，所有接出该点的边数之和

## 图的实现方式（无权图）

​	图的实现方式主要分为两种，一种为邻接矩阵，另一种为邻接表。

### 邻接矩阵

​	在使用邻接矩阵来表示图的时候，对于一个拥有v个节点的图，我们创建一个`v*v`的二维布尔数组，如果从i到j有边，则将数组的第i行第j列值赋为true，如果是无向图的话，同时还要将第j行i列赋为true。

**图示：**

![邻接矩阵](D:\Note\pictures\Graph\邻接矩阵.png)

**代码定义:**

*为了后续的一些操作的实现，此处使用了接口。*

*UnweightedGraph接口：*

```java
public interface Graph {
    int getVertexNum();

    int getEdgeNum();

    void addEdge(int v, int w);

    boolean hasEdge(int v, int w);

    Iterable<Integer> adj(int v);

    void show();
}

```

```java
public class Dense_Graph implements Graph {
    private int vertexNum;//顶点数
    private int edgeNum;//边数
    private boolean directed;//表示是否为有向图
    private boolean[][] g;//邻接矩阵
  
    public Dense_Graph(int vertexNum, boolean directed) {//初始化
        this.vertexNum = vertexNum;
        this.directed = directed;
        this.edgeNum = 0;
        g = new boolean[vertexNum][vertexNum];//初始化二维数组
    }

    public int getVertexNum() {//返回顶点数
        return vertexNum;
    }

    public int getEdgeNum() {//返回边数
        return edgeNum;
    }

    public void addEdge(int v, int w) {//添加一条边
        assert v >= 0 && v < vertexNum && w >= 0 && w < vertexNum;
        //如果v和w已经有边则不进行添加,用于处理平行边
        if (!hasEdge(v, w)) {
            g[v][w] = true;//在邻接矩阵中，添加边就是将两个节点对应的数组值设为true
            if (!directed) {
                //如果是无向图
                g[w][v] = true;//无向图意味着二者互通
            }
            edgeNum++;//边数加一
        }
    }

    public boolean hasEdge(int v, int w) {
        return g[v][w];//判断v和w之间是否有边
    }

    public Iterable<Integer> adj(int v) {//迭代器，用于迭代某一个节点相邻的所有节点
        assert v >= 0 && v < vertexNum;
        Vector<Integer> adjV = new Vector<>();//创建一个vector对象，用于保存结果
        for (int i = 0; i < vertexNum; i++) {
            if (g[v][i]) {//如果v与i相通
                adjV.add(i);//将i添加
            }
        }
        return adjV;//返回迭代器
    }

    public void show() {//用于打印整个图
        for (int i = 0; i < vertexNum; i++) {
            for (int j = 0; j < vertexNum; j++) {
                System.out.print(g[i][j] + "\t");
            }
            System.out.println();
        }
    }
}
```

### 邻接表

​	在使用邻接表来表示一张图的时候，我们通常用链表数组来存储图的信息，即如果我们要创建一个v个节点的图，则就有v个链表，这v个链表组成数组，数组的索引表示哪个节点，链表里面的值代表与该节点相连的其它节点。

**图示：**

![邻接表](D:\Note\pictures\Graph\邻接表.png)

**代码定义：**

```java
public class Sparse_Graph implements Graph {
    private int vertexNum;//顶点数
    private int edgeNum;//边数
    private boolean directed;//是否是有向图
    private Vector<Integer>[] g;//使用邻接表存储图

    public Sparse_Graph(int vertexNum, boolean directed) {//初始化邻接表
        this.vertexNum = vertexNum;
        this.directed = directed;
        this.edgeNum = 0;
        g = new Vector[vertexNum];//初始化g数组
        for (int i = 0; i < vertexNum; i++) {
            g[i] = new Vector<>();//为每一个vector初始化
        }
    }

    public int getVertexNum() {
        return vertexNum;
    }

    public int getEdgeNum() {
        return edgeNum;
    }

    public void addEdge(int v, int w) {//添加边
        assert v >= 0 && v < vertexNum;
        assert w >= 0 && w < vertexNum;

        if (!hasEdge(v, w)) {//如果边存在则不添加
            g[v].add(w);
            if (!directed && v != w) {//如果是无向图
                g[w].add(v);
            }
            edgeNum++;
        }

    }
  
    public boolean hasEdge(int v, int w) {
        for (int i = 0; i < g[v].size(); i++) {
            if (g[v].elementAt(i) == w) {
                return true;
            }
        }
        return false;
    }

    public Iterable<Integer> adj(int v) {//返回v相邻节点的迭代器
        assert v >= 0 && v < vertexNum;
        return g[v];//直接返回v这个链表即可。
    }

    public void show() {//打印图
        for (int i = 0; i < vertexNum; i++) {
            System.out.print(i + ": ");
            for (int j = 0; j < g[i].size(); j++) {
                System.out.print(g[i].elementAt(j) + "\t");
            }
            System.out.println();
        }
    }
}

```

### 小结

​	以上就是图的两种表现方式的实现，从它们的实现中也可以看出，邻接矩阵适合用于表现稠密图，而邻接表适合于稀疏图，所以在面对具体的实现的时候我们要视情况选取图的实现方式。

## 图的基本操作算法

​	图是一个相当大的领域，而与之有关的算法完全可以写一本不薄的书，所以我们这边也不可能将之说一遍，这里我们先只讲最基本的两个算法，一个是DFS，另一个是BFS，也就是深度优先搜索和广度优先搜索，其它的算法大都是这两个的变种，所谓万变不离其宗，我们先把这两个吃透了，遇到其它的也就不怕了。

### DFS算法及其应用

####定义

​	深度优先搜索算法（Depth-First-Search，简写DFS），主要是用于对树或图的遍历。它的核心思想就是从一个未访问过的节点出发，不断搜索未访问过的节点，如果没有未访问过的节点了，则返回上一层，接着重复该操作。通俗点就是从一点出发，一直沿着一条路走，如果到底了则返回找其它的路走。

**图示：**

![DFS](D:\Note\pictures\Graph\DFS.png)

#### 求连通分量

​	基于dfs，我们可以很容易的求得一张图的连通分量，所谓连通分量就是指一张图中有几个部分是连在一起的。

**图示：**

![连通分量](D:\Note\pictures\Graph\连通分量.PNG)

​							*在上图中，该图分为三个部分，所以连通分量为3*

**思路：**

- 对于一张图，首先我们循环遍历每一个节点元素，如果遇到未访问过的，则将其标上记号。
- 接着使用dfs算法对与该节点连接的所有节点进行遍历操作，一边遍历，一边标上记号，以表示访问过了。
- dfs结束就表示对一个连通分量遍历完了，之后将记录数加一。
- 重复以上操作，直到图中所有元素均被遍历过为止。

**代码：**

``` java
public class Component {
    private int count;//连通分量的个数
    private boolean[] visted;//用于保存该元素是否被访问过了
    private Graph graph;//传入的图
    private int[] id;//表示该节点所属的连通分量是哪一个

    public Component(Graph graph) {
        this.graph = graph;
        count = 0;
        id = new int[graph.getVertexNum()];
        Arrays.fill(id, -1);//填充id为-1
        visted = new boolean[graph.getVertexNum()];

        //搜索连通分量
        for (int i = 0; i < graph.getVertexNum(); i++) {//遍历一遍图
            if (!visted[i]) {//如果该节点没有访问过
                dfs(i);//使用dfs对与该节点相连的节点全部访问一遍
                count++;//访问完count加一，同时进入下一个循环
            }
        }
    }

    public int getCount() {//获取连通分量的个数
        return count;
    }

    private void dfs(int v) {
        visted[v] = true;//设置该节点已经访问过了
        id[v] = count;//在同一个连通分量里面的节点，id是一致的。
        for (int g : graph.adj(v)) {//使用我们之前实现的迭代器，对与该节点相邻的节点遍历
            if (!visted[g]) {//如果该节点未访问过就进行访问，否则继续循环
                dfs(g);
            }
        }
    }

    public boolean isConnected(int v, int w) {//判断两个节点是否连通，只需要判断二者的连通分量id是否一致即可
        assert v >= 0 && v < graph.getVertexNum();
        assert w >= 0 && w < graph.getVertexNum();
        return id[v] == id[w];
    }
}

```

#### 路径搜索(无权图)

​	基于dfs算法，我们也能对两个节点间的路径进行搜索，我们只需要在对接点进行遍历访问的时候，将访问到的节点保存起来即可。

**代码：**

```java
public class Path {
    private Graph graph;   0
    private boolean visted[];//某节点是否访问过了
    private int[] from;//保存的路径
    private boolean find;//保存从起点到终点的路径是否已经找到了

    public Path(Graph graph) {//初始化，传进来一张图
        assert graph != null;
        this.graph = graph;
       }

    //深度优先搜索
    private void dfs(int v, int w) {//查找从起点v到终点w之间的路径
        assert v >= 0 && v < graph.getVertexNum();
        visted[v] = true;//表示该节点已经访问过了。
        if (v == w) {//如果当前访问的就是终点，则返回
            find = true;//已经找到了
            return;
        }

        for (int i : graph.adj(v)) {//对v周围节点进行访问
            if (!visted[i]) {//如果未访问过则进行访问
                if (find)//如果已经找到了路径则无需再遍历
                    return;
                from[i] = v;//保存是从哪个节点访问这个被访问节点的
                dfs(i, w);
            }
        }
    }

    public boolean hasPath(int v, int w) {//判断v和w之间是否有路径
        assert w >= 0 && w < graph.getVertexNum();
        find = false;//将该变量初始化为未找到
        visted = new boolean[graph.getVertexNum()];//每次遍历前都要初始化
        from = new int[graph.getVertexNum()];
        Arrays.fill(from, -1);//填充
        dfs(v, w);
        return visted[w];//如果访问过了则表示有路径，否则就没有
    }

    public Vector<Integer> getpath(int v, int w) {//获取从v到w的路径
        Vector<Integer> path = new Vector<>();//最终返回的路径
        if (hasPath(v, w)) {//先判断有没有路径
            Stack<Integer> stack = new Stack<>();//设置一个栈
            int p = w;
            while (p != v && p != -1) {
                stack.push(p);//将路径从后向前放入栈中
                p = from[p];
            }
            stack.push(p);

            while (!stack.empty()) {
                path.add(stack.pop());//将路径顺序再倒过来
            }
        }
        return path;//返回路径
    }

    public void showpath(int v, int w) {
        Vector<Integer> vector = getpath(v, w);//获取路径
        if (vector != null) {
            for (int i = 0; i < vector.size(); i++) {//打印
                System.out.print(vector.elementAt(i));
                if (i != vector.size() - 1) {
                    System.out.print("->");
                }
            }
            System.out.println();
        } else {
            System.out.println(v + "到" + w + "之间没有路径");
        }
    }
}

```

### BFS算法及其应用

####定义

​	广度优先搜索(Breadth-First-Search，缩写BFS)，它也是图论中的一种遍历算法，但它的遍历方式与DFS有所不同，DFS是从一个节点开始走到底，再回来接着从另一个节点遍历，而BFS则是从一个节点出发开始不断遍历离它最近的节点，直到它周围的节点都遍历完了，再遍历更外层的节点。

**图示：**

![BFS Example (Custom)](D:\Note\pictures\Graph\BFS Example (Custom).png)

#### 最短路径(无权图)

​	之前的DFS可以搜索两个节点之间的路径，但是这个路径并不一定是最短的路径，而基于BFS的思想，我们可以十分方便的找到两个节点之间的最短路径。

**代码：**

```java
public class ShortestPath {

    private Graph graph;//图
    private boolean[] visted;//是否被访问
    private int[] from;//保存一个节点到起点的路径中，它的父节点是哪个
    private int[] ord;//计数，保存该节点到起点最短路径的长度
  
    public ShortestPath(Graph graph) {//初始化
        assert graph != null;
        this.graph = graph;
    }

    private void bfs(int v, int w) {//使用广度优先，遍历与起点v相连的节点
        PriorityQueue<Integer> queue = new PriorityQueue<>();
        queue.add(v);//将起点添加到队列中
        visted[v] = true;//设为已经访问过
        while (!queue.isEmpty()) {//直到队列为空
            int s = queue.poll();//将队列顶的元素抛出
            Vector<Integer> vector = (Vector<Integer>) graph.adj(s);//将该节点元素周围的所有元素进行访问
            for (int i : vector) {
                if (!visted[i]) {//如果该节点元素没有访问
                    visted[i] = true;//设置为已访问
                    from[i] = s;//保存是从哪个节点访问这个被访问节点的
                    ord[i] = ord[s] + 1;//计数加一
                    queue.add(i);//将该节点加入到队列中，在下一次while循环中将开始访问该节点周围所有的节点
                }
                if (i == w) {//如果i这个节点已经是终点，则不需要再访问其他节点了。
                    return;
                }
            }
        }
    }

    public int MinLength(int v, int w) {//返回两个节点间最短路径的长度
        if (hasPath(v, w)) {
            return ord[w];
        } else {
            return 0;
        }
    }

    public boolean hasPath(int v, int w) {//v与w之间是否有路径
        assert w >= 0 && w < graph.getVertexNum();
        //初始化
        visted = new boolean[graph.getVertexNum()];
        from = new int[graph.getVertexNum()];
        ord = new int[graph.getVertexNum()];
        Arrays.fill(from, -1);
        Arrays.fill(ord, 0);
        bfs(v, w);
        return visted[w];//如果访问过了则表示有路径，否则就没有
    }

    public Vector<Integer> getPath(int v, int w) {
        //获取从v到w的路径
        Vector<Integer> path = new Vector<>();//最终返回的路径
        if (hasPath(v, w)) {//先判断有没有路径

            Stack<Integer> stack = new Stack<>();//设置一个栈
            int p = w;
            while (p != v && p != -1) {
                stack.push(p);//将路径从后向前放入栈中
                p = from[p];
            }
            stack.push(p);

            while (!stack.empty()) {
                path.add(stack.pop());//将路径顺序再倒过来
            }
        }
        return path;//返回路径
    }

    public void showPath(int v, int w) {

        Vector<Integer> vector = getPath(v, w);//获取路径
        if (vector != null) {
            for (int i = 0; i < vector.size(); i++) {//打印
                System.out.print(vector.elementAt(i));
                if (i != vector.size() - 1) {
                    System.out.print("->");
                }
            }
            System.out.println();

        } else {
            System.out.println(v + "到" + w + "之间没有路径");
        }
    }
}

```

### 小结

​	不管是DFS还是BFS，它们虽然实现的方式不一样，DFS主要是使用递归或栈来实现，而BFS则使用队列来实现，但它们目标都是将节点一个个遍历，直到所有的节点都访问过为止，而在此基础上又衍生出了许多不同的算法，例如基于BFS的`Flood Fill，A*，B*，Dijklas,Prime`，而基于DFS的`拓扑排序 ,记忆化搜索，IDA*`等，尽管有些算法的实现上较为复杂，但主体思想还是不变的。

## 总结

​	以上就是对图基础的简单介绍，后面会有对图论的进阶，但事实上要想靠一两篇文章就将图这个分支介绍完那是不可能的事情，事实上图论是一个极其庞大的分支，所以我们只能尽可能先把基础打好，一点点再向更难的分支前行。

















