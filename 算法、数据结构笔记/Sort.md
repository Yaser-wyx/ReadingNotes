# 排序



## 引言

​	排序算法有好几种，从最简单的*选择，冒泡，插入排序*，到比较复杂的*归并，堆排序和快速排序*，再到*计数排序，基数排序和桶排序*等。要列举的话足有上百种，但这里只列举几个常用的排序算法，即*冒泡，插入，选择，归并和快排*。

## 冒泡排序

​	冒泡排序是最为基础的一种排序方式，也是初学者最为容易掌握的一种排序方式，下面就简单介绍一下该算法。

​	冒泡算法作为一种O(n^2^)的算法，虽然效率上有所欠缺，但却胜在算法思想简单易学。该算法的核心思想就是重复遍历要排序的数列，一次比较数列中的两个数，如果它们的顺序不对，就将他们交换位置，遍历数列直到没有元素需要交换为止，这就和这个算法的名字一样，大的元素就像泡泡一样不断地上浮到最顶端，而小的元素则随着一次次的交换到最底端。

### 冒泡排序流程：

​	1.从第一个开始比较相邻的两个元素，如果前一个大于后一个，则交换二者位置；

​	2.一次遍历完成后，最大的元素就排在了最后面，接着进行第二次遍历；

​	3.第二次遍历会将前一次遍历找到的最大值排除，在前面找到前半部分的最大值，将之排在最大值前面，以此类推最后我们就完成了整个排序的过程

​	图示：

![Bubble_sort](D:\Note\pictures\Sort\Bubble_sort_animation.gif)

​	代码(java版)：

```java
public void Sort(int[] nums) {
        for (int i = nums.length - 1; i >= 0; i--) {//最外层循环，控制遍历的范围[0~i]
            for (int j = 0; j < i; j++) {//在[0~i]的范围内不断查找这个范围内的最大值将之放在最后
                if (nums[j] > nums[j + 1]) {//如果前一个数大于后一个数，交换二者位置
                    int temp = nums[j];
                    nums[j] = nums[j + 1];
                    nums[j + 1] = temp;
                }
            }
        }
    }
```



## 选择排序

​	选择排序是一种非常直观的排序算法，它每一次遍历都是在未排序部分找到最大（最小）值，然后将它放在未排序的起始位置，然后再从剩余未排序部分继续寻找最大（最小）值，直至全部元素排序完成。

### 选择排序流程：

​	1.首先设一个索引，该索引用于保存最小（最大）值的地址，并给之赋一个初值，初值为未排序的第一个数的索引；

​	2.接着，从第一个未排序元素开始遍历，如果该元素小于（大于）所设索引位置的数，则将该元素的索引赋予所设的索引；

​	3.一次遍历完成后，将索引位置的值与未排序的第一个值进行交换位置，则已将一个数排好顺序；

​	4.不断循环以上步骤直至全部元素排好顺序；

​	图示：

![Selection_sort_animation](D:\Note\pictures\Sort\Selection_sort_animation.gif)

​	代码：

```java
public void Sort(int[] nums) {
        for (int i = 0; i < nums.length; i++) {//遍历的次数
            int min_index = i;//给min_index赋一个初始值
            for (int j = i; j < nums.length; j++) {//从i遍历到最后一个数，从这里面找到最小值，将该索引赋给min
                if (nums[j] < nums[min_index]) {//如果当前数比index所指向的数还要小，就将当前数的索引赋给index
                    min_index = j;
                }
            }
            //一次遍历完成后，将index与i的值交换
            int temp = nums[min_index];
            nums[min_index] = nums[i];
            nums[i] = temp;
        }
    }
```



## 插入排序

​	插入排序也是一种十分直观的排序算法，它是通过将要排序的数列分为已排序部分与未排序部分，然后将未排序的部分不断插入到前面已排序的数列中，直到全部插入完毕。

### 插入排序流程：

​	1.选择未排序部分的第一个，从已排序的数列中从后向前扫描；

​	2.如果已排序好的数列的最后一个大于未排序的第一个，则将二者位置交换；

​	3.接着比较最后第二个是否比之前选择的数大，如果大，接着交换，否则跳出循环（可以提前终止循环）；

​	**图示：**

![Insertion_sort_animation](D:\Note\pictures\Sort\Insertion_sort_animation.gif)

**代码：**

```java
    public void Sort(int[] nums) {
        for (int i = 1; i < nums.length; i++) {//遍历的次数
            int temp = nums[i];
            int j = i;
            for (; j > 0 && temp < nums[j - 1]; j--) {//在已排序部分进行查找temp该插入的部分
                nums[j] = nums[j - 1];//如果num[j-1]个比temp大，就将前一个值复制给后一个数
            }
            nums[j] = temp;//此时，j保存的就是temp该放的位置
        }
    }
```



## 归并排序

​	归并排序是一种高效的排序算法，时间复杂度为nlog(n)，该算法的核心思想是分治法，将大问题不断分解为小问题直至可以直接求解。

### 归并排序流程：

​	1.将要排序的数列不断递归地分割为左右两部分，直至每一部分只有单独的一个数

​	2.分割完之后就要进行合并，比较递归分割后的左右两部分，将这两部分进行比较后，按序放回原始数组中。

​	**图示：**

![Merge-sort-example-300px](D:\Note\pictures\Sort\Merge-sort-example-300px.gif)

​	**代码（递归版）:**

```java
	void Sort(int[] nums) {
        merge(nums, 0, nums.length - 1);
    }
    void merge(int[] nums, int l, int r) {
        if (l >= r) {//如果左边大于右边，此时就代表该部分只有一个数，不再需要进行递归操作
            return;
        }
        int mid = l + (r - l) / 2;//中间位置计算
        merge(nums, l, mid);//递归的分割数列的前半部分
        merge(nums, mid + 1, r);//递归的分割数列的后半部分
        merge_sort(nums, l, mid, r);//分割完之后就进行和并操作
    }

    void merge_sort(int[] nums, int l, int mid, int r) {
        int[] temp = new int[r - l + 1];
        System.arraycopy(nums, l, temp, 0, temp.length);
      	
      //初始化，i指向左半部分的起始索引位置l；j指向右半部分起始索引位置mid+1
        int i = l;
        int j = mid + 1;
        for (int k = l; k <= r; k++) {
            if (i > mid) { // 如果左半部分元素已经全部处理完毕
                nums[k] = temp[j - l];//直接将右半部分剩余的数全部放入原数组中
                j++;
            } else if (j > r) { // 如果右半部分元素已经全部处理完毕
                nums[k] = temp[i - l];//直接将左半部分剩余的数全部放入原数组中
                i++;
            } else if (temp[i - l] > temp[j - l]) {//如果左半部分的值大于有半部分的值
                nums[k] = temp[j - l];//将右半部分的值赋给原始数组
                j++;
            } else {
                nums[k] = temp[i - l];//将左半部分的值赋给原始数组
                i++;
            }
        }
    }
```

**代码（迭代版）:**

```java
void Sort(int[] nums) {
        for (int size = 1; size <= nums.length; size += size) {//一轮循环的长度，就相当于把递归版的分解操作取消掉了
            for (int j = 0; j + size < nums.length; j += size * 2) {//j代表左半部分，j+size代表有半部分
                merge_sort(nums, j, j + size - 1, Math.min(j + size * 2 - 1, nums.length - 1));
              // Math.min(j + size * 2 - 1, nums.length - 1)防止越界，如果size>(num.length/2)的话，r的值就会超过数组的总长度，这是就因该是num.length。
            }
        }
    }

    void merge_sort(int[] nums, int l, int mid, int r) {//该部分与上面一致
        int[] temp = new int[r - l + 1];
        System.arraycopy(nums, l, temp, 0, temp.length);
        int i = l;
        int j = mid + 1;
        for (int k = l; k <= r; k++) {
            if (i > mid) {
                nums[k] = temp[j - l];
                j++;
            } else if (j > r) {
                nums[k] = temp[i - l];
                i++;
            } else if (temp[i - l] > temp[j - l]) {
                nums[k] = temp[j - l];
                j++;
            } else {
                nums[k] = temp[i - l];
                i++;
            }
        }
    }
```



## 快速排序（基础版）

​	快速排序就如它的名字一样，排序速度非常快，是一种nlog(n)级别的排序算法，其核心思想与归并排序一样，也是分治思想，它从待排序的数组中选取一个数作为基准数，然后将比它大的数放在它的右边，比它小的数都放在它的左边，不断重复就可以完成整个数组的排序。

### 快速排序流程：


​		1.随机选取数组中的一个数，将它作为基准数；

​		2.设置两个索引，一个用于遍历数组中数的索引i，另一个是比基准数小的最后一个数位置的索引k；

​		3.从左边开始遍历，如果比基准数小，则将当前的数与第一个比基准数大的数位置交换，否则将i索引加一;

​		4.重复以上操作就将数组中的数分成了两部分，左边比基准数小，右边比基准数大，而且基准数也在它应该在的位置;

​		5.接着进行递归操作，将上一次排序后的左半部分和右半部分分别进行上面的操作，直至全部数排序完成。

​	**图示：**


![Sorting_quicksort_anim](D:\Note\pictures\Sort\Sorting_quicksort_anim.gif)

​	**代码：**

``` java
 //递归调用
    void quick_Sort(int[] nums, int l, int r) {
        if (l >= r) {
            return;
        }
        int p = partition(nums, l, r);//返回的索引左边都比nums[p]小，右边都比nums[p]大
        quick_Sort(nums, l, p - 1);
        quick_Sort(nums, p + 1, r);
    }
    //对nums[l...r]进行分类
    //返回索引p，使得nums[l..p-1]都小于nums[p],右边都大于num[p]
    int partition(int[] nums, int l, int r) {
        int k = l;//k表示比nums[p]小的最后一个元素,即nums[l....k]都是比temp小的数，nums[k+1.....r]都是大于等于temp的数
        int temp = nums[l];
        int random = (int) (Math.random() * (r - l + 1)) + l;//随机算法进行优化
        nums[l] = nums[random];
        nums[random] = temp;
        temp = nums[l];
        for (int i = l + 1; i <= r; i++) {
            if (nums[i] <= temp) {
                int cache = nums[i];
                nums[i] = nums[k + 1];
                nums[k + 1] = cache;
                k++;
            }
        }
        nums[l] = nums[k];
        nums[k] = temp;
return k;
    }
```



## 快速排序（优化版）

​	以上版本的快排在遇到有大量重复元素的数组时，该算法就会退化到n^2级别，之所以会这样，是因为上一个版本如果遇到等于的数是直接将其放在了或者是左边亦或是在右边，会出现左右不平衡的状态，这时候就需要进行优化，将等于的数进行左右分配平均一下，这样就不会出现左右不平衡的情况，这也就是双路快排的实现。

​	**图示：**

![Sorting_quicksort2](D:\Note\pictures\Sort\Sorting_quicksort2.png)

​	**代码：**

``` java
 void quick_sort(int[] nums, int l, int r) {
        if (l >= r) {
            return;
        }
        int p = partition2(nums, l, r);
        quick_sort(nums, l, p - 1);
        quick_sort(nums, p + 1, r);

    }

    int partition2(int nums[], int l, int r) {
        //随机化处理
        int random = RandomUtils.nextInt(l, r);
        int temp = nums[random];
        nums[random] = nums[l];
        nums[l] = temp;
        temp = nums[l];

        int k = l + 1;//设置索引，从l到k的数都是小于等于temp的
        int j = r;//设置索引，从j到r的数都是大于等于temp的
        boolean is_left = true;//设置从那边开始遍历
        while (k <= j) {//设置边界条件
            if (is_left) {
                if (nums[k] >= temp) {
                    is_left = false;//如果nums[k]大于等于temp说明这个数需要放在右边区域
                } else {
                    k++;//否则k索引加一
                }
            } else {
                if (nums[j] <= temp) {
                    is_left = true;//如果nums[k]小于等于temp说明该数需要放在左边区域
                    int cache = nums[k];//交换两者位置，即将之前遍历到的大于等于temp的数与当前小于等于temp的数交换位置。
                    nums[k] = nums[j];
                    nums[j] = cache;
                    k++;
                    j--;
                } else {
                    j--;
                }
            }
        }
        nums[l] = nums[j];//注意，该处是j的索引而不是k的索引，因为遍历到最后j与k会交换位置，即j=k-1，
        // 此时j才是比temp小的最后一个数，而k变成了比temp大的第一个数
        nums[j] = temp;
        return j;
    }
```

### 快速排序（再优化）

​	基于上面的思想，我们还可以进一步优化，对于重复的元素，我们将其单独分出来，即数组左边一段比基准数小，中间一段和基准数一样，右边一段比基准数大，这样数组就分成了三部分，这就是三路快排的基本思想。

**图示：**

​	![Sorting_quicksort3](D:\Note\pictures\Sort\Sorting_quicksort3.png)

**代码：**

```java
    void Sort(int[] nums) {
        quick_sort(nums, 0, nums.length - 1);
    }

    void quick_sort(int[] nums, int l, int r) {
        if (l >= r)
            return;
        int[] p = partitions(nums, l, r);
        quick_sort(nums, l, p[0]);
        quick_sort(nums, p[1], r);

    }

    int[] partitions(int[] nums, int l, int r) {
        int random = RandomUtils.nextInt(l, r);
        int temp = nums[l];
        nums[l] = nums[random];
        nums[random] = temp;
        temp = nums[l];

        int lt = l;//该索引表示[l...lt]都小于temp
        int gt = r + 1;//该索引表示[gt...r]都大于temp
        int i = lt + 1;//该索引表示[lt+1...i]都等于temp
        while (i < gt) {//使用i使用进行遍历
            if (nums[i] < temp) {//如果比temp小，就与第一个等于temp的位置交换
                int cache = nums[i];
                nums[i] = nums[lt + 1];
                nums[lt + 1] = cache;
                i++;//同时i索引与lt索引都自增1
                lt++;
            } else if (nums[i] > temp) {//如果比temp大，就与第一个大于temp之前的位置交换
                int cache = nums[i];
                nums[i] = nums[gt - 1];
                nums[gt - 1] = cache;
                gt--;//同时gt索引要自减
            } else {
                i++;
            }
        }
        nums[l] = nums[lt];
        nums[lt] = temp;
        return new int[]{lt, gt};
    }

```

## 总结

​	以上就是这些主要排序的基本思路及实现，可能有的地方实现的方式与一些书上的实现方式有所不同，但大体的思路都是一样的，其实算法的实现并不是十分的重要，重要的还是对算法思想的理解，思想理解了，实现的方法可以千差万别。