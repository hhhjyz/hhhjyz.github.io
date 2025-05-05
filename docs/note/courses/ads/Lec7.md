# Lec 7: Divide and Conquer

## 1 Introduction

分治法(Divide-and-conquer algorithm)属于一种算法范型，它的基本思想是将一个问题分解为若干个规模较小的相同问题，然后递归地解决这些子问题，最后将这些子问题的解合并得到原问题的解，一个比较经典的案例就是归并排序和快速排序。


!!! example "Closest Points Problem"
    二维最近点问题(Closest Points Problem)，指的是给定平面上的 n 个点，找出其中距离最近的两个点。

    该分治算法的时间复杂度计算递推公式：
    $$T(N=2T(\frac N2)+O(N)=O(N\mathrm{log}N)$$

