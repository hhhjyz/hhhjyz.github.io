# Lec8 : Dynamic Programming

## 1 Climbing Stairs Problem

!!! question 问题描述
    楼底和楼顶之间有 n 段台阶，每步可以上 1 个或 2 个台阶，请问有多少种爬到楼顶的方法。

- 假如我们现在站在第i个台阶上，因为每步可以上1个或2个台阶，所以在上一步我们应该站在第i-1或第i-2个台阶上
- 令爬到第i个台阶的方案树为$F_i$，那么很自然地，我们得到了一个递推关系式：$F_i = F_{i - 1} + F_{i - 2}$

- 根据递推关系，要得到 FNFN​，只需知道 FN−1FN−1​ 和 FN−2FN−2​ 的值

时间复杂度： $T(N)=O(N)$

## 2 Ordering Matrix Multiplications

假设我们计算$n$个矩阵的乘法$\mathbf{M}_1 \cdot \dots \cdot \mathbf{M}_n$，其中$\mathbf{M}_i$是一个规模为$r_{i-1} \times r_i$的矩阵。令计算矩阵乘法$\mathbf{M}_i \cdot \dots \cdot \mathbf{M}_j$的最优成本为$m_{ij}$，那么我们可以得到以下递推关系式：

mij={0if i=j mini≤l<jmil+ml+1 j+ri−1rlrjif j>i

- 我们需要计算的$m_{ij}$有$O(N^2)$个（$i, j$的范围在$[1, n]$之间）
- 根据递推关系式，某个子问题的解一定来自比该子问题规模更小的子问题的解中。因此为了节省计算次数，我们还是**自底向上**计算：先算最小规模（即$i = j$时）的$m_{ij}$，再计算更大规模的$m_{ij}$，这样规模较大的子问题可以利用规模较小的子问题的解直接计算，而无需重复计算更小规模的子问题

时间复杂度： $T(N)=O(N^3)$

## 3 Optimal Binary Search Trees

**最优二叉查找树**(optimal binary search trees, OBST)：一种在二叉查找树上完成静态查找(static search)（即没有插入和删除操作）操作的最优方法。

!!! question  问题描述
    给定$N$个单词，满足词典序$w_1 < w_2 < \dots < w_N$，且每个词$w_i$出现的概率（以下称为“词频”）为$p_i$。现在要求将这些词排列在一个二叉查找树上，使得所有单词的预期查找时间（$T(N) = \sum\limits_{i=1}^N p_i \cdot (1 + d_i)$，其中$d_i$为$w_i$在树中的深度）尽可能小，即构造一棵OBST。

构造OBST的解法与计算矩阵乘法的最优顺序类似。先令：

- Tij：由单词$w_i \dots w_j(i < j)$构成的OBST
- cij：$T_{ij}$的成本（$c_{ii} = p_i$，PPT上的是错的）
- rij：$T_{ij}$的根节点
- wij：$T_{ij}$的权重，等于$\sum\limits_{k = i}^j p_k$（$w_{ii} = p_i$）

若$T_{ij}$是最优的，那么需要满足$c_{ij} = w_{ij} + \min\limits_{i < l \le j}{c_{i, l - 1} + c_{l + 1, j}}$

时间复杂度：$T(N)=O(N^3)$

## 4 Floyd Shortest Path Algorithm

Floyd算法的大致思路：从$D^{-1}$开始，连续得到$D^0, D^1, \dots, D^{N-1}$。如果已经解决了$D^{k-1}$，则此时有两种可能的情况：

- 第$k$个节点并不在最短路内，即$D^k = D^{k - 1}$
- 第$k$个节点在最短路内，那么满足$D^k[i][j] = D^{k-1}[i][k] + D^{k-1}[k][j]$

因此有递推关系：$D^k[i][j] = \min{D^{k-1}[i][j], D^{k-1}[i][k] + D^{k-1}[k][j]}, k \ge 0$

??? code "代码实现"
    ```cpp
    // A[] contains the adjacency matric with A[i][i] = 0
    // D[] contains the values of the shortest path
    // N is the number of vertices
    // A negative cycle exists iff D[i][i] < 0
    void AllPairs(TwoDimArray A, TwoDimArray D, int N) {
        int i, j, k;
        for (i = 0; i < N; i++)    // initialize D
            for (j = 0; j < N; j++)
                D[i][j] = A[i][j];
        
        for (k = 0; k < N; k++)      // add the kth vertex into the path
            for (i = 0; i < N; i++)
                for (j = 0; j < N; j++)
                    if (D[i][k] + D[k][j] < D[i][j])    // update shortest path
                        D[i][j] = D[i][k] + D[k][j];
    }
    ```

时间复杂度： $T(N)=O(N^3)$

## 5 Product Assembly

$$
\begin{align} f[line][stage] & = \min\{f[line][stage - 1] + t_{process}[line][stage - 1] \notag  , f[1 - line][stage - 1] + t_{transit}[1 - line][stage - 1]\} \notag \end{align}
$$

其中$f[line][stage]$表示在_stage_阶段时，在第_line_条组装线上的最优时间，$t_{process}[line][stage]$表示在同一条组装线上进入_stage_阶段所需的时间，$t_{process}[line][stage]$表示从不同组装线上进入_stage_阶段所需的时间。

1. 确定好计算的顺序。这里直接看代码：
    
    !!! code "代码实现"
        ```cpp
        // Initialization
        f[0][0] = 0; 
        f[1][0] = 0;
        // Outer loop: start from the first stage, end with the last stage
        for (stage = 1; stage <= n; stage++) {
            // Inner loop: test each line and find the minimum path
            for (line = 0; line <= 1; line++) {
                f[line][stage] = min(f[line][stage - 1] + t_process[line][stage - 1], f[1 - line][stage - 1] + t_transit[1 - line][stage - 1]);
            }
        }
        // The solution comes from the last stage of two lines
        Solution = min(f[0][n], f[1][n]);
        ```
## 6 Knapsack Problem

### 6.1 0-1背包问题

解题步骤：

1. 定义状态
    
    - 观察发现，放入物品会改变背包内物品的总价值和背包容量，因此需要记录的量为**前$i$个物品在容量为$c$的背包中的最大价值**，用符号化的语言表示为$dp[i, c]$，那么要求的就是$dp[n, cap]$
    - 用一张$(n + 1) \times (cap + 1)$的表来记录状态
2. 推导状态转移方程
    
    - 当我们对物品$i$做出决策时，需要考虑两种情况
        
        - 不放入物品：$dp[i, c] = dp[i - 1, c]$，背包容量和价值均不变
        - 放入物品：$dp[i, c] = dp[i - 1, c - w_{i - 1}] + v_{i - 1}$，背包容量减少，价值增加
    - 因此完整的状态转移方程为：
        
        $$ dp[i, c] = \max{dp[i - 1, c], dp[i - 1, c - w_{i - 1}] + v_{i - 1}} $$
        
3. 确定计算顺序
    
    - 首先确定一下边界条件：当无物品或背包容量为0时最大价值为0，即状态表首列$dp[i, 0]$和首行$dp[0, c]$均等于0
    - 根据状态方程知，当前状态是根据它上方和左上方的状态推出来的，因此只需正向循环遍历这张状态表即可

下面展示对应的代码实现：

??? code "代码实现"


    ```cpp
    int knapsack_01(int weight[], int val[], int n, int cap) {
        int i, j;
        int dp[MAXN][MAXCAP];

        for (i = 0; i <= n; i++) 
            for (j = 0; j <= cap; j++)
                dp[i][j] = 0;

        for (i = 1; i <= n; i++) {
            for (j = 1; j <= cap; j++) {
                if (weight[i - 1] > c) {
                    dp[i][j] = dp[i - 1][j];
                } else {
                    dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight[i - 1]] + val[i - 1]);
                }
            }
        }

        return dp[n][cap];
    }
    ```


- 时间复杂度：$O(n \cdot cap)$
- 空间复杂度：$O(n \cdot cap)$

事实上，这种算法在空间上还可以再优化（$O(n \cdot cap) \rightarrow O(n)$）：

- 观察发现，第i行状态表的状态来自于第i-1行状态表的状态；且遍历第i行时第i-1行的状态已经算好了，因此我们尝试将状态表从二维降到**一维**
- 这时就不能正向遍历了，因为在原来的状态表中，当前状态依赖于它上面的和左上角的状态；如果压缩成一维，左上角的状态就被压到了左边，而左边的状态即上一个状态已经更新过了，所以正向遍历就失效了，因此采取**倒序遍历**的策略

??? code "代码实现"

    ```cpp
    int knapsack_01(int weight[], int val[], int n, int cap) {
        int j;
        int dp[MAXN][MAXCAP];

        for (j = 0; j <= cap; j++)
            dp[j] = 0;

        for (i = 1; i <= n; i++) {
            for (j = cap; j >= 1; j--) {
                if (weight[i - 1] <= c) {
                    dp[j] = max(dp[j], dp[c - weight[i - 1]] + val[i - 1]);
                }
            }
        }
        

        return dp[cap];
    }
    ```

#### 6.1.1 Unbounded Knapsack Problem

[](https://github.com/NoughtQ/notebook/blob/master/docs/algorithms/ads/8.md#unbounded-knapsack-problem)

完全背包问题则解放了0-1背包的限制：**每个物品可以重复选取（无数次）**。

- 解题思路与0-1背包问题类似，唯一的区别在于状态转移方程中，**选取物品的时候不需要改变i值**，因为物体可以重复选取
- 对于空间压缩，由于当前状态依赖于上面和左边的状态，因此将数组压缩成一维后，正向遍历就能正确解决问题

??? code "代码实现"

    ```cpp
    // 与上面代码的唯一区别在于内部循环的遍历顺序发生改变
    int knapsack_ub(int weight[], int val[], int n, int cap) {
        int j;
        int dp[MAXN][MAXCAP];

        for (j = 0; j <= cap; j++)
            dp[j] = 0;

        for (i = 1; i <= n; i++) {
            for (j = 1; j <= 1; j++) {
                if (weight[i - 1] <= c) {
                    dp[j] = max(dp[j], dp[c - weight[i - 1]] + val[i - 1]);
                }
            }
        }
        
        return dp[cap];
    }
    ``` 


#### 6.1.2 Bounded Knapsack Problem

[](https://github.com/NoughtQ/notebook/blob/master/docs/algorithms/ads/8.md#bounded-knapsack-problem)

多重背包问题有了更复杂的限制：**物品$i$最多只能选取$k_i$次**。

- 此时状态转移方程为：
    
    $$ dp[i, c] = \max\limits_{0 \le k \le k_i}{dp[i - 1, c], dp[i - 1, c - w_{i - 1} \cdot k] + v_{i - 1} \cdot k} $$
    
    也就是说，对于每种物品，我们需要加一层遍历来决定选取多少的这一类的物品，因此时间复杂度就提升至$O(n \cdot cap \cdot \max{k_i})$，但空间复杂度没有改变
    
- 事实上该算法还可以进一步优化，甚至可以将时间复杂度降到线性