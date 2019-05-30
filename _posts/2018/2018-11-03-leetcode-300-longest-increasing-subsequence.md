---
layout: post
title: LeetCode练习题300. Longest Increasing Subsequence——动态规划
date: 2018-11-03 00:00:00
categories: 
- Leetcode
tags: 
- LeetCode
- 动态规划
- C++
description: LeetCode上的关于动态规划的练习题
---



# 题目  {#question}
Given an unsorted array of integers, find the length of longest increasing subsequence.

**Example 1:**
```bash
Input: [10, 9, 2, 5, 3, 7, 101, 18] 
Output: 4 
Explanation: The longest increasing subsequence is [2, 3, 7, 101], therefore the length is 4.
```

**Note**
- There may be more than one LIS combination, it is only necessary for you to return the length.
- Your algorithm should run in O(n2) complexity.


# 分析  {#analyse}
这道题的意思是：在一个给定的数列中，找一个最长的递增子数列，递增子数列的元素可以不连续，然后返回该序列的长度。

我第一次看见这道题，觉得挺简单地，就直接动手做了。结果一测试，发现这道题并没有想象中的那么简单，还是有一些坑的，加上这道题也有一些值得拿来说的解题思想，所以就分析一下这道题。

我一开始是用两个for循环，第一个循环遍历数列的每一个元素 i ，第二个循环计算并找到以元素 i 为起点的递增序列的长度。但这样做会遇到不少问题，很难找到以元素 i 为起点的最长递增序列。因为，如果我们把求以元素 i 为起点的递增序列看作一个子问题，原数列长度为len，那么当我们计算所有以元素 i ( i = 0 : len - 1)为起点的递增序列时，子问题的规模呈现出len, len - 1, len - 2, ... 1的形态。也就是说计算所有以元素 i ( i = 0 : len - 1)为起点的递增序列长度，问题规模一开始会很大、很难计算，这样子是很难求出正确解的。

正确做法：

我们计算所有以元素 i ( i = 0 : len - 1)为终点的递增序列长度，这样的话，子问题的规模呈现出1, 2, ... , len - 1的形态，这样的子问题规模从小到达，从易到难，便能够成功找出最大的递增序列长度。

1. 若数列长度为0，返回0，否则，设变量result为所求的最大长度，初始化为1，并设数组sublen[ i ]保存着以元素 i 为终点的最长递增序列的长度，都初始化为1.
2. 循环遍历数列(i = 0 : len - 1)，遍历到元素 i 时，再按从 j = i - 1 到 j = 0 的序号递减循序遍历数列，若nums[j] < nums[i]，则subLen[i] = subLen[i] > subLen[j] + 1 ? subLen[i] : subLen[j] + 1，result = result > subLen[i] ? result : subLen[i]。
3. 返回最终结果result

# 代码  {#code}
```
int lengthOfLIS(vector<int>& nums) {
	int len = nums.size();
	if (len == 0) return 0;
	else if (len == 1) return 1;
	int subLen[1000] = {0};
	int result = 1; //若nums不为空，最终结果肯定至少为1，因为一个节点的最长递增字串长度为1
	for (int i = 0; i < len; i++) {
		subLen[i] = 1;
		for (int j = i - 1; j >= 0; j--) { //数列从右往左遍历，才能更好求解，从左往右便历不好求解
			if (nums[j] < nums[i]) {
				//遍历i节点时，用j遍历前面的节点，令j节点的最长递增字串长度sublen加1
				//找到这个值的最大，（加1即加上i节点本身）
				subLen[i] = subLen[i] > subLen[j] + 1 ? subLen[i] : subLen[j] + 1;
				//每次都更新result
				result = result > subLen[i] ? result : subLen[i];
			}
		}
	
	}
	return result;
}
```