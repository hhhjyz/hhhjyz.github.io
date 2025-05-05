# Lec 10: NP-Completeness

Halmilton Problem、Clique Problem 、 TSP Problem、Vertex cover Problem都是NPC问题

The easist: 是$O(N)$级别的，因为我们必须读入输入至少一次

The hardest: undecided problems

## 1 Halting problem

对于任意一个程序，我们无法设计一个算法来判断它是否会在有限时间内停机（即判断程序是否会死循环）。

## 2 The class NP

确定型图灵机：根据唯一状态，下一步怎么走是确定的

非确定性图灵机： 下一步有多个选择

NP： Nondeterministic polynomial-time, 可以在多项式时间内验证给定解的正确与否

P类问题： 多项式时间复杂度内解决

$P \subseteq NP$ ture

$P \subset NP$ ?

NPC问题： 所有NP问题都可以在多项式时间内规约为NP问题，只要解决一个NPC问题，就可以解决所有NP问题

给定任意实例$\alpha \in Problem~A$,如果我们可以找到一个程序$R(\alpha)\rightarrow \belta \in Problem~B$,程序B是$O(N^{k1})$的，而且另一个程序$D(\belta)$可以在$O(N^{k2})$时间内得到答案，并且两个问题的答案相同，就称之为问题A规约（reduce）到问题B

## 3 A Formal-language Framework

抽象问题：抽象问题 Q 是一个关于集合 I 和集合 S 的一个二元关系。其中 I表示问题**实例**（Instance），SS表示问题的**解**（Solution）

所有的优化问题可以转化为判定问题（Yes or No），判定问题方便进行规约

**Encoding:**
计算机将问题转化为0,1串

### 3.1 Formal language Theory

一个判定问题可以表达为答案为1的所有问题的集合

给出一个新的实例，如果是集合中的一个元素，算法就会接受它，如果不是集合中的一个元素，算法就会拒绝它，就称之为算法能够解决我们的问题




co-NP问题，L和L的补都是NP问题