#堆

[TOC]

## 引言

​	堆是计算机科学中一类特殊的数据结构，它虽然也被叫做优先队列，但它并不是队列，它并不是按照先进先出的原则进行存储数据的，它更像是一棵二叉树。

## 最大堆##

###定义###

​	最大堆的实现是通过构造二叉堆，而二叉堆的实质是一棵完全二叉树，它具备以下性质：

- 任一节点都是小于（最小堆）或大于（最大堆）其子节点，而根节点是最大或最小值。
- 堆总是一棵完全二叉树，即除最后一层外，其它层都是满的，而最后一层也应是自左向右填入数据

**图示:**

![二叉堆](D:\Note\pictures\Heap\二叉堆.png)

###实现###
**代码实现**

```java
public class MaxHeap<T extends Comparable> {
    private T[] data;//数据
    private int count;//保存的数据个数

    public MaxHeap(int capacity) {
        data = (T[]) new Comparable[capacity + 1];//因为是从1开始存放数据，所以容量要比传进来的容量加一
    }

    public MaxHeap(T arr[]) {//使用传进来的值进行构造
        this(arr.length);
        System.arraycopy(arr, 0, data, 1, arr.length);//复制数组
        count = arr.length;
        Hepify();
    }

    private void Hepify() {
        int end = count / 2;//找到最后一个有孩子的父亲节点

        while (end > 0) {
            shift_down(end--);//从这最后一个有孩子的父节点开始做shift_down操作直到根节点
        }

    }

    public int size() {
        return count;
    }

    public boolean isEmpty() {
        return count == 0;
    }

    public void insert(T item) {//直接在最后数组最后一个位置进行插入，之后再进行shift_up操作
        assert count + 1 <= data.length;
        data[++count] = item;
        shift_up();
    }
	//核心代码
    private void shift_up() {//该方法是将最后一个元素不断向上比较并交换，直到其值小于其父节点结束交换
        int index = count;
        while (index > 1 && data[index / 2].compareTo(data[index]) < 0) {
            swap(index, index / 2);//如果当前索引不是根节点且索引处的值大于其父亲节点的值，则交换二者
            index /= 2;//更新索引
        }
    }

    public T pop() {//抛出根节点的元素之后进行shift_down操作
        if (count == 0)
            return null;
        T element = data[1];//保存要返回的值
        data[1] = data[count];//将根节点元素与最后一个元素进行调换位置
        data[count--] = null;//将最后一个元素设为空

        shift_down(1);
        return element;//返回保存的值
    }
	
  	//核心代码
    private void shift_down(int index) {//该操作在抛出元素后进行，为了将抛出后的堆重新排序
        //不断将index处的元素向下调换位置，直到该元素比两个子节点都大
        T value = data[index];//先保存index处的值，最后在找到value该放的位置之后再将值赋给该位置，减少交换次数
        while (index * 2 <= count) {//判断该节点是否有孩子
            int bigger = index * 2;//保存两个孩子中较大值的索引
            if (bigger + 1 <= count && (data[bigger].compareTo(data[bigger + 1]) < 0)) {
              //判断是否越界，然后再比较两个子节点，选出较大的那个和index处的元素进行比较
                bigger++;
            }
            if (value.compareTo(data[bigger]) < 0) {//如果比较大的那个子节点小，则交换位置
                data[index] = data[bigger];
                index = bigger;//更新索引的值
            } else {//否则就跳出循环
                break;
            }
        }
        data[index] = value;//将value该存放的位置赋值
    }
    private void swap(int i, int j) {
        T temp = data[i];
        data[i] = data[j];
        data[j] = temp;
    }
}

```

###核心代码讲解###

​	在这里，我们是使用数组来实现堆这种数据结构，习惯上一般从`1`这个索引开始存放数据，因为这样可以十分方便的检索其左右孩子节点。

​	在上面代码中有两个方法是核心代码，一个是shift_up，该方法是将新插入的元素不断向上比较，直到比父节点小或是已经在根节点上了为止；另一个是shift_down，该方法是将传入索引处的值不断与两个子节点中较大的值比较，直到比两个子节点都大或已经处在叶子节点为止。

​	

###衍生###

​	使用堆这种数据结构可以很方便的实现一种排序算法，即堆排序。算法的核心是使用堆这种数据结构，因为堆的特性决定了其根元素是整个堆的最大值，那么我们只要不断将这个最大值进行抛出即可，因为堆自身会对抛出操作后的数据进行自动维护操作，所以我们无需进行其他操作。

​	**代码**

```java
void Sort(Integer[] nums) {
        MaxHeap<Comparable> heap = new MaxHeap<>(nums);//直接使用nums这个数组初始化heap
        for (int i = nums.length - 1; i >= 0; i--) {
            nums[i] = (Integer) heap.pop();//进行抛出操作，之后堆回自动维护
        }
    }
```

###	优化###

​	上述方法是借用堆这种数据结构进行的堆排序，但我们还可以对其进行空间上的优化，使其不用再开辟新的内存空间，直接在待排序数组上进行排序操作。

​	**代码**

```java
void Sort(Integer[] nums) {
            for (int i = (nums.length - 1) / 2; i >= 0; i--) {//从最后一个有孩子的叶子节点开始进行shiftDown操作
                ShiftDown(nums, i, nums.length - 1);
            }
            for (int i = nums.length - 1; i > 0; i--) {
                int temp = nums[0];//将最大值放到最后
                nums[0] = nums[i];
                nums[i] = temp;
                ShiftDown(nums, 0, i - 1);//需要将i再减1是因为已经将未排序中最大的元素放在了未排序数中最后一个的位置， 而且也不要对其再次进行shiftDown操作了
            }
        }
    	//该处shift_down操作与heap中的基本一致
        private void ShiftDown(Integer[] nums, int index, int n) {//n代表最后一个元素的索引
            int temp = nums[index];
            while (index * 2 + 1 <= n) {//因为是从0开始进行编号的，所以左孩子的编号是2*index+1
                int bigger = index * 2 + 1;
                if (bigger + 1 <= n && nums[bigger] < nums[bigger + 1]) {
                    bigger++;
                }
                if (temp < nums[bigger]) {
                    nums[index] = nums[bigger];
                    index = bigger;
                } else {
                    break;
                }
            }
            nums[index] = temp;
        }
```

##最大索引堆##

​	上面实现的最大堆优点十分明显，就是稳定，并且衍生出来的堆排序在时间效率上面也很可观，属于nlogn级别的算法，但与此同时缺点也十分明显，主要有两点：

- 在元素的数据比较复杂的时候，移动元素比较之低效，因为我们在移动元素时是直接移动元素本身，如果该元素是一个上千字节的字符串，那效率就会十分之低；

- 无法更改元素的值，因为元素一旦入堆之后，它的位置就不再确定，同时如果更改其值就破坏了堆本身的性质；

  ​	这两个缺点，第一个解决还比较之容易，可以直接更改指针的指向，而第二个就比较之棘手了，虽然也可以解决，但会直接导致算法在某些情况下的时间复杂度降至O(n²)，这不是我们所想要的，所以这时候就产生了索引堆这个概念。

###索引堆定义###

​	所谓索引堆就是不再像之前实现的普通最大堆那样直接对元素进行操作，而是对元素的索引进行操作，这样就从根本上解决了上述两个难题。

​	**图示：**

![最大索引堆](D:\Note\pictures\Heap\最大索引堆.png)

​	图中index表示的是在这个堆中，该处节点在data中索引的值，以图中index[1]为例，该处的值为10，意思就是data[10]这个元素时处在堆中1（也就是根节点）这个位置上的。

###索引堆实现###
​	**代码：**

 						(此处我加了一个优化，即reverse数组，后面有讲解)

``` java
public class Index_MaxHeap<T extends Comparable> {
    private T[] data;//存放数据
    private int count;//数据的个数
    private int[] indexes;//索引
    private int[] reverse;//索引数组的索引
    private int capacity;//容量

    public Index_MaxHeap(int capacity) {//初始化
        this.capacity = capacity;
        data = (T[]) new Comparable[capacity + 1];
        count = 0;
        reverse = new int[capacity + 1];
        indexes = new int[capacity + 1];
    }

    public void insert(int index, T item) {//将元素插入到index处
        assert count + 1 <= capacity;
        assert index + 1 >= 1 && index + 1 <= capacity;//防止数组越界
        index++;//之所以要加一，是因为在里面是从1开始索引的，而外界是从0开始索引的
        data[index] = item;//插入元素
        indexes[++count] = index;//index保存的是data的索引
        reverse[index] = count;//count保存的是indexes的索引
        shift_up(count);
    }

    private void shift_up(int index) {//index是在indexs里面的索引，indexes[index]是指data里面的索引
        while (index > 1 && data[indexes[index / 2]].compareTo(data[indexes[index]]) < 0) {//如果父节点小于子节点
            //交换二个索引的位置
            int cache = indexes[index / 2];
            indexes[index / 2] = indexes[index];
            indexes[index] = cache;
            reverse[indexes[index / 2]] = index / 2;//reverse的索引是data的索引，reverse的值是indexes的索引，reverse将二者相互关联了起来
            reverse[indexes[index]] = index;
            index /= 2;
        }
    }

    public T extractMax() {//删除并返回最大值
        assert count > 0;
        T temp = data[indexes[1]];//先保存要返回的元素
        indexes[1] = indexes[count];//将根节点的值赋值为最后一个索引
        indexes[count] = 0;//清除原始数据
        reverse[indexes[1]] = 1;
        reverse[indexes[count--]] = 0;
        shift_down(1);
        return temp;

    }

    public int extractMaxIndex() {//删除并返回最大值索引
        assert count > 0;
        int temp = indexes[1] - 1;
        indexes[1] = indexes[count];
        indexes[count] = 0;
        reverse[indexes[1]] = 1;
        reverse[indexes[count--]] = 0;
        shift_down(1);
        return temp;

    }

    public T getMax() {//获取最大值
        assert count > 0;
        return data[indexes[1]];

    }

    public int getMaxIndex() {//获取最大值的索引
        assert count > 0;
        return indexes[1] - 1;
    }

    public T getItem(int index) {//通过索引查找数据
        assert contain(index);
        index++;
        return data[index];
    }

    public int getIndex(T item) {//通过数据查找索引
        assert count > 0;
        for (int i = 1; i <= count; i++) {
            if (data[indexes[i]].compareTo(item) == 0) {
                return indexes[i] - 1;
            }
        }
        return -1;

    }

    private boolean contain(int i) {//判断该元素是否存在
        assert i + 1 >= 1 && i + 1 <= capacity;
        return reverse[i + 1] != 0;
    }

  public void change(int index, T item) {//更改指定元素的值
        assert contain(index);
        index++;
        data[index] = item;

        shift_down(reverse[index]);
        shift_up(reverse[index]);

    }

    private void shift_down(int index) {//index是指indexes里面的
        while (index * 2 <= count) {
            int bigger = index * 2;//找到较大的子节点
            if (bigger < count && data[indexes[bigger]].compareTo(data[indexes[bigger + 1]]) < 0) {
                bigger++;
            }
            //将父节点与较大的子节点比较
            if (data[indexes[bigger]].compareTo(data[indexes[index]]) > 0) {
                int cache = indexes[bigger];
                indexes[bigger] = indexes[index];
                indexes[index] = cache;
                reverse[indexes[index]] = index;
                reverse[indexes[bigger]] = bigger;
            } else {
                break;
            }
        }
    }
}

```

​	在上述实现中有一个reverse数组，该数组是indexes的索引，让我们来梳理一下data，indexes和reverse它们之间的关系：

![最大索引堆](D:\Note\pictures\Heap\最大索引堆1.png)

​	即假设有一个在data中的元素，位置是i,那么reverse[i]就是indexes中该元素的位置，例如图中的data[1]=15，reverse[1]指向的值是8，indexes[8]所保存的值1就是data中15的位置。更浅显一点就是indexes的值是data的索引，而reverse的值是data里每一个元素在堆的中位置，因为indexes的索引就是堆中的位置，二者等价。

##总结##

​	堆这种数据结构在计算机科学中是一种十分重要的数据结构，它可以实现许多重要的应用，例如操作系统的任务调度，游戏中AI自动攻击对象的优先级等等，另外堆除了上面的最大堆和最大索引堆还有其它例如二项堆，斐波那契堆等，这些可能会在以后的文章里面进行归纳介绍。











