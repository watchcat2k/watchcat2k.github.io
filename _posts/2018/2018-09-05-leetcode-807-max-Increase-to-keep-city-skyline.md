---
layout: post
title: LeetCode练习题807. Max Increase to Keep City Skyline——数组
date: 2018-09-05 00:00:00
categories: 
- Leetcode
tags: 
- LeetCode
- 数组
- C++
description: LeetCode上的关于数组的练习题
---




# 题目  {#question}
In a 2 dimensional array grid, each value grid[i][j] represents the height of a building located there. We are allowed to increase the height of any number of buildings, by any amount (the amounts can be different for different buildings). Height 0 is considered to be a building as well. 

At the end, the "skyline" when viewed from all four directions of the grid, i.e. top, bottom, left, and right, must be the same as the skyline of the original grid. A city's skyline is the outer contour of the rectangles formed by all the buildings when viewed from a distance. See the following example.

What is the maximum total sum that the height of the buildings can be increased?

```bash
Example:
Input: grid = [[3,0,8,4],[2,4,5,7],[9,2,6,3],[0,3,1,0]]
Output: 35
Explanation: 
The grid is:
[ [3, 0, 8, 4], 
[2, 4, 5, 7],
[9, 2, 6, 3],
[0, 3, 1, 0] ]

The skyline viewed from top or bottom is: [9, 4, 8, 7]
The skyline viewed from left or right is: [8, 7, 9, 3]

The grid after increasing the height of buildings without affecting skylines is:

gridNew = [ [8, 4, 8, 7],
            [7, 4, 7, 7],
            [9, 4, 8, 7],
            [3, 3, 3, 3] ]
```


# 分析  {#analyse}
题目大意是要我们尽可能地增加每一栋建筑物的高度，增加高度的同时，从水平方向和垂直方向上观察整个建筑群，得到的观察结果仍与观察原始建筑群得到的结果相同。

题目用一个二维数组来表示每栋建筑物的高度，我们先循环扫描一次二维数组`grid`，得到每一行和每一列的最大高度，分别存储在`maxHeightOfHorizen[i]` 和 `maxHeightOfVertical[i]` ，然后进行第二次循环扫描二维数组`grid`，假设某栋建筑物处在第` i `行第` j `列，若要增加高度的同时保持观察结果的一致性，则最大能到达的高度应该为`maxHeightOfHorizen[i]`和`maxHeightOfVertical[j]`中的最小值。

# 代码  {#code}
```
class Solution {
public:
    int maxIncreaseKeepingSkyline(vector<vector<int>>& grid) {
        int len = grid.size();
        int maxHeightOfVertical[50] = { 0 }, maxHeightOfHorizen[50] = { 0 };
        int temp = 0;
        int result = 0;

        //扫描
        for (int i = 0; i < len; i++) {
            maxHeightOfHorizen[i] = grid[i][0];
            maxHeightOfVertical[i] = grid[0][i];

            for (int j = 0; j < len; j++) {
                if (grid[i][j] > maxHeightOfHorizen[i]) {
                    maxHeightOfHorizen[i] = grid[i][j];
                }
                if (grid[j][i] > maxHeightOfVertical[i]) {
                    maxHeightOfVertical[i] = grid[j][i];
                }
            }
        }

        //增加高度
        for (int i = 0; i < len; i++) {
            for (int j = 0; j < len; j++) {
                temp = maxHeightOfHorizen[i] < maxHeightOfVertical[j] ? maxHeightOfHorizen[i] : maxHeightOfVertical[j];
                result += temp - grid[i][j];
            }
        }

        return result;
    }
};
```