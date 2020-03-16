# 第三周

[toc]

## Informed Search

- 大纲

![image-20200316211522157](picture/image-20200316211522157.png)

### Best-First-Search

![image-20200316210239935](picture/image-20200316210239935.png)

- Informed Search也就是启发式搜索，它包含对未来的一个预估
- 使用一个启发函数来对接下来的每一个Node进行评估，然后将评估结果最好的那个Node进行展开
- 启发式会评估从指定Node到**最近的Goal**所需花费的Cost
- 两个特例：
  - Greedy Search直接使用启发式的评估结果，即估计指定Node到达最近Goal所需要的的花费h(n)作为F值
  - A*使用从Start到达指定Node所需要的花费g(n)+估计该指定Node到达最近Goal所需要的的花费h(n)作为F值

#### Greedy Search

![image-20200316212447938](picture/image-20200316212447938.png)

- Greedy Search展开哪些最接近Goal的Node

##### 特性

![image-20200316212611838](picture/image-20200316212611838.png)

- 不具有完备性
  - 如果使用Tree Search，则会进入循环无法出来。
  - 如果使用Graph Search，可以基本保证完备性，但是在状态为无穷的时候，无法保证，且会导致需要大量的记忆空间。
- 不具备最优性，因为无法保证完备性
- 时间复杂度：$O(b^m)$，但是如果启发函数所给的预估值十分好，那么时间复杂度会很低
- 空间复杂度：$O(b^m)$，会保存所有Node到内存中。

#### A* Search

![image-20200316214533981](picture/image-20200316214533981.png)

- A*总体思想是避免对总体Cost大的Node进行展开
- $f(n) = g(n) + h(n)$
  - $g(n)$：从起始节点Start到达指定Node所花费的Cost
  - $h(n)$：评估从指定Node到达目标所需要的花费的Cost
  - $f(n)$：评估从起始节点Start出发，经过指定Node达到Goal所需要的的Cost
- A*结合了Greedy Search与Uniform-Cost Search的优点

##### 特性

![image-20200316214946211](picture/image-20200316214946211.png)

- 具备完备性：除非在无限个Node中，所有的的$StepCost\leq0$。
- 最优性：TODO
- 时间复杂度：$O(b^{\epsilon d})$
  - 与$h(n)$有关，假设有一个完美的评估函数$h^*(n)$，$h^*(n)$的预估值与真实值完全一样，那么$h(n)$越是接近这个$h^*(n)$，则时间复杂度越低。
  - 可以使用$\epsilon$来表示二者的接近程度，$\epsilon=0$表示完全一样，$\epsilon=1$表示完全不一样，当$\epsilon=0$时，时间复杂度为最小$O(b^{d})$，当$\epsilon=1$时，时间复杂度为最大$O(b^{d})$
- 空间复杂度：$O(b^{d})$

