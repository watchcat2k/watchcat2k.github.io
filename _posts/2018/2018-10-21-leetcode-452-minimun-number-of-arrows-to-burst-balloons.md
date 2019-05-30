---
layout: post
title: LeetCode练习题452. Minimum Number of Arrows to Burst Balloons——贪心算法
date: 2018-10-21 00:00:00
categories: 
- Leetcode
tags: 
- LeetCode
- 贪心算法
description: LeetCode上的关于贪心算法的练习题
---



# 题目  {#question}
There are a number of spherical balloons spread in two-dimensional space. For each balloon, provided input is the start and end coordinates of the horizontal diameter. Since it's horizontal, y-coordinates don't matter and hence the x-coordinates of start and end of the diameter suffice. Start is always smaller than end. There will be at most 104 balloons.

An arrow can be shot up exactly vertically from different points along the x-axis. A balloon with xstart and xend bursts by an arrow shot at x if xstart ≤ x ≤ xend. There is no limit to the number of arrows that can be shot. An arrow once shot keeps travelling up infinitely. The problem is to find the minimum number of arrows that must be shot to burst all balloons.

**Example 1:**
```bash
Input:
[[10,16], [2,8], [1,6], [7,12]]

Output:
2

Explanation:
One way is to shoot one arrow for example at x = 6 (bursting the balloons [2,8] and [1,6]) and another arrow at x = 11 (bursting the other two balloons).
```


# 分析  {#analyse}
这道题的实质是贪心算法中的区间重叠问题。

题目大意是：在x轴上给出几个不同的区间，如[[10,16], [2,8], [1,6], [7,12]]，然后在x轴上找一些点，使得这些点尽可能地“穿过”这些区间，即点被包含在区间内。问至少需要多少个点才能“穿过”所有的区间？

做法如下：
1. 首先对这些区间按照左端点从小到大的顺序进行排序，若左端点相同，则按照右端点从小到大的顺序排序。排序后的区间数组为points。如[[10,16], [2,8], [1,6], [7,12]]被排序为[[1,6], [2,8], [7,12], [10,16]]。
2. 设我们要找的点的位置为pos，初始化为排序后的第一个区间的右端点的值，即points[0][1]。并将至少需要的点的个数result初始化为1，因为现在我们正在第一个找符合的点，该点位于位置pos，位置pos需要在后面确定。
3. 用循环变量 i 从排序后的第二个区间开始循环遍历，若points[i][0] <= pos，则pos = points[i][1] < pos ? points[i][1] : pos（这是为了更新符合的点的位置pos，使得该点能“穿过”已经遍历过的区间），否则result++，pos = points[i][1]，（因为前一个符合要求的点已经不能再穿过当前和往后的区间了，所以需要的点的个数result增加1，并将新的点的位置pos设置为当前区间的右端点值。）
4. 最终得到result值为所求，返回result。


# 代码  {#code}
```
class Solution {
public:
    int partition(vector<pair<int, int>>& points, int start, int end) {
        if (start < end) {
            pair<int, int> pivot = points[end];
            int i = start;
            pair<int, int> temp;
            for (int j = start; j < end; j++) {
                if (points[j].first < pivot.first || (points[j].first == pivot.first && points[j].second < pivot.second)) {
                    temp.first = points[j].first;
                    temp.second = points[j].second;
                    points[j].first = points[i].first;
                    points[j].second = points[i].second;
                    points[i].first = temp.first;
                    points[i].second = temp.second;
                    i++;
                }
            }
            temp.first = points[end].first;
            temp.second = points[end].second;
            points[end].first = points[i].first;
            points[end].second = points[i].second;
            points[i].first = temp.first;
            points[i].second = temp.second;
            return i;
        }
        return -1;
    }

    void myQuickSort(vector<pair<int, int>>& points, int start, int end) {
        if (start < end) {
            int mid = partition(points, start, end);
            myQuickSort(points, start, mid - 1);
            myQuickSort(points, mid + 1, end);
        }
    }

    int findMinArrowShots(vector<pair<int, int>>& points) {
        int len = points.size();
        if (len == 0) return 0;
        else if (len == 1) return 1;

        myQuickSort(points, 0, len - 1);

        int result = 0;
        int pos = points[0].second;

        result++;
        for (int i = 1; i < len; i++) {
            if (points[i].first <= pos) {
                if (points[i].second < pos) {
                    pos = points[i].second;
                }
            }
            else {
                result++;
                pos = points[i].second;
            }
        }


        return result;
    }

};
```