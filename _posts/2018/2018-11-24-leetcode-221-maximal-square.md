---
layout: post
title: LeetCode练习题221. Maximal Square——动态规划
date: 2018-11-24 00:00:00
categories: 
- Leetcode
tags: 
- LeetCode
- 动态规划
description: LeetCode上的关于动态规划的练习题
---

# 题目  {#question}
Given a 2D binary matrix filled with 0's and 1's, find the largest square containing only 1's and return its area.

Example 1:

```bash
Input: 

1 0 1 0 0
1 0 1 1 1
1 1 1 1 1
1 0 0 1 0

Output: 4
```


# 分析  {#analyse}
这道题也是典型的动态规划问题。

动态规划问题的两个关键步骤：一个是子问题的划分，子问题的规模要从小到大；另一个是子问题之间的递推关系。还有，为了简化问题，我们在主要计算步骤都是求最大正方形的边长，最后才计算以该边长为正方形的面积。

1. 首先划分子问题：以循环变量 i，j 遍历输入的二维数组，子问题为求出以 matrix[ i ][ j ]元素为右下角的最大的正方形的边长，并把这个边长存在一个二维数组 result[ i ][ j ]中。
2. 子问题的关系：求以 matrix[ i ][ j ] 元素为右下角的最大的正方形时，先查看 matrix[ i ][ j ] 的值，若为0，则以该元素为右下角的最大的正方形的边长也为0；否则，要查看在输入的矩阵中与该元素相邻的左边、左上角和右边三个元素计算得到的最长边长，在这三个值中取得最小值 min，则以该元素为右下角的最大的正方形的边长为 min + 1。

**注意**：在计算时，对于输入矩阵的左边界和上边界的所有元素，计算方法有些不同。在矩阵的左边界和上边界的所有元素，若该元素的值为0，则以该元素为右下角的最大的正方形的边长也为0；否则以该元素为右下角的最大的正方形的边长为1。

最后，在 result[ i ][ j ] 中找到最大的边长，计算出面积返回即可


# 代码  {#code}
```
class Solution {
public:
   int minNum(int a, int b, int c) {
	int min = a;
	if (b < min) min = b;
	if (c < min) min = c;
	return min;
}

int maximalSquare(vector<vector<char>>& matrix) {
	int rowlen = matrix.size();
	if (rowlen == 0) {
		return 0;
	}
	int collen = matrix[0].size();
	int maxWidth[1000][1000];
	int result = 0;

	for (int i = 0; i < rowlen; i++) {
		for (int j = 0; j < collen; j++) {
			int tempValue = matrix[i][j] - 48;
			if (i == 0 || j == 0) {
				if (tempValue == 0) {
					maxWidth[i][j] = 0;
				}
				else {
					maxWidth[i][j] = 1;
				}
			}
			else {
				if (tempValue == 0) {
					maxWidth[i][j] = 0;
				}
				else {
					int min = minNum(maxWidth[i - 1][j], maxWidth[i][j - 1], maxWidth[i - 1][j - 1]);
					maxWidth[i][j] = min + 1;
				}
			}
			result = result > maxWidth[i][j] ? result : maxWidth[i][j];
		}
	}


	return result * result;
}
};
```