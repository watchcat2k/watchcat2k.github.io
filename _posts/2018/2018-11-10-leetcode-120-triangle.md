---
layout: post
title: LeetCode练习题120. Triangle——动态规划
date: 2018-11-10 00:00:00
categories: 
- Leetcode
tags: 
- LeetCode
- 动态规划
- C++
description: LeetCode上的关于动态规划的练习题
---


# 题目  {#question}
Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.

For example, given the following triangle

```bash
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]

```

The minimum path sum from top to bottom is 11 (i.e., 2 + 3 + 5 + 1 = 11).

**Note**
- Bonus point if you are able to do this using only O(n) extra space, where n is the total number of rows in the triangle.


# 分析  {#analyse}
动态规划方法是正确的解决办法。

利用动态规划的方法计算，按照题目的三角形数据，计算过程如下：

生命一个int类型数组all_dist，对于原来的三角形，遍历到第二层时，有 2+3=5和2+4=6，我们要把遍历到当前层各节点所经历的最小权重和保存到all_dist中，所以all_dist保存当前层的计算结果[5,6]。

接下来，当前层为[6,5,7]。对于当前行中的每一个元素，计算当前元素权重与上一行中相邻的2个元素权重和的最小值，然后把这个最小值更新到当前元素对应all_dist数组的位置。不断进行下去，直到最后一行为止。


# 代码  {#code}
```
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        int row = triangle.size();
        vector<int> all_dist(1000, 0);
        int result = 0;

        all_dist[0] = triangle[0][0];

        for (int i = 1; i < row; i++) {
            vector<int> temp_all_dist(all_dist);
            for (int j = 1; j < i; j++) {
                all_dist[j] = temp_all_dist[j] + triangle[i][j] < temp_all_dist[j - 1] + triangle[i][j] ?
                    temp_all_dist[j] + triangle[i][j] : temp_all_dist[j - 1] + triangle[i][j];
            }
            all_dist[0] = temp_all_dist[0] + triangle[i][0];
            all_dist[i] = temp_all_dist[i - 1] + triangle[i][i];
        }

        result = all_dist[0];
        for (int i = 1; i < row; i++) {
            result = result < all_dist[i] ? result : all_dist[i];
        }
        return result;
    }
};
```