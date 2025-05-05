# Greedy Algorithm

**Optimization Problem:** 给定一组限制条件和优化函数，称满足条件的解为可行解，如果一个可行解在优化函数中取得最佳值，则称该解为最优解

**The Greedy Method:** 

- 根据预先设定的贪心标准，在每个阶段中做出最佳决策
- 当前做出的决策不允许在后面改变，所以确保做出每个决策都是可行的
- 只有当局部最优=全局最优时，贪心算法才是可行的
- 贪心算法不保证得到最优解

## 1 Activity Selection

!!! question 问题描述
    - 给定一组活动集$S = \{a_1, a_2, \dots, a_n\}$，这些活动均在一个房间开展，活动$a_i$的进行时间为$[s_i. f_i)$
    - 如果$a_i$和$a_j$满足$s_i \ge f_j$或$s_j \ge f_i$（即两个活动的时间不重叠），称这两个活动是**兼容的**(compatible)
    - 为了解题方便，预先将这些活动根据结束时间的先后排好序，即保证$f_1 \le f_2 \le \dots f_{n-1} \le f_n$
    
    问题：请你求出最大的、活动之间相互兼容的子集，即求在不发生冲突的前提下能安排的最多活动的方案

**几种贪心策略：**

- 挑选最早开始的活动（false
- 挑选时间最短的活动（false
- 挑选冲突最少的活动（false
- 挑选最早结束的活动（good


- 实现步骤：
    
    - 选择首先结束的活动，递归解决剩余的活动子集
    - 由于这是一个尾递归，因此可以用迭代方法替代
    - 时间复杂度：$O(N \log N)$（目前不清楚为什么）

- 得到正确的贪心算法后，我们回过头来修改DP的状态转移方程：
    
    $$ c_{1, j} = \begin{cases}1 & \text{if}\ j = 1 \ \max {c_{1, j - 1}, c_{1, k(j)} + 1} & \text{if}\ j > 1\end{cases} $$
    
    其中$c_{1, j}$是$a_1$到$a_j$之间的最优解，$a_{k(j)}$是离$a_j$最近的、兼容的、且先于$a_j$完成的活动

    时间复杂度$O(n^2)$
    
- 如果每个活动都有一个权重，那么状态转移方程为：
    
    $$ c_{1, j} = \begin{cases}w_j & \text{if}\ j = 1 \ \max {c_{1, j - 1}, c_{1, k(j)} + w_j} & \text{if}\ j > 1\end{cases} $$
    
    - 此时DP算法依然可行，但是贪心算法就行不通了

**区间调度问题：**

- 首先设置初始教室数量为1，将所有活动按照开始时间排序，然后从前往后遍历
- 每次选择一个活动时，看当前有无空闲教室，如果有就直接放进对应的教室，否则的话则新开一个教室

## 2 Schedule Problems

!!! problem 问题描述
    调度问题： 给定n个任务，每个任务i有一个完成所需时间$l_u$和权重$w_i$，这些任务只能被一次执行，不能并行
记$\sigma$为一种调度，任务i完成的时间$C_i(\sigma)+l_i$+(i之前的任务时长之和），题目要求最小化加权时间和，即求$T=min\sum_{i=1}^{n}w_iC_i(\sigma)$

这类问题很适合用贪心策略解决：将权重更大、时间更短的任务放在前面处理，这样得到的解应为最优解。

时间复杂度： $O(log n)$


## 3 Huffman Codes

![](assets/Lec%209/file-20241228111342254.png)

所有的字符位叶子节点上，而不会位于内部节点，

!!! code 代码实现
    ```
    void Huffman(PriorityQueue heap[], int C) {
        consider the C characters as C single node binary trees,
        and initialize them into a min heap;
    
        for (i = 1; i < C; i++) {
            create a new node;
            // be greedy here
            delete root from min heap and attach it to left_child of node;
            delete root from min heap and attach it to right_child of node;            
            weight of node = sum of weights of its children;
            // weight of a tree = sum of the frequencies of its leaves
            insert node into min heap;
        }
    }
    ```
    具体步骤为： 
    - 初始化：将每个字符作为一个二叉树的姐弟啊，并将它们放在一个最小堆内
    - 循环执行一下步骤：（共节点数-1次）
        - 取出频率最小的节点，作为新树的左孩子
        - 再取出频率次小的节点，作为新树的右孩子，新树的根节点是二节点频率之和，重新放回最小堆

时间复杂度：$T=O(ClogC)$

!!! note 小结论
    - 若字符个数为C,Huffman tree的节点个数为2C-1
    - 编码长度不超过C-1
