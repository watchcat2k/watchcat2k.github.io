---
layout: post
title: LeetCode练习题435. Non-overlapping Intervals——贪心算法
date: 2018-10-28 00:00:00
categories: 
- Leetcode
tags: 
- LeetCode
- 贪心算法
description: LeetCode上的关于贪心算法的练习题
---

# 题目  {#question}
Given a collection of intervals, find the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping.

**Note**
- You may assume the interval's end point is always bigger than its start point.
- Intervals like [1,2] and [2,3] have borders "touching" but they don't overlap each other.

**Example 1:**
```bash
Input: [ [1,2], [2,3], [3,4], [1,3] ]

Output: 1

Explanation: [1,3] can be removed and the rest of intervals are non-overlapping.
```

**Example 2:**
```bash
Input: [ [1,2], [1,2], [1,2] ]

Output: 2

Explanation: You need to remove two [1,2] to make the rest of intervals non-overlapping.
```

**Example 3:**
```bash
Input: [ [1,2], [2,3] ]

Output: 0

Explanation: You don't need to remove any of the intervals since they're already non-overlapping.
```

# 分析  {#analyse}
原题是让我们去除有重叠的区间，以使得剩下的区间之间不存在重叠。问最少需要去除多少个这样的区间。

这道题是典型的用贪心算法来求解的问题，而难点在于如何找到一个正确的贪心算法。

我一开始是用以下贪心算法求解的：每遍历一次所有的区间，就找出这样一个区间，与该区间重叠的区间数量最多，然后去除该区间，一直循环遍历重复之前步骤直到剩余区间没有重叠的。最后按照该方法计算得到的结果并不正确，所以要凭空找到正确的贪心算法并不容易。

正确做法如下：

设想这样的情景：我们要完成一些任务，这些任务都有它自己的完成所需的时间区间，完成任务所需的时间区间有可能重叠，然而我们在一个时间区间内只能执行一个任务。所以为了尽可能的完成更多的任务，我们要选择执行结束时间更早的任务，这样在一定的时间区间内，我们才能完成更多的任务。

这道题也类似，我们要选择结束时间更早的区间，并且这些区间没有冲突，然后剩余没有被选择的区间就是要被去除的。

通过这道题我们发现，要解决贪心问题，关键在于找到一个合适并且正确的贪心算法，然而有时候查找这样的贪心算法并不容易，这需要我们经过大量的练习积累经验，这样才能更好地应对这类贪心问题。

# 代码  {#code}
```
/**
 * Definition for an interval.
 * struct Interval {
 *     int start;
 *     int end;
 *     Interval() : start(0), end(0) {}
 *     Interval(int s, int e) : start(s), end(e) {}
 * };
 */
class Solution {
public:
    static bool mySortFun(Interval a, Interval b) {
        if (a.end != b.end) {
            return a.end < b.end;
        }
        else {
            return a.start < b.start;
        }
    }
    //这是一个贪心问题，我们每次都找到那个结束点最小的区间，
    //然后依次向后找那些与前面区间不冲突且结束点早的区间。
    //这个过程中我们把局部的最优解合并成了全局的最优解
    int eraseOverlapIntervals(vector<Interval>& intervals) {
        if (intervals.size() <= 1) return 0;
        int head;
        int result = 0;

        sort(intervals.begin(), intervals.end(), mySortFun);

        head = intervals.front().end;
        for (int i = 1; i < intervals.size(); i++) {
            if (intervals[i].start < head) {
                result++;
            }
            else {
                head = intervals[i].end;
            }
        }
        return result;
    }
};
```