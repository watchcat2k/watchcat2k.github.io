---
layout: post
title: LeetCode练习题279. Perfect Squares——动态规划
date: 2018-12-09 00:00:00
categories: 
- Leetcode
tags: 
- LeetCode
- 动态规划
- C++
description: LeetCode上的关于动态规划的练习题
---


# 题目  {#question}
Given a positive integer n, find the least number of perfect square numbers (for example, 1, 4, 9, 16, ...) which sum to n.

Example 1:

```bash
Input: n = 12
Output: 3
Explanation: 12 = 4 + 4 + 4.
```

Example 2:

```bash
Input: n = 13 
Output: 2
Explanation: 13 = 4 + 9
```


# 分析  {#analyse}
这道题也是典型的动态规划问题。

划分子问题：求出构成数值 i 至少要多少个perfect square number，并把这个最小值存入 minResult[ i ]，其中 i 从 1 到 n。这样划分子问题的话，子问题的规模就是从小到大。

确定子问题的关系：求 minResult[ i ] 时，用变量 j 进行循环，j 的值为 1,2,3,4...，那么 j * j 就代表各个 square number，即1,4,9,16...，那么有子问题关系为 minResult[ i ] = minResult[ i - j * j ] + 1。

实现的一些细节：一开始声明 minResult 长度为 n + 1，值全为 INT_MAX，并初始化minResult[0] = 0，minResult[1] = 1，因为这两个值是很容易得知的，并且后面的计算要利用这两个值。求 minResult[ i ] 时，用变量 j 进行循环，当 j * j > i，就要退出循环，即找到了最小的 minResult[ i ] 。


# 代码  {#code}
```
class Solution {
public:
    int numSquares(int n) {
        vector<int> minResult(n + 1, INT_MAX);
        minResult[0] = 0;
        minResult[1] = 1;

        for (int i = 2; i <= n; i++) {  //求数字为i的最小完美正方数,从2开始
            for (int j = 1; j * j <= i; j++) {  //j为1,2,3,4... j * j 为1, 4, 9, 16...
                if (minResult[i - j * j] + 1 < minResult[i]) {
                    minResult[i] = minResult[i - j * j] + 1;
                }

            }
        }

        return minResult[n];
    }
};
```