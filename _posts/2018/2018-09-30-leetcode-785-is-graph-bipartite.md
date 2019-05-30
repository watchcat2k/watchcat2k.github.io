---
layout: post
title: LeetCode练习题785. Is Graph Bipartite?——图搜索
date: 2018-09-30 00:00:00
categories: 
- Leetcode
tags: 
- LeetCode
- 图搜索
description: LeetCode上的关于图搜索的练习题
---


# 题目  {#question}
Given an undirected graph, return true if and only if it is bipartite.

Recall that a graph is bipartite if we can split it's set of nodes into two independent subsets A and B such that every edge in the graph has one node in A and another node in B.

The graph is given in the following form: graph[i] is a list of indexes j for which the edge between nodes i and j exists.  Each node is an integer between 0 and graph.length - 1.  There are no self edges or parallel edges: graph[i] does not contain i, and it doesn't contain any element twice.

**Example 1:**
```bash
Input: [[1,3], [0,2], [1,3], [0,2]]
Output: true
Explanation: 
The graph looks like this:
0----1
|    |
|    |
3----2
We can divide the vertices into two groups: {0, 2} and {1, 3}.
```

**Example 2:**
```bash
Input: [[1,2,3], [0,2], [0,1,3], [0,2]]
Output: false
Explanation: 
The graph looks like this:
0----1
| \  |
|  \ |
3----2
We cannot find a way to divide the set of nodes into two independent subsets.
```

**Note:**

- graph will have length in range [1, 100].
- graph[i] will contain integers in range [0, graph.length - 1].
- graph[i] will not contain i or duplicate values.
- The graph is undirected: if any element j is in graph[i], then i will be in graph[j].


# 分析  {#analyse}
这道题的大致意思是：给出一个无向图，要我们判断这个图是否为二分图。

首先我们要理解什么是二分图。二分图又称作二部图，是图论中的一种特殊模型。设G=(V,E)是一个无向图，如果顶点V可分割为两个互不相交的子集(A,B)，并且图中的每条边（i，j）所关联的两个顶点i和j分别属于这两个不同的顶点集(i in A,j in B)，则称图G为一个二分图。

在弄清楚二分图的定义后，我们可以利用一种简单的方法来判断一个图是否为二分图，即染色法。因为二分图可以划分为两个互不相交的子集，所以可以只用两种颜色染色，使得一条边的两个邻接节点染上不同的颜色。

首先定义颜色，0为未上色，1为第一种颜色，-1为第二种颜色

大致做法：
1. 声明一个数组color，用来保存所有点的颜色，全初始化为未上色，然后定义深度优先搜索函数dfs，这个函数第一个参数是图graph，第二个参数是要遍历的开始点start，第三个参数是遍历时开始点start的上一个点的颜色，第四个参数是所有点的颜色数组color。
2. 在dfs函数中，若此刻遍历的开始点start的颜色为未上色，则染上与上一个邻接点的颜色不同的颜色，然后继续深度优先搜索；若已上色，则判断该点颜色是否与上一个邻接点的颜色不同，若不同则返回true，否则返回false。
3. 在isBipartite函数中，用for循环遍历所有点，若该点是无颜色，则传入深度优先搜索函数dfs函数处理，否则跳过。（因为在一次dfs遍历后，与开始点start连通的点肯定都进行了染色处理，所以已经染色的可以跳过。注意，此时传入的点是第一个遍历的点，不存在与之连接的上一个点的颜色，所以dfs函数的第三个参数我们默认传入-1，目的是让第一个遍历的点染上第一种颜色。）

# 代码  {#code}
```
class Solution {
public:
    bool dfs(vector<vector<int>>& graph, int start, int beforeColor, vector<int>& color) {
        if (color[start] != 0) {
            if (color[start] == beforeColor) return false;
            else return true;
        }

        color[start] = (-1) * beforeColor;
        int len = graph[start].size();
        for (int i = 0; i < len; i++) {
            if (dfs(graph, graph[start][i], color[start], color) == false) {
                return false;
            }
        }

        return true;
    }

    //染色法，二分图最少只需两种颜色区分两个点集
    //一条边对应的两个点必须是不同的颜色
    bool isBipartite(vector<vector<int>>& graph) {
        int len = graph.size();
        vector<int> color;
        //初始化颜色，0为未上色，1为第一种颜色，-1为第二种颜色
        for (int i = 0; i < len; i++) {
            color.push_back(0);
        }

        for (int i = 0; i < len; i++) {
            if (color[i] == 0 && dfs(graph, i, -1, color) == false) {
                return false;
            }		
        }
        return true;
    }

};
```