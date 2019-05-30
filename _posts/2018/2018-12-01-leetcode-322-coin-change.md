---
layout: post
title: LeetCode练习题322. Coin Change——动态规划
date: 2018-12-01 00:00:00
categories: 
- Leetcode
tags: 
- LeetCode
- 动态规划
description: LeetCode上的关于动态规划的练习题
---


# 题目  {#question}
You are given coins of different denominations and a total amount of money amount. Write a function to compute the fewest number of coins that you need to make up that amount. If that amount of money cannot be made up by any combination of the coins, return -1.

Example 1:

```bash
Input: coins = [1, 2, 5], amount = 11
Output: 3
Explanation: 11 = 5 + 5 + 1
```

Example 2:

```bash
Input: coins = [2], amount = 3
Output: -1
```


# 分析  {#analyse}
这道题也是典型的动态规划问题。

利用动态规划解决问题的两个关键核心分别是：第一点是将原问题划分为子问题，子问题规模从小到大；第二点是找出子问题之间的关系，即递推公式。然而划分子问题和找子问题之间的关系有时候并不是这么容易。比如这道题，我一开始也思考了许多种可能的子问题划分方法，但是都找不到一个合适的能够找到递推关系的划分方法。后来是查阅了一些资料才明白如何划分。

子问题划分：找出构成面值为 i_amount 所需的最少硬币数，其中 i_amount 取值为 1 ~ amount。

子问题递推式：令 allResult[ i_amount ] 代表构成面值为 i_amount 所需的最小的硬币数，所以用变量 j 遍历所有面值的硬币 coins[ j ]，找到最小的 allResult[ i_amount - coins[ j ] ] + 1，令 allResult[ i_amount ] = allResult[ i_amount - coins[ j ] ] + 1。

用上述方法计算时有些具体要注意的地方：
1. 要令 allResult 初始化为长度为 amount + 1，值全为 INT_MAX的数组，且令 allResult[ 0 ] = 0
2. 然后用变量 i_amount 在外层循环，i_amount 值从 1 到 amount。
3. 在内层用变量 j 循环所有的 coins[ j ]，如果 i_amount - coins[j] >= 0 && allResult[i_amount - coins[j]] != INT_MAX 成立，才去找最小的 allResult[ i_amount - coins[ j ] ] + 1 取代allResult[ i_amount ] 。



# 代码  {#code}
```
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        int coinsLen = coins.size();

        //sort(coins.begin(), coins.end(), compare);
        sort(coins.begin(), coins.end());

        vector<int> allResult(amount + 1, INT_MAX); //初始化amount + 1个
        allResult[0] = 0;  //第0个初始化为0, 第i个代表综合为i需要的最小硬币数
        for (int i_amount = 1; i_amount <= amount; i_amount++) {
            for (int j = 0; j < coinsLen; j++) {
                if (i_amount - coins[j] >= 0 && allResult[i_amount - coins[j]] != INT_MAX) {
                    allResult[i_amount] = allResult[i_amount - coins[j]] + 1 < allResult[i_amount] ?
                        allResult[i_amount - coins[j]] + 1 : allResult[i_amount];
                }

            }
        }
        if (allResult[amount] == INT_MAX) {
            return -1;
        }
        else {
            return allResult[amount];
        }
    }
};
```