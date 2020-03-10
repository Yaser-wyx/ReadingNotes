# 第二周

[toc]

## 无信息搜索(Uninformed Search)

### Problem-Solving Agent

![image-20200310181124911](picture/image-20200310181124911.png)

一个以Search为中心的Problem-Solving Agent：

- 在初始状态下，首先它会根据对**当前世界的猜想（State）**来规划一个Goal。
- 接着，根据**当前的State与Goal**去将Problem公式化。
- 然后将这个Formulate的**Problem作为输入**给Search进行执行。
- Search执行后返回一个执行序列Seq
- 对执行序列中的**每一个动作依次执行，并返回执行结果，直到执行序列为空**。
- 当seq为空后，在根据当前State进行Goal的规划，也就是返回到了步骤一，**不断循环执行整个过程**。

#### Example：

![image-20200310183719256](picture/image-20200310183719256.png)

- 规划的目标：到达台北车站
- 构造的问题：
  - States：所在地铁的不同站点
  - Actions：在两个站点间进行转移
- Search方案：一个移动序列

![image-20200310184111863](picture/image-20200310184111863.png)

- 当前所处站点：古亭
- 目标：台北车站

![image-20200310184231324](picture/image-20200310184231324.png)

- Problem的构建：Problem的定义需要五个组成部分
  1. 初始状态
  2. 在某一个State下的可选Action集合
  3. 在某一State下，执行一个Action后的结果，或者是某一个State下所有可能的转移结果集合
  4. 对Goal进行测试，Goal可能是一个Set，所有在Set里的都是可取的Goal
  5. 从Initial State 到一个Goal的Cost。
- Solution是一个从起始状态到一个Goal的行动序列。

### 抽象化

![image-20200310212248779](picture/image-20200310212248779.png)

- 现实生活中的Problem转化到CS中的Problem后，由于高度的抽象化，会导致**丢失许多Details**，所以最后计算机给出的**Solution可能是无法使用**的，因此我们需要对所给方案做出一个**保证**，需要保证这些方案是**可实现的**。

- 要保证方案可实现，就需要使得**当前Real State集合中的所有子State**（any state）对应到**下一个Real State集合中的部分子State**（some state）。

- Formulate一个好Problem会大大减少State的space。


### 搜索类算法

#### Tree Search Algorithm

![image-20200310220317649](picture/image-20200310220317649.png)

- 使用Problem的初始状态来初始化一个链表或队列
- 不断重复以下步骤：
  - 如果当前链表为空，则返回失败
  - 否则，从链表中读取一个Node
  - 对该Node做Goal Test
    - 如果成功，则返回相应的solution
    - 否则继续扩展该Node的子节点，并将扩展的Node加入到列表中。