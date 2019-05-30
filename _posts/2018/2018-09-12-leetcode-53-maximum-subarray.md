---
layout: post
title: LeetCode练习题53. Maximum Subarray——数组
date: 2018-09-12 00:00:00
categories: 
- Leetcode
tags: 
- LeetCode
- 数组
description: LeetCode上的关于数组的练习题
---

# 题目
Given an integer array nums, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

**Example:**
```bash
Input: [-2,1,-3,4,-1,2,1,-5,4],
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
```

**Follow up:**

If you have figured out the O(n) solution, try coding another solution using the divide and conquer approach, which is more subtle.


# 分析
这个问题可以直接用O(n)复杂度算法来求解。算法如下：
1. 步骤一，创建变量result和变量max，都赋值为nums[0]，创建循环变量i，赋值为0。
2. 步骤二，如果max+nums[i]的值大于nums[i]的值，变量max赋值为max+nums[i]，否则赋值为nums[i]。如果result的值大于max的值，变量result保持为原result的值，否则赋值为max的值。
3. 步骤三，i 赋值为i+1，如果 i 的值等于nums.size()，则返回result的值并终止程序，否则执行步骤二。

# 代码
```
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if (nums.size() == 0) return 0;

        int result = nums[0];
        int max = nums[0];
        for (int i = 1; i < nums.size(); i++) {
            max = max + nums[i] > nums[i] ? max + nums[i] : nums[i];
            result = result > max ? result : max;
        }
        return result;
    }
};
```