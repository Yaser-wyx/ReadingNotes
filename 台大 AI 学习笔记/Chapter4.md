# 第四周

[toc]

## Beyond Classical Search(超越经典搜索)

- 大纲

![](picture/image-20200524213010018.png)

## Black-Box Optimization（黑盒优化）

![image-20200524222312096](picture/image-20200524222312096.png)

- 计算机主要解决两类问题：
  1. Equation Solving: 只有一个最佳解。
  2. Optimization: 存在多个解，对于每一个解会反馈得到不同的分数，目标就是获取最大分数的那个解。
- Optimization 存在两种:
  - 一种是Black-Box Optimization，也就是**不知道从$x$映射到$y$的$f$，即$f_{unknow}(x) \to y $。**
  - 另一种是Glass-Box Optimization，**知道从$x$映射到$y$的$f$，即$f_{know}(x) \to y $。**
    - 虽然一般上来说Glass-Box问题比较简单，但是在实际应用中，因为所给的$f_{know}(x)$比较复杂，可能会由一系列（成百上千个）的函数构成，导致**无法通过简单的求微分来解决，此时将Glass-Box问题也视为Black-Box问题来解决。**

## Steepest Descent（爬山算法）

![image-20200525121646118](picture/image-20200525121646118.png)

- 爬山算法就是在当前$s$的周围，检索并用$f$（上文图片中的）计算所有的状态，从中取出最好的一个状态$s'$，并从当前$s$转移到$s'$，不断重复，直到周围没有更好的状态为止。

- *关键问题：对于周围的状态如果与当前状态一样，是否需要进行转移？*
  ![image-20200525121808478](picture/image-20200525121808478.png)

  - 如果不转移，在如图所示的情况，也就是有一个平原的地方就无法达到最好。
    ![image-20200524223647850](picture/image-20200524223647850.png)
  - 如果进行转移，就会出现在一个平原上进行无穷循环。
    ![](picture/image-20200525122059301.png)
  - 解决办法：一般使用$\le$，同时设置一个`step limit​`，当相同的情况超过这个`limit`，则不再继续执行下去。
    - 缺点：虽然限制了无穷循环的问题，但也限制了该算法探索的能力。