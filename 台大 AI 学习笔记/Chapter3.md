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
- 最优性（较为复杂，此处简单介绍，下文有详细介绍）：
  - 对于Tree Search，需要保证`Admissible`，也就是只要$h(n)\leq h^*(n)$即可
  - 如果是Graph Search，还需要保证`Consistent`（下文介绍）
- 时间复杂度：$O(b^{\epsilon d})$
  - 与$h(n)$有关，假设有一个完美的评估函数$h^*(n)$，$h^*(n)$的预估值与真实值完全一样，那么$h(n)$越是接近这个$h^*(n)$，则时间复杂度越低。
  - 可以使用$\epsilon$来表示二者的接近程度，$\epsilon=0$表示完全一样，$\epsilon=1$表示完全不一样，当$\epsilon=0$时，时间复杂度为最小$O(b^{d})$，当$\epsilon=1$时，时间复杂度为最大$O(b^{d})$
- 空间复杂度：$O(b^{d})$

#### Optimality of A*

##### Admissible

![image-20200317205907119](picture/image-20200317205907119.png)

- 对于Tree Search，需要保证`Admissible`，也就是只要$h(n)\leq h^*(n)$即可
- 如果是Graph Search，还需要保证`Consistent`。

###### Admissible的证明

![image-20200317205322978](picture/image-20200317205322978.png)

##### Consistent

![image-20200317210607580](picture/image-20200317210607580.png)

- 对于Graph Search除了需要`Admissible`，还需要`Consistent`
- 需要一个引理，如果$h(n)$是`Consistent`，那么在A*中任何path的f值都是**非增的**。
- 如果$h (n)$具有了`Consistent`，就是说，在Search过程中，f值会从某个最小值开始慢慢增大，A*每次会在一个固定的f值范围内去展开f值最小的Node，当该范围内的Node全部展开后，再调整被固定的f值，以此循环，如果在范围内找到了一个Goal，那么可知，该Goal一定是最优的。

##### 引理证明

![image-20200317213014786](picture/image-20200317213014786.png)

![image-20200317212949507](picture/image-20200317212949507.png)

#### IDA*

![image-20200317220625790](picture/image-20200317220625790.png)

- IDA\*就是使用IDS的思想，使用A\*的f值来作为限制进行search
- 每一次选取未展开Node的最小f值作为限制L

##### 特性

![image-20200317220938978](picture/image-20200317220938978.png)

- 完备性与最优性与A*完全一样
- 时间复杂度：$O(b^{\epsilon d})$
- 空间复杂度：$O(bd)$，也就是线性复杂度
- 问题
  - 由于使用未展开Node中的最小f值来作为每次迭代加深的限制，这就导致了大量的重复计算，因为可能对于每一个Node的f值都不一样，那么，每一次迭代都只会展开一个Node，这样时间复杂度的前置常数会极大。

