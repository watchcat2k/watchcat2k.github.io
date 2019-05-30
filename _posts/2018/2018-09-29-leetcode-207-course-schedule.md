---
layout: post
title: LeetCode练习题207. Course Schedule——图搜索
date: 2018-09-29 00:00:00
categories: 
- Leetcode
tags: 
- LeetCode
- 图搜索
- C++
description: LeetCode上的关于图搜索的练习题
---


# 题目  {#question}
There are a total of n courses you have to take, labeled from 0 to n-1.

Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: [0,1]

Given the total number of courses and a list of prerequisite pairs, is it possible for you to finish all courses?

Example 1:

```bash
Input: 2, [[1,0]]
Output: true
Explanation: There are a total of 2 courses to take.   To take course 1 you should have finished course 0. So it is possible.
```

Example 2:

```bash
Input: 2, [[1,0],[0,1]]
Output: false
Explanation: There are a total of 2 courses to take.   To take course 1 you should have finished course 0, and to take course 0 you should   also have finished course 1. So it is impossible.
```


# 分析  {#analyse}
这道题的实质是检测一个给出的有向图是否有环的问题，若有向图有环，则返回false，否则返回true

我们用染色法来判断一个有向图是否有环：

首先，给所有的节点的颜色color赋给一个初始值0。color的值有三种，1、0、-1分别代表三种不同的颜色，1代表以该点为起点不存在环，0代表未上色，-1代表以该点为起点有环。

然后，遍历所有的点，以每个点为起点进行深度遍历dfs。在深度遍历时，若当前节点未染色，则将它的颜色设为 -1；若当前节点染颜色 -1，说明该店处在一条环路上，手动终止深度遍历并返回false；若当前节点染色 1，说明当前节点不处在环路上，手动终止深度遍历并返回true；若深度遍历能全部进行不被手动终止，说明在深度遍历的路径上的所有节点都不处在环路上，修改它们的颜色为1。

最终，查看所有节点的颜色，若有存在节点颜色为-1，则最终结果为false，否则返回true。


# 代码  {#code}
```
class Solution {
public:
    //color 1代表无环安全，0代表未上色，-1代表有环不安全
    bool dfs(int numCourses, vector<pair<int, int>>& prerequisites, int start, vector<int> & color) {
        if (color[start] == 0) {
            color[start] = -1;
        }
        else if (color[start] == -1) {
            return false;
        }
        else if (color[start] == 1) {
            return true;
        }

        int len = prerequisites.size();
        for (int i = 0; i < len; i++) {
            if (prerequisites[i].first == start) {
                if (dfs(numCourses, prerequisites, prerequisites[i].second, color) == false) {
                    return false;
                }
            }
        }

        color[start] = 1;
        return true;
    }

    bool canFinish(int numCourses, vector<pair<int, int>>& prerequisites) {
        const int MAX_NUM = 5000;
        vector<int> color(MAX_NUM);
        for (int i = 0; i < MAX_NUM; i++) {
            color[i] = 0;
        }

        for (int i = 0; i < numCourses; i++) {
            if (color[i] == 0) {
                if (dfs(numCourses, prerequisites, i, color) == false) {
                    return false;
                }
            }
        }

        for (int i = 0; i < numCourses; i++) {
            if (color[i] != 1) {
                return false;
            }
        }

        return true;
    }
};
```