# 二叉搜索树
[TOC]

## 引言

​	二叉搜索树（BST），也称二叉查找树，它是一种链式存储结构，是基于二分查找这一算法思想衍生而来的。

## 二分查找##

​	因为二叉搜索树是基于二分查找这一算法衍生而来的数据结构，所以在这里简单介绍一下二分查找。

**注：二分查找算法使用的前提是要查找的数据已经排好序，否则该算法将无法使用。**

​	二分查找又称折半查找，它是一种log(n)级别的查找算法，所以其效率十分高，虽比不上哈希算法的O(1)的时间复杂度，但其胜在实现方式简单而且也易于理解。

​	二分查找算法的思路是在已排好序的数组中，先从中间开始查找目标，如果找到则将索引返回；否则，如果该数比目标数小，则接着在右边查找，如果该数比目标数大，则在左边继续查找。以此类推，直到找到为止，如果没有找到则返回-1.

​	**图示：**

​						![Binary_search_into_array](D:\Note\pictures\BST\Binary_search_into_array.png)

​	**代码：**

``` java
public class BinarySearch<T extends Comparable> {
    //二分查找，在arr里面查找target，并返回索引
    public int search1(T[] arr, T target) {//迭代版
        int l = 0;//左边界
        int r = arr.length - 1;//右边界
        while (l <= r) {//查找条件
            int mid = l + (r - l) / 2;//中间索引，此处不使用(l+r)/2是因为该方法在l和r都很大的情况下会出现精度溢出的问题
            if (arr[mid].compareTo(target) == 0) {//找到怎返回索引
                return mid;
            } else if (arr[mid].compareTo(target) < 0) {//如果比目标小则在右半部分继续查找
                l = mid + 1;//更新左边界
            } else {//如果比目标大则在左半部分继续查找
                r = mid - 1;//更新右边界
            }
        }
        return -1;//如果没有找到就返回-1
    }

    public int search2(T[] arr, T target, int l, int r) {//递归版，思想与迭代版一致
        int mid = l + (r - l) / 2;
        if (arr[mid].compareTo(target) == 0) {
            return mid;
        } else if (arr[mid].compareTo(target) < 0) {
            return search2(arr, target, mid + 1, r);
        } else {
            return search2(arr, target, l, mid - 1);
        }
    }
    
}
```



## 二叉搜索树定义##

​	二叉查找树是指一棵空树，或是具有以下特征的二叉树：

​		1.如果它的左子树不为空，则左子树上的节点值均小于根节点上的值；

​		2.如果它的右子树不为空，则右子树上的节点值均大于根节点上的值；

​		3.它的左右子树也均为二叉搜索树，也遵循以上规则，即它是一个递归的数据结构；

​	**图示：**

​						![二叉树](D:\Note\pictures\BST\二叉树.jpg)

​	**代码：**

- 节点定义代码

```java
public class Node<Key extends Comparable<Key>, Value> {
    protected Key key;//该节点的key值
    protected Value value;//一个key值所对应的value值
    protected Node<Key, Value> left;//左子树
    protected Node<Key, Value> right;//右子树节点

    public Node(Key key, Value value) {
      //使用传入的key和value值来初始化节点
        this.key = key;
        this.value = value;
        this.left = this.right = null;
    }

    public Node(Node<Key, Value> node) {
      //使用传入的node节点来初始化节点
        this.key = node.key;
        this.value = node.value;
        this.left = node.right;
        this.left = node.left;
    }
}
```

- 二叉搜索树定义代码

```java
public class BST<Key extends Comparable<Key>, Value> {
    private Node<Key, Value> root;//根节点
    private int count;//节点数

    public BST() {//初始化
        this.root = null;
        this.count = 0;
    }

    public int size() {//节点数
        return count;
    }

    public boolean IsEmpty() {//判空
        return count == 0;
    }
}
```



## 二叉搜索树节点插入##

​	二叉树节点的插入本质就是一个二分查找算法，先与根节点比较，如果与根节点一样则直接更新根节点的值，若比它小则继续在根节点的左子树上进行插入，否则在右子树上进行插入，依次递归下去，直到找到插入节点为止。

​	**代码：**

```java

    public void insert(Key key, Value value) {
        root = insert(root, key, value);//更新插入新的节点后的根节点
    }

   private Node insert(Node node, Key key, Value value) {
        if (node == null) {//递归终止条件，如果该节点为空，则直接新建节点
            count++;//新增了节点
            return new Node(key, value);//返回新增的节点
        }

        if (node.key.compareTo(key) == 0) {
            node.value = value;//更新value值
        } else if (node.key.compareTo(key) > 0) {//如果比node节点的key值小，则插入node的左子树中
            node.left = insert(node.left, key, value);
        } else {
            node.right = insert(node.right, key, value);//如果比node节点的key值大，则插入node的右子树中
        }
        return node;//返回插入后的根节点

    }
```



## 二叉搜索树节点查找##

​	二叉树的查找也与二分查找基本一致，先是与根节点比较，若根节点就是查找的对象，就返回，否则如果小于根节点则在左子树中继续查找，如果大于根节点则在右子树中查找，直到找到节点或是返回null。

​	**代码：**

```java
   //查找值为key的节点
    public Value search(Key key) {
        return search(root, key);
    }
   //在以root为根的二叉搜索树中查找键值为key的节点
    private Value search(Node node, Key key) {
        if (node == null) {
            return null;//如果该节点为null
        }
        if (node.key.compareTo(key) == 0) {
            return (Value) node.value;//找到了要查找的节点
        } else if (node.key.compareTo(key) < 0) {
            return search(node.right, key);//递归查找右子树
        } else {
            return search(node.left, key);//递归查找左子树
        }
    }
```



## 二叉搜索树遍历##

###前序遍历，中序遍历和后序遍历###

​	一般树的遍历分为三种：*前序遍历，中序遍历和后序遍历*。

​	前序遍历是先访问一棵树的根节点，然后再是左子树和右子树；中序遍历是先访问一棵树的左子树，接着是根节点，最后再是右子树；后序遍历则先访问左右子树，最后访问根节点元素。

​	**图示：**

​							![traverse](D:\Note\pictures\BST\traverse.png)	

​	从以上可以看出，所谓的前中后序遍历的不同点主要在访问根节点的不同时机上面，而这些遍历算法的核心思想就是深度搜索，即沿着树的深度遍历树的节点，尽可能深的搜索树的分支，直到所有的节点都被访问过为止。

​	**代码：(递归版)**

``` java
  	//前序遍历
    public void preOrder() {
        preOrder(root);
    }
    
    private void preOrder(Node node) {
        if (node != null) {//当前节点不为空
            System.out.println(node.key);//先访问当前节点，其实就是根节点
            preOrder(node.left);//访问左子树
            preOrder(node.right);//访问右子树
        }
    }

    //中序遍历
    public void inOrder() {
        inOrder(root);
    }
	
    private void inOrder(Node node) {
        if (node != null) {//当前节点不为空
            inOrder(node.left);//先访问左子树
            System.out.println(node.key);//访问当前节点，即根节点
            inOrder(node.right);//最后访问右子树
        }
    }

	//后序遍历
    public void postOrder() {
        postOrder(root);
    }	
	
    private void postOrder(Node node) {

        if (node != null) {//当前节点不为空
            postOrder(node.left);//先访问左子树
            postOrder(node.right);//然后访问右子树
            System.out.println(node.key);//最后访问根节点
        }
    }
```

​	在此处，由于是一棵二叉搜索树，所以对于其中的中序遍历有一个特殊的用途，就是可以直接将树的数据进行排序，因为二叉树的性质决定它的左边永远比根节点小，右边永远比根节点大，而中序遍历就是先遍历左子树，然后再是根节点，最后才是右子树，这与二叉树的性质完全一致，所以可以进行排序。

###层次遍历###

​	另外，对于树来说还有一种遍历方式，即层次遍历，它是从一棵树的根节点出发，遍历完一层之后再遍历下一层。它的核心思想是广度搜索，也就是尽可能的从根节点出发，将其周围的一层访问完之后再访问更深一层的节点。

​	**图示：**

![traverse2](D:\Note\pictures\BST\traverse2.jpg)

​	**代码：**	

```java
//层次遍历
    public void levelOrder() {
        PriorityQueue<Node> queue = new PriorityQueue<>();//队列
        queue.add(root);//将根节点放入队列中
        while (!queue.isEmpty()) {//直到队列为空，遍历结束
            Node<Key, Value> node = queue.poll();//从队列中拿到一个元素
            if (node != null) {//如果该节点不为空
                if (node.left != null)
                    queue.add(node.left);//如果左节点存在，则将其入队
                if (node.right != null)
                    queue.add(node.right);//如果右节点存在，则将其入队
                System.out.println(node.key);
            }
        }
    }
```



## 二叉搜索树节点删除##

二叉树节点删除是二叉树实现的一个难点，我们在实现的时候需要考虑三种情况：

1. 没有孩子节点	

2. 只有一个孩子，即只存在一个左子树或是右子树中的一个

3. 左右孩子都存在

  也许一开始没有很好地思路，那我们先考虑两种特殊情况，即删除二叉树的最小值与最大值，这两个是比较容易的。

###删除最小值###

  根据二叉树的定义，我们要删除最小值首先需要不断递归查找一棵树的左节点，直到该节点不存在左节点为止，然后将该节点的右节点赋值给该节点根节点的左节点，说的简单点就是用它右节点的值代替自己的位置即可。

  ​**代码：**

```java
  //删除最小节点
    public void removeMin() {
        if (root != null) {
            root = removeMin(root);//更新删除最小节点后的根节点
        }
    }
       private Node removeMin(Node node) {
        if (node.left == null) {
            count--;//减去节点个数
            return node.right;//如果right为空则直接返回空，否则将右节点放在node根节点的左子树上，也就是代替node节点
        }
        node.left = removeMin(node.left);
        return node;
    }
```



### 删除最大值##

​	有了上面删除最小值的经验，删除最大值也与其一样，只不过是在右节点中不断查找。

​	**代码：**

``` java
   //删除最大节点
    public void removeMax() {
        if (root != null) {
            root = removeMax(root);
        }
    }
    private Node removeMax(Node node) {
        if (node.right == null) {
            count--;//减去节点个数
            return node.left;//如果left为空则直接返回空，否则将左节点放在node根节点的右子树上
        }
        node.right = removeMax(node.right);
        return node;

    }
      
```

### 删除任意节点###

​	删除任意节点其实与上面的删除最大最小值节点类似，只不过考虑的东西略复杂一些。

​	**思路：**

- 首先，我们先找到要删除的节点，找到之后进行判断是否有孩子节点，如果没有则将该节点删除即可

  **图示：**

  ![delete1](D:\Note\pictures\BST\delete1.png)

  ​

- 如果有孩子节点，则判断只是有左孩子还是只有右孩子存在，亦或是二者都有。

- 如果只是有左右两个孩子中的一个，则将存在的那个孩子节点代替自己的位置即可。

  **图示：**![delete2](D:\Note\pictures\BST\delete2.png)

  ​

- 若果二者均存在，那么我们需要找到要删除节点的后继节点`(或者也可以是前驱节点，这里用后继节点作为例子，二者思想一样)`。

- 要找到这个后继节点，我们可以这样想，它就排在要删除节点的后一位，那么它肯定是在右子树里面，因此我们就需要在右子树中找，而且因为该节点只比要删除节点大，那么换而言之就是，该节点比要删除节点的右子树中其它节点都要小，这样一来的话不就和我们前面的删除最小节点一样了么，只需要不断递归查找要删除节点右子树的最小值即可，这也是Hibbard Deletion的算法思想。

  **图示：**

  ![Hibbard Deletion](D:\Note\pictures\BST\Hibbard Deletion.png)

  ​

  **代码：**

```java
  //删除指定节点
    public void remove(Key key) {

        if (root != null) {
            root = remove(root, key);//更新删除节点后的根节点
        }
    }

    private Node remove(Node node, Key key) {
        //三种情况
        //1.左右节点均不存在
        //2.只存在左右节点中的一个
        //3.左右节点都存在
      
        if (node == null) {
            return null;//如果没有该节点，则返回空
        }
      
        //先找到要删除的节点
        if (node.key.compareTo(key) == 0) {//找到了要删除的节点
            //前两种情况的处理方式
            if (node.left == null) {
                //只有右孩子
                //注意，此处将左右孩子都没有的情况隐藏在里面了
                //因为如果左右孩子都没有，则会直接进入该分支，而因为右孩子也为空，所以直接返回null
                count--;
                return node.right;
            } else if (node.right == null) {
                //只有左孩子
                count--;
                return node.left;
            }

            //左右节点都存在
            Node<Key, Value> temp = new Node<>(Mininum(node.right));//先保存要删除的节点的后继节点
            temp.right = removeMin(node.right);//返回删除该后继节点后的根节点，注：该根节点就是要删除节点的右节点
            temp.left = node.left;//将要删除节点的左孩子赋值给后继节点
            return temp;//返回后继节点

        } else if (node.key.compareTo(key) > 0) {
          
      //要删除的节点在node节点的左边，则在node的左子树中查找，并将删除节点后的左子树返回并更新node的左子树
            node.left = remove(node.left, key);
            return node;//返回更新后的node
          
        } else {
          
      //要删除的节点在node节点的右边，则在node的右子树中查找，并将删除节点后的左子树返回并更新node的左子树
            node.right = remove(node.right, key);
            return node;//返回更新后的node
        }

    }
```



## 总结##

​	以上便是二叉搜索树的整个实现流程，二叉树是计算机科学中一种重要的数据结构，虽然其在大多数情况下效率都十分高，但在一些特殊情况下就会变得极其糟糕，从O(logn)退化至O(n^2^)，例如我们的数据是极其有序的，即输入的数据是从1至100的，这种情况下1就是根节点，而其它数都在右节点，最要命的是整棵树是不存在左节点的，这时候树就退化成了一个链表了，对于这种情况，解决方法之一就是仿照快排中的一个优化思想，就是随机算法，将输入的数据做个随机化处理，这样退化成链表的概率就大大降低了，还有另一种方法就是平衡二叉树，而平衡二叉树中最出名的几种是：`红黑树，AVL树，2-3-4树`，它们都可以很好地处理上述遇到的问题，我会在后续的更新中实现它们。



