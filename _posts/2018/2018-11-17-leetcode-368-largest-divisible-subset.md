---
layout: post
title: LeetCode练习题368. Largest Divisible Subset——动态规划
date: 2018-11-17 00:00:00
categories: 
- Leetcode
tags: 
- LeetCode
- 动态规划
description: LeetCode上的关于动态规划的练习题
---

# 题目  {#question}
Given a set of distinct positive integers, find the largest subset such that every pair (Si, Sj) of elements in this subset satisfies:

Si % Sj = 0 or Sj % Si = 0.

If there are multiple solutions, return any subset is fine.

Example 1:

```bash
Input: [1,2,3]
Output: [1,2] (of course, [1,3] will also be ok)
```

Example 2:

```bash
Input: [1,2,4,8]
Output: [1,2,4,8]
```


# 分析  {#analyse}
这道题很明显应该用动态规划的方法来求解。

首先我们要将原问题划分为子问题，且子问题的规模是从小到大的。所以我们将原问题划分为以下子问题：首先将原来的输入数组 nums 按照从小到大的顺序排序，然后遍历数组 nums，每次遍历求出以 nums[i](i = 0~len-1) 为结尾的 Largest Divisible Subset，并把每次便历得到的结果存入 result[i](i = 0~len-1)。注意，如果每次遍历求出以 nums[i](i = 0~len-1) 为开头的 Largest Divisible Subset，子问题的规模是从大到小的，那么我们一开始就要求规模较大的子问题，这样子是求不出结果的，也是不符合动态规划的思想的。

然后我们要确定子问题之间的关系。当我们求以 nums[i](i = 0~len-1) 为结尾的 Largest Divisible Subset 时，我们要在 nums[i] 之前的数组部分找到符合原题关系的数组子集，即找到符合的 result[j](j = 0~i-1)，并选择一个数组长度最大的 result[j](j = 0~i-1)，与 nums[i] 合并为result[i]，成为以 nums[i](i = 0~len-1) 为结尾的 Largest Divisible Subset。这样不断进行下去，所有的以 nums[i](i = 0~len-1) 为结尾的 Largest Divisible Subset都找到了，并都存在result[i](i = 0~len-1)中。

最后，遍历result[i](i = 0~len-1)，找出一个数组长度最长的作为返回值，即最终结果。

# 示例  {#example}
以输入数组{2, 3, 4, 9, 8}为例：

首先排序得到{2, 3, 4, 8, 9}，然后开始遍历数组

以2为结尾的Largest Divisible Subset只有2本身，result[0] = {2}；

以3为结尾的Largest Divisible Subset只有3本身，result[1] = {3}；

以4为结尾的Largest Divisible Subset，4之前的数组部分有且只有一个符合的result[0]，result[2] = {2, 4}；

以8为结尾的Largest Divisible Subset，8之前的数组部分有符合的result[0]和result[2]，选择更大的result[2]，所以result[3] = {2, 4, 8}；

以9为结尾的Largest Divisible Subset，9之前的数组部分有且只有一个符合的result[1]，result[4] = {3, 9}；

最终返回最大的result[3]

# 代码  {#code}
```
class Solution {
public:
   bool isArrayCorrect(vector<int> arr, int num) {
        int i;
        for (i = 0; i < arr.size(); i++) {
            if (! (arr[i] % num == 0 || num % arr[i] == 0)) {
                break;
            }
        }
        if (i == arr.size()) {
            return true;
        }
        else {
            return false;
        }
    }

    vector<int> largestDivisibleSubset(vector<int>& nums) {
        int len = nums.size();

        if (len == 0) {
            return {};
        }

        int maxlen = 0;
        int maxPosition = -1;

        vector<vector<int>> result(len);

        sort(nums.begin(), nums.end());

        result[0].push_back(nums[0]);
        for (int i = 1; i < len; i++) {
            int subPosition = -1;
            int subMaxLen = 0;
            for (int j = 0; j < i; j++) {
                if (isArrayCorrect(result[j], nums[i])) {
                    if (result[j].size() > subMaxLen) {
                        subMaxLen = result[j].size();
                        subPosition = j;
                    }
                }
            }
            if (subPosition == -1) {
                result[i].clear();
                result[i].push_back(nums[i]);
            }
            else {
                result[i].clear();
                result[i].assign(result[subPosition].begin(), result[subPosition].end());
                result[i].push_back(nums[i]);
            }

        }
 
        for (int i = 0; i < len; i++) {
            if (result[i].size() > maxlen) {
                maxPosition = i;
                maxlen = result[i].size();
            }
        }
        return result[maxPosition];

    }
};
```