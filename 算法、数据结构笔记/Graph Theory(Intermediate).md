# 图论基础(中级)

[TOC]

## 引言

​	之前我们在图论基础的初级中学过了图的两种实现方式，介绍了关于图的两种遍历方式，一个是深度优先搜索，另一个是广度优先搜索，同时对这两种的遍历方式的应用进行了简单的介绍。

​	在这一章中，我们将介绍两类与图论相关的问题，一个是最小生成树问题，另一个是单源最短路径问题，这两类都是图论中经典的问题，我们在此会对他们进行详细的介绍。

## 带权图的定义及实现

###定义

​	在上一章中，图都是以无权图的形式进行实现的，然后这里实现的最小生成树与最短路径的算法都是以带权图为基础，所以我们先简单介绍一下带权图。

​	所谓带权图，其实很简单，就是图中的每一条边上有自己的权重，简单点说就是边上有数值，这个数值就是通过这条边的费用，而且这个数值有正有负。

**图示：**![有向带权图](D:\Note\pictures\Graph\带权图.png)

​										    *（带权图）*

### 实现

​	有权图的实现很简单，只要在我们之前实现的无权图上加上一个属性值，以表示边的权重即可，这边我们实现了一个Edge类，用于表示一条边的两个端点与这条边的权重值。

**代码实现：**

*Edge类：*

```java
public class Edge<Weight extends Number & Comparable> implements Comparable<Edge> {
    private int v, w;//两个顶点,在有向图中，V表示起点，W表示终点
    private Weight weight;//边的权值

    public Edge(int v, int w, Weight weight) {//用数值初始化
        this.v = v;
        this.w = w;
        this.weight = weight;
    }

    public Edge(Edge<Weight> edge) {//直接用另一条边初始化
        this.v = edge.v;
        this.w = edge.w;
        this.weight = edge.weight;
    }
   public Edge() {//无参构造器
    }
    public int getV() {//获取起点
        return v;
    }

    public int getW() {//获取终点
        return w;
    }

   public int other(int x) {//给定一个顶点，返回另一个顶点
        assert x == v || x == w;
        return x == this.v ? this.w : this.v;
    }

    @Override
    public String toString() {
        return "Edge{" +
                "v=" + v +
                " ->  w=" + w +
                ", weight=" + weight +
                '}';
    }

    @Override
    public int compareTo(Edge o) {//比较两条边
        if (this.weight.compareTo(o.weight) > 0) {//如果该边的权值大于另一条边
            return 1;
        } else if (this.weight.compareTo(o.weight) < 0) {//小于
            return -1;
        } else {//等于
            return 0;
        }
    }

    public Weight getWeight() {//获取该边的权值
        return weight;
    }
}
```

*WeightGraph接口：*

```java
public interface WeightGraph<Weight extends Number & Comparable> {
    int getVertexNum();//获取顶点数

    int getEdgeNum();//获取边数

    void addEdge(Edge<Weight> edge);//添加一条边

    boolean hasEdge(int v, int w);//两个顶点间是否有边

    Iterable<Edge<Weight>> adj(int v);//定点的邻边迭代器

    void show();//打印图
}

```

*带权图的邻接矩阵实现：*

```java
public class DenseWeight_Graph<Wegight extends Number & Comparable> implements WeightGraph {
    private int vertexNum;//顶点数
    private int edgeNum;//边数
    private boolean directed;//表示是否为有向图
    private Edge<Wegight>[][] g;//邻接矩阵

    public DenseWeight_Graph(int vertexNum, boolean directed) {//初始化
        this.vertexNum = vertexNum;
        this.directed = directed;
        this.edgeNum = 0;
        g = new Edge[vertexNum][vertexNum];
    }

    @Override
    public int getVertexNum() {//返回顶点数
        return vertexNum;
    }

    @Override
    public int getEdgeNum() {//返回边数
        return edgeNum;
    }

    @Override
    public void addEdge(Edge edge) {//添加一条边
        assert edge != null;
        assert edge.getV() >= 0 && edge.getV() < vertexNum;
        assert edge.getW() >= 0 && edge.getW() < vertexNum;

        if (!hasEdge(edge.getV(), edge.getW())) {
            g[edge.getV()][edge.getW()] = new Edge(edge);
            if (!directed && edge.getV() != edge.getW()) {
                g[edge.getW()][edge.getV()] = new Edge(edge.getW(), edge.getV(), edge.getWeight());
            }
            edgeNum++;
        }
    }

    @Override
    public boolean hasEdge(int v, int w) {
        assert v >= 0 && v < vertexNum && w >= 0 && w < vertexNum;
        return g[v][w] != null;//判断v和w之间是否有边
    }

    @Override
    public Iterable<Edge<Wegight>> adj(int v) {//返回v相邻节点边的迭代器
        assert v >= 0 && v < vertexNum;

        Vector<Edge<Wegight>> vector = new Vector<>();
        for (int i = 0; i < vertexNum; i++) {
            if (g[v][i] != null) {
                vector.add(g[v][i]);
            }
        }
        return vector;

    }

    @Override
    public void show() {//打印
        for (int i = 0; i < vertexNum; i++) {
            for (int j = 0; j < vertexNum; j++) {
                if (g[i][j] != null) {
                    System.out.printf("%#.2f\t", g[i][j].getWeight());
                } else {
                    System.out.print("NULL\t");
                }

            }
            System.out.println();
        }
    }
}

```

*带权图的邻接表实现：*

```java
public class SparseWeight_Graph<Weight extends Number & Comparable> implements WeightGraph {
    private int vertexNum;//顶点数
    private int edgeNum;//边数
    private boolean directed;//是否是有向图
    private Vector<Edge<Weight>>[] g;//使用邻接表存储图

    public SparseWeight_Graph(int vertexNum, boolean directed) {//初始化邻接表
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

    @Override
    public void addEdge(Edge edge) {
        assert edge.getW() >= 0 && edge.getW() < vertexNum;
        assert edge.getV() >= 0 && edge.getV() < vertexNum;

        g[edge.getV()].add(new Edge(edge));
        if (!directed && edge.getW() != edge.getV()) {
            g[edge.getW()].add(new Edge(edge.getW(), edge.getV(), edge.getWeight()));
        }
        edgeNum++;

    }

    @Override
    public boolean hasEdge(int v, int w) {
        for (int i = 0; i < g[v].size(); i++) {
            if (g[v].elementAt(i).other(v) == w) {
                return true;
            }
        }
        return false;
    }

    public Iterable<Edge<Weight>> adj(int v) {//返回v相邻节点边的迭代器
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

​	以上就是带权图的邻接表和邻接矩阵的实现，实现思路基本和之前的无权图区别不大。

## 最小生成树问题

### 定义

- **连通图：**在无向图中，如果任意两个顶点之间都有路径相连通，则称该无向图为连通图。

- **强连通图：**在有向图中，如果任意两个顶点之间都有路径相连通，则称该有向图为强连通图。

- **连通网**：在连通图中，若图的边具有一定的意义，每一条边都对应着一个数，称为权；权代表着连接两个顶点的代价，称这种连通图叫做连通网。

- **生成树**：一个连通图的生成树是指一个连通子图，它含有图中全部n个顶点，但只有足以构成一棵树的n-1条边。一棵有n个顶点的生成树有且仅有n-1条边，如果生成树中再添加一条边，则必定成环。

- **最小生成树**：在连通网的所有生成树中，所有边的代价和最小的生成树，称为最小生成树。

  ![最小生成树](D:\Note\pictures\Graph\最小生成树.png)

  ​								    *(一棵最小生成树）*

###最小生成树算法

  	解决最小生成树问题的算法有许多种，但它们大都是基于贪心这一算法设计思想，所谓贪心算法简单来说就是每一步都选取当前状态下最优的解决方案，注意还有一种与之类似的是动态规划，它们二者虽然相似，但还是有所不同的，这里就不进行介绍了，之后对算法的整理中会进行具体介绍。

  	而基于贪心这一算法设计思想，有两种最小生成树算法是最为著名的，一个是Prim算法，另一个是Kruskal算法。Prim算法实现比Kruskal算法较复杂一些，但时间复杂度上优于Kruskal。

#### Prim算法

##### 切分定理

  	在讲述prim算法之前，我们要先了解几个名词和一个定理。

  - **切分：** *将一张图的节点分成两部分，称为一个切分。*![切分](D:\Note\pictures\Graph\切分.png)

    

- **横切边：** *如果一条边，它的两个端点分别处于切分的两部分，则称该边为横切边。*![横切边](D:\Note\pictures\Graph\横切边.png)

  

- **切分定理：** *在任意的一个切分中，权值最小的横切边必定是最小生成树中的一条边。*![切分定理](D:\Note\pictures\Graph\切分定理.png)

  

##### Prim算法实现思路

- 首先我们对图进行切分操作，将之分为两部分，一部分只包含起点，另一部分包含除起点外的其它所有节点。
- 从起点出发，遍历起点周围所有的横切边，将这些边放入到最小堆中。
- 从最小堆中拿出权值最小的那个横切边，根据切分定理，该横切边必定属于最小生成树。
- 将该横切边的另一个节点加入到与起点同一个部分中。
- 将新加入的这个节点周围所有的横切边放入到最小堆中。
- 从最小堆中拿出下一个权值最小的横切边，判断该横切边的另一个节点是否访问过，如果访问过则丢弃并拿出下一个权值最小的横切边；否则将该横切边视为最小生成树中的边，并将该节点并入已访问过的节点中。
- 以此类推，直到图中所有节点都被遍历访问过为止。

**图示：**![Prim](D:\Note\pictures\Graph\Prim.jpg)

##### Prim的简单实现

​	我们在此先实现一个简易版的Prim算法，后续在对其时间复杂度上的分析，以及优化。

**代码：**

​	注：此处prim需要用到一个最小堆的数据结构，这里我用的是自己实现的最小堆，实现原理与前几章介绍的最大堆一致，这里不再赘述，当然如果不想自己写，也可以使用各个语言自带的优先队列。

*最小堆：*

```java
public class MinHeap<T extends Comparable> {
    private T[] data;
    private int count;

    public MinHeap(int capacity) {
        data = (T[]) new Comparable[capacity + 1];//从1开始
        this.count = 0;
    }

    public void insert(T element) {//插入节点
        assert count + 1 < data.length;
        data[++count] = element;
        shift_up(count);//维护最小堆
    }

    private void shift_up(int index) {

        while (index > 1 && data[index].compareTo(data[index / 2]) < 0) {//判断该节点是否小于其父节点
            T temp = data[index];//如果小于则交换二者
            data[index] = data[index / 2];
            data[index / 2] = temp;
            index /= 2;
        }
    }

    public T extractMin() {//抛出最小值
        T min = data[1];
        data[1] = data[count--];
        shift_down(1);//维护最小堆
        return min;//返回值
    }

    private void shift_down(int i) {

        while (i * 2 <= count) {
            int min = i * 2;
            if (data[min].compareTo(data[min + 1]) > 0 && min + 1 <= count) {//比较两个节点，哪个最小
                min++;
            }
            if (data[min].compareTo(data[i]) < 0) {//将最小的那个子节点与该节点比较
                T temp = data[i];//如果该节点大于子节点中的一个，则交换
                data[i] = data[min];
                data[min] = temp;
                i = min;
            } else break;//否则退出
        }
    }

    public boolean isEmpty() {//是否为空
        return count == 0;
    }
}

```

**LazyPrime:**

``` java
public class LazyPrim<Weight extends Number & Comparable> {
    private MinHeap<Edge<Weight>> minHeap;//最小堆，辅助数据结构
    private WeightGraph<Weight> graph;//图
    private boolean[] marked;//标记该节点是否访问过了
    private Vector<Edge<Weight>> mst;//最小生成树的边
    private Number weight;//整棵树的最小权值

    public LazyPrim(WeightGraph<Weight> graph) {
        this.graph = graph;//初始化
        minHeap = new MinHeap<>(graph.getEdgeNum());
        marked = new boolean[graph.getVertexNum()];
        mst = new Vector<>();
        weight = 0.0;

        //LazyPrim
        visited(0);//从起点开始遍历
        while (!minHeap.isEmpty()) {//如果最小堆未空，则对堆中每一条边进行遍历
            Edge<Weight> edge = minHeap.extractMin();//从堆中取出一条最小边
            if (!marked[edge.getW()] || !marked[edge.getV()]) {//判断两个点是否被访问过了，都访问过则将该边丢弃
                mst.add(edge);//是最小横切边，将其添加到最小生成树中
                //访问该边未访问过的节点
                if (!marked[edge.getW()]) {
                    visited(edge.getW());
                } else {
                    visited(edge.getV());
                }
            }
        }

        for (Edge edge : mst) {//将整棵树的权值相加
            weight = (double) weight + (double) edge.getWeight();
        }
    }

    private void visited(int v) {//辅助方法
        assert !marked[v];
        marked[v] = true;//标记已访问该节点
        for (Edge<Weight> edge : graph.adj(v)) {//对该点周围每一条边进行遍历
            if (!marked[edge.other(v)]) {//如果另一个节点还没有访问过
                minHeap.insert(edge);//该边为横切边，但未必是最小横切边，先将其添加到最小堆中
            }
        }
    }

    public Vector<Edge<Weight>> getMst() {//获取最小生成树
        return mst;
    }

    public Number getWeight() {//获取最小生成树的权值
        return weight;
    }
}
```

​	以上就是Prim简易版的实现，从代码中我们可以看出，我们该版本的Prim算法时间复杂度在O(ElogE)这个级别，其中E指代边数，但我们Prim算法一般是应用于稠密图当中，而稠密图的边数是节点数的平方，所以此时我们的算法在效率上还不尽人意，所以我们需要对其进行优化，使其达到O(ElogV)级别。

##### Prim算法优化

​	既然要优化，那么我们就来想一下哪些地方可以优化。首先我们可以看到，在算法运行中，我们在最小堆中的边随着已访问节点的增加，有的已经不是横切边了，但我们还是把它留在了堆中，并会对其进行遍历；其次，虽然有的边是横切边，但它不可能是最小横切边，但尽管如此，我们还是将其放在了最小堆中。

​	既然现在我们知道了需要优化的地方，那么我们就需要了解如何进行优化。至于如何进行优化，我们可以看到，大多数的时间我们都是对那些不是最小的横切边进行遍历，那我们能不能只将每一个节点最小的那个横切边放入堆中，并对每个节点只维护一条最小的横切边呢？很显然这是可以的，但我们不使用最小堆这个数据结构，因为在最小堆中，数据一旦放入就无法维护，因此我们需要一个比最小堆更好的数据结构最小索引堆，我们使用最小索引堆存储与每一个节点相连的最小横切边的权值，用另一个edge数组来存放到每一个节点的横切边，这样我们可以很方便的对每一个节点的横切边进行维护操作。

**代码：**

```java
public class Prim<Weight extends Number & Comparable> {
    private Index_MinHeap<Weight> index_minHeap;//辅助数据结构最小索引堆，用于保存到每个节点最小的权重
    private boolean[] marked;//是否访问过
    private WeightGraph<Weight> graph;//图
    private Number weight = 0.0;//总的权值
    private Vector<Edge<Weight>> mst;//最小生成树的边
    private Edge<Weight>[] edgeto;//保存到每个节点所经过的横切边

    public Prim(WeightGraph graph) {//初始化
        this.graph = graph;
        marked = new boolean[graph.getVertexNum()];
        mst = new Vector<>();
        edgeto = new Edge[graph.getVertexNum()];
        index_minHeap = new Index_MinHeap<>(graph.getVertexNum());
        //Prim算法
        visit(0);//访问起点
        while (!index_minHeap.isEmpty()) {//最小索引堆是否为空
            int index = index_minHeap.extractMinIndex();//从堆中拿出最小边的索引，该边一定是到对应节点的最小横切边
            assert edgeto[index] != null;
            mst.add(edgeto[index]);//将边加入到最小生成树中
            visit(index);//访问该节点
        }

        for (Edge<Weight> edge : mst) {//访问生成树中每一条边
            weight = (double) weight + edge.getWeight().doubleValue();//将每一条边的权值相加，获取整棵树的权值
        }
    }

    private void visit(int v) {//访问
        assert !marked[v];
        marked[v] = true;//标记该节点已经访问过

        for (Edge<Weight> edge : graph.adj(v)) {//遍历v节点的每一条邻边
            int w = edge.other(v);//获取与v节点同一边的另一节点w
            if (!marked[w]) {//如果w节点没有访问过，表示这是一个横切边

                if (edgeto[w] == null) {//如果还没有到w节点的边
                    //表示该节点还没有与之对应的横切边
                    edgeto[w] = edge;//将该边作为横切边保存
                    index_minHeap.insert(w, edge.getWeight());//保存到w节点的权重到最小索引堆
                } else if (edge.getWeight().compareTo(edgeto[w].getWeight()) < 0) {//当前的横切边的权重小于之前保存的横切边
                    edgeto[w] = edge;//更新数据
                    index_minHeap.change(w, edge.getWeight());//将新的横切边权重的值保存到相应节点
                }
            }
        }
    }

    public Vector<Edge<Weight>> getMst() {
        return mst;
    }

    public Number getWeight() {
        return weight;
    }
}

```

#### Kruskal算法

​	Kruskal算法的思想很简单，可以说是Prim算法的简化版，所以时间复杂度上可能不及优化过的Prim，和未优化过的Prim是一个级别的。它每次都是从图中选取最短的一条边作为最小生成树的一部分，当然这有一个前提，就是加入这条边之后，此生成树不能有环，因为树不能有环，有环就成了图，这也是树与图的本质区别。

##### Kruskal算法实现思路

- 将图中所有的节点遍历一边，同时将所有的边保存到一个最小堆中。
- 从最小堆中取出一条边，使用并查集判断在没有这条边的情况下，该边的两个端点是否相连。
- 如果在没有该边的情况下两个端点仍可以连接，则表明如果加入此边生成树会形成一个环，因此将该边丢弃。
- 否则将该条边加入到最小生成树中。
- 重复执行上述操作，直到生成树的边数等于节点数-1为止。

**图示：**

![Kruskal](D:\Note\pictures\Graph\Kruskal.jpg)

#####Kruskal算法实现

**代码：**

```java
public class Krusk<Weight extends Number & Comparable> {
    private Vector<Edge<Weight>> Mst;//最小生成树
    private Number weight = 0.0;//整棵树的权值

    public Krusk(WeightGraph<Weight> graph) {
        Mst = new Vector<>();//初始化最小生成树
        MinHeap<Edge<Weight>> minHeap = new MinHeap<>(graph.getEdgeNum());//用于存放图中的每一条边
        for (int i = 0; i < graph.getVertexNum(); i++) {//遍历图中每一个节点
            for (Edge<Weight> edge : graph.adj(i)) {//将每一个节点周围的邻边进行遍历
                if (edge.getV() < edge.getW()) {//确保边只加入一次
                    minHeap.insert(edge);//将每一条边插入到最小堆中
                }
            }
        }
        Quick_Union5 union = new Quick_Union5(graph.getVertexNum());//使用并查集来判断生成树是否有环
        while (!minHeap.isEmpty() && Mst.size() < graph.getVertexNum() - 1) {//不断循环直到堆中没有边或生成树中边的个数等于顶点数-1
            Edge<Weight> edge = minHeap.extractMin();//获取到图中最小边
            if (!union.isConnected(edge.getV(), edge.getW())) {//使用并查集判断该边的两个是否已经相连
                union.union(edge.getV(), edge.getW());//将两者连接
                Mst.add(edge);//将该边添加到最小生成树中
            }
        }

        //计算权值
        for (Edge<Weight> edge : Mst) {
            weight = (double) weight + edge.getWeight().doubleValue();
        }
    }

    public Vector<Edge<Weight>> getMst() {
        return Mst;
    }

    public Number getWeight() {
        return weight;
    }
}
```
### 小结

​	以上就是关于最小生成树最经典的两个算法，但事实上我们还有一种实现生成树的算法，叫Vyssotsky's Algorithm，它的实现思路有别于前面两种算法，它是将图中的边逐渐的添加到生成树中，当一旦生成了环，我们就将该环中权值最大的那个边删除，不断重复，直到图中的所有边都被遍历过为止，然而该算法目前并没有很好的数据结构支撑它快速对环中权值最大的边删除，所以我们在实现最小生成树主要使用前两种方法。

## 单源最短路径问题

### 定义

- **最短路径：**从某 顶点出发，沿图的边到达另一顶点所经过的路径中，各边上权值之和最小的一条路径。
- **单源最短路径问题：**给定一个带权有向图G=（V,E），其中每条边的权是一个实数。另外，还给定V中的一个顶点，称为源。现在要计算从源到其他所有各顶点的最短路径长度。这里的长度就是指路上各边权之和。
- **最短路径估计：**对每个节点V来说，我们维护一个属性用来记录从起点S到到节点V的最短路径所需权值的上界。
- **松弛操作：**我们将从起点S到节点U之间最短路径的费用加上U到节点V之间边的权重，与当前的S到V所需要的费用进行比较，如果前者更小，我们就对S到V之间的最短路径进行更新操作。

### 单源最短路径算法

​	解决单源最短路径问题主要就是靠上面定义中的松弛操作，而目前基于此的主要有以下几种算法：Dijkstra算法，Bellman-Ford算法，Floyd算法和SPFA算法，而本节只对其中的两种进行讲解，一个是Dijkstra，另一个是Bellman-Ford。

​	Dijkstra算法适用于没有负权值的图中，时间复杂度在O(ElogV)；Bellman-Ford适用范围更广，可以使用在正负权值均存在的图中，且同时可以自行检测图中有无负权环，因为一旦有负权环则意味着改图不存在权值和最小的路径。虽然Bellman-Ford适用范围广，但与之相对的它的时间复杂度就高，需要O(EV)的时间复杂度。

#### Dijkstra算法

​	Dijkstra算法的主要思想是基于贪心来实现的，它每一次都是选取一个节点周围边中权值最小的那个作为最短路径中的一条边，然后循着该边到另一个端点，接着重复之前的操作，直到所有的点节点都被遍历过为止。

#####Dijkstra算法实现思路

- 首先从起点S出发，遍历其邻边，将邻边的权值保存到最小索引堆中对应的节点上。
- 从堆中取出权值最小边指向的节点U，遍历该节点U周围的邻边。
- 在遍历邻边的时候，我们要判断该边另一个端点V是否访问过，如果访问过了则跳过，否则判断是否可以进行松弛操作，即判断从S到U再到V，是否比从S不经过U到V所需要的费用更少，如果是则更新数据。
- 不断重复以上操作，直到所有节点均被访问过为止。

**图示：**

![Dijkstra](D:\Note\pictures\Graph\Dijkstra(1).jpg)![Dijkstra](D:\Note\pictures\Graph\Dijkstra(2).jpg)![Dijkstra](D:\Note\pictures\Graph\Dijkstra(3).jpg)

##### Dijkstra算法实现

**代码：**

```java
public class Dijkstra<Weight extends Number & Comparable> {
    private WeightGraph<Weight> graph;//传入的图
    private boolean[] marked;//用于标记是否被访问过
    private int s;//起点
    private Number[] distTo;//distTo[i]记录从起点到i的最短路径的权值
    private Edge<Weight>[] from;//from[i]记录从哪一条边到达该点的

    public Dijkstra(WeightGraph<Weight> graph, int s) {//初始化
        this.graph = graph;
        assert s >= 0 && s < graph.getVertexNum();
        this.s = s;
        marked = new boolean[graph.getVertexNum()];
        distTo = new Number[graph.getVertexNum()];
        from = new Edge[graph.getVertexNum()];
        for (int i = 0; i < graph.getVertexNum(); i++) {
            marked[i] = false;
            distTo[i] = 0.0;
        }
		//Dijkstra算法
        Index_MinHeap<Weight> minHeap = new Index_MinHeap<>(graph.getVertexNum());//存放从起点到每个点的所需的最小费用
        //初始化起点
        distTo[s] = 0.0;
        marked[s] = true;
        from[s] = new Edge<>(s, s, (Weight) (Object) 0.0);
        minHeap.insert(s, (Weight) distTo[s]);//将起点加入到最小索引堆中
        while (!minHeap.isEmpty()) {//不断循环，直到堆中没有数据为止
            int v = minHeap.extractMinIndex();//获取到最小权值边对应的节点
            marked[v] = true;//标记该点已经访问过了
            //松弛操作
            for (Edge<Weight> edge : graph.adj(v)) {//遍历该节点周围的边
                int w = edge.other(v);//获取这个邻边端点是谁
                if (!marked[w]) {//判断该节点是否访问过
                    if (from[w] == null ||//确保还没有边可以到达该节点
                            (distTo[v].doubleValue() + edge.getWeight().doubleValue()) <
                        	distTo[w].doubleValue()) {//判断以该节点作为中间节点，是否可以使到达该节点的费用减少
                        //将当前的最小值赋给distTo
                        distTo[w] = distTo[v].doubleValue() + edge.getWeight().doubleValue();
                      	
                        from[w] = edge;//更新该点是从哪边遍历来的
                        if (minHeap.exist(w)) {//如果该点已经在堆中了
                            //改变值
                            minHeap.change(w, (Weight) distTo[w]);
                        } else {
                            //插入值
                            minHeap.insert(w, (Weight) distTo[w]);
                        }
                    }
                }
            }
        }
    }

    public Vector<Edge<Weight>> getPath(int w) {//获取从起点到节点W的路径，该处的实现与上一章中的使用BFS获取最短路径代码一致
        assert w >= 0 && w < graph.getVertexNum();
        assert hasPath(w);
        Vector<Edge<Weight>> path = new Vector<>();
        Stack<Edge<Weight>> temp = new Stack<>();
        Edge<Weight> edge = from[w];
        while (edge.getV() != s) {
            temp.push(edge);
            edge = from[edge.getV()];
        }
        temp.push(edge);//该处将起点加入进来

        while (!temp.empty()) {
            path.add(temp.pop());
        }

        return path;
    }

    public Number getDistTo(int w) {//获取从起点到节点W所需要的费用
        assert hasPath(w);
        return distTo[w];
    }

    public boolean hasPath(int w) {//判断是否有最短路径
        assert w >= 0 && w < graph.getVertexNum();
        return marked[w];
    }

    void showPath(int w) {//打印路径
        assert w >= 0 && w < graph.getVertexNum();
        assert hasPath(w);
        Vector<Edge<Weight>> path = getPath(w);
        for (int i = 0; i < path.size(); i++) {
            System.out.print(path.elementAt(i).getV() + " -> ");
            if (i == path.size() - 1)
                System.out.println(path.elementAt(i).getW());
        }
    }
}

```

#####Dijkstra算法优化（待填坑）

​	以上就是基于Dijkstra算法的实现，虽然时间复杂度上还可以，（当然还可以进一步优化，使用斐波那契堆进行优化可以将时间复杂度进一步降到O(E+VlogV)级别，此处就不进行优化了，以后有时间加上）但是它的一个缺点就是不能处理负权边，同时呢实现上略微复杂了点，所以接下来我们将介绍另一种普适性上更强的一种算法，即Bellman-Ford算法。

#### Bellman-Ford算法

​	Bellman-Ford算法是基于动态规划这一算法思想进行设计的，对于一张图如果存在负权环，它会直接告诉我们不存在解决方案，如果没有，则算法会给出最短路径及其权重和。

​	Bellman-Ford算法对图中每一条边都会进行V-1次处理，每一次处理都会对每一条边进行一次松弛操作。当v-1次循环结束后还会再对全图的每一条边进行松弛操作，如果在这次松弛操作之后还能使整个路径的权值更小，则表明该图无解，否则表示已经找到了图中的最小路径。

##### Bellman-Ford算法实现思路

- 对整张图进行V-1次的遍历操作；
- 每一次遍历，都对图中的每一条边进行松弛操作，判断如果起点S到节点U的花费加上该边的权值w小于从起点S到终点V的花费，则更新终点V的最小权值；
- 检测图中是否有负权边形成了环，遍历图中的所有边，计算S至V的距离，如果对于从S到V存在更小的距离，则说明存在环；

**图示：**![Bellman-Ford](D:\Note\pictures\Graph\Bellman-Ford.png)

#####Bellman-Ford算法实现

**代码：**

```java
public class Bellman_Ford<Weight extends Number & Comparable> {
    private WeightGraph<Weight> graph;//图的引用
    private int s;//起点
    private Number[] distTo;//distTo[i]记录从起点到节点V的最短路径的权值
    private Edge<Weight>[] from;//记录该顶点是从哪条边来的
    private boolean hasNegativeCycle;//是否有负权环


    public Bellman_Ford(WeightGraph<Weight> graph, int s) {//初始化
        this.graph = graph;
        this.s = s;
        distTo = new Number[graph.getVertexNum()];
        from = new Edge[graph.getVertexNum()];
        Arrays.fill(from, null);

        //Bellman-Ford算法
        distTo[s] = 0.0;//初始化起点
        from[s] = new Edge<>(s, s, (Weight) (Object) 0.0);

        for (int pass = 1; pass < graph.getVertexNum(); pass++) {//对图遍历V-1次
            //每遍历一次都要对图中的每一条边进行松弛操作
            for (int v = 0; v < graph.getVertexNum(); v++) {//访问图中的每一个节点
                for (Edge<Weight> edge : graph.adj(v)) {//对每一个节点的邻边进行访问
                    if (from[edge.getV()] != null)//判断节点V是否为空，V是每一条有向边的起点
                        if (from[edge.getW()] == null ||//如果节点W为空，表示该有向边还没有访问过
                                distTo[edge.getV()].doubleValue() + edge.getWeight().doubleValue() <
                                        distTo[edge.getW()].doubleValue()) {//判断经过节点V再到节点W是不是花费更少
                            from[edge.getW()] = edge;//是的话进行更新操作
                            distTo[edge.getW()] = distTo[v].doubleValue() +
                              edge.getWeight().doubleValue();

                        }
                }
            }
        }

        hasNegativeCycle = detectNegativeCycle();//在进行一次松弛操作

    }

    private boolean detectNegativeCycle() {
        for (int v = 0; v < graph.getVertexNum(); v++) {//访问图中的每一个节点
            for (Edge<Weight> edge : graph.adj(v)) {//对每一个节点的邻边进行访问
                if (from[edge.getW()] == null ||//判断节点W是否还没有访问过
                        distTo[v].doubleValue() + edge.getWeight().doubleValue() <
                                distTo[edge.getW()].doubleValue()) {//判断是否还有更少的到达节点W
                    return true;//如果有就表明该图存在负权边，即不存在最短路径
                }
            }
        }
        return false;//否则就表明已经找到了图中的最短路径
    }

    public boolean HasNegativeCycle() {
        return hasNegativeCycle;
    }

    public Vector<Edge<Weight>> getPath(int w) {//返回最短路径
        assert w >= 0 && w < graph.getVertexNum();
        Vector<Edge<Weight>> path = new Vector<>();
        if (!hasNegativeCycle) {
            Stack<Edge<Weight>> temp = new Stack<>();
            Edge<Weight> edge = from[w];
            while (edge.getV() != s) {
                temp.push(edge);
                edge = from[edge.getV()];
            }
            temp.push(edge);//该处将起点加入进来
            while (!temp.empty()) {
                path.add(temp.pop());
            }
        }
        return path;
    }

    public Number getDistTo(int w) {//获取到节点W的最小权值
        assert w >= 0 && w < graph.getVertexNum();
        assert !HasNegativeCycle();
        return distTo[w];
    }

    public boolean hasPath(int w) {//判断是否存在最短路径
        assert w >= 0 && w < graph.getVertexNum();
        return !hasNegativeCycle && from[w] != null;
    }

    public void showPath(int w) {//打印最短路径
        assert w >= 0 && w < graph.getVertexNum();
        assert !hasNegativeCycle;
        Vector<Edge<Weight>> path = getPath(w);
        for (int i = 0; i < path.size(); i++) {
            System.out.print(path.elementAt(i).getV() + " -> ");
            if (i == path.size() - 1)
                System.out.println(path.elementAt(i).getW());
        }
    }
}
```

*补充：*对于为什么需要对图进行V-1次循环，是因为我们在每一次遍历图的时候，如果我们在访问U时，找到了一个比S->V更短的路径S->U->V，我们就需要对其进行更新，但V在U之前已经被遍历过了，如果此时我们直接结束就无法将那些经过V再到达的节点进行更新，所以我们需要不断重复的循环更新最短路径直到所有的边都被更新过。而对于一张图，不能存在环就将所有节点连接起来，我们最多只能使用V-1条边，所以我们循环V-1次。

##### Bellman-Ford算法优化（待填坑）

#### 小结

​	以上就是解决单源最短路径问题的两种算法，虽然Bellman-Ford算法的普适性更强但与之相对的所需的时间复杂度就高，尽管可以使用队列对其进行优化，但总的效率上还是比不上Dijkstra算法，这是两个算法设计思想上的偏差导致的。因为Dijkstra基于贪心，它只寻找当前状态下的最优解，而Bellman-Ford因为基于动态规划，它寻找的是全局状态下的最优解，这之间必然会有所不同。但在现实生活中我们大多处理的都是不带负权边的图，所以Dijkstra算法的使用频率更高一些。

## 总结

​	以上就是图论中的两个比较经典的问题及它们的解决思路，但图论中的问题还有很多，我们这里只是求解了无向图中的最小生成树，但还有一个问题是最小树形图问题，它是针对有向图的，它的解决算法是朱刘算法（没错，是国人提出的）；另外我们这里只是求解单源最短路径，而与该问题相近的还有所有节点对的最短路径问题，它的解决方案就需要用到Floyd算法。

​	但事实上哪怕对于我们已经介绍过的问题，事实上它们的解法还并不能确定是最好的方法，它不像排序算法，已经在理论上被证实最好的时间复杂度是O(nlogn)，而对于以上问题并没有被证明，或许存在一种更好的算法实现，平均复杂度在O(E)级别的，所以说算法还有许多可以值得探讨的地方。