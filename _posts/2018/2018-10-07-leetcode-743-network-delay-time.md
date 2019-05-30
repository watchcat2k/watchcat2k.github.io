---
layout: post
title: LeetCode练习题743. Network Delay Time——图搜索
date: 2018-10-07 00:00:00
categories: 
- Leetcode
tags: 
- LeetCode
- 图搜索
- C++
description: LeetCode上的关于图搜索的练习题
---


# 题目  {#question}
There are N network nodes, labelled 1 to N.

Given times, a list of travel times as directed edges times[i] = (u, v, w), where u is the source node, v is the target node, and w is the time it takes for a signal to travel from source to target.

Now, we send a signal from a certain node K. How long will it take for all nodes to receive the signal? If it is impossible, return -1.

**Note:**

- N will be in the range [1, 100].
- K will be in the range [1, N].
- The length of times will be in the range [1, 6000].
- All edges times[i] = (u, v, w) will have 1 <= u, v <= N and 1 <= w <= 100.


# 分析  {#analyse}
这道题可以简化为有向图求某一点到其余点最短路径的问题，因为网络传播时，信号向所有方向传播，某节点收到信号时，该信号一定是经过最短的路径到达的。若从起始点出发，存在一点与起始点不连通，则返回-1，否则返回起始点到其余点最短路径中的最大值。

因为题目中边的权都是正数，所以这里使用Dijkstra算法，大概思路如下：
1. 一开始设置从起始点到其余点 i 的最短路径 dist[i] 为 INT_MAX
2. 以宽度优先算法遍历有向图，遍历到的节点 u ，查看与 u 相连的所有节点 v，若 dist[u] + l(u,v) < dist[v]，则令dist[v] = dist[u] + l(u,v)。
3. 最后检查所有节点 i 的 dist[i]，若存在 dist[i] 为 INT_MAX，说明有节点与初始点不连通，返回 -1，否则返回 dist[i] 中的最大值。

# 代码  {#code}
```
class Solution {
public:
    int networkDelayTime(vector<vector<int>>& times, int N, int K) {
        int dist[101];
        bool found[101];
        vector<int> que;
        int temp;
        int max = 0;
        int len = times.size();

        for (int i = 0; i < 101; i++) {
            dist[i] = INT_MAX;
            found[i] = false;
        }
        dist[K] = 0;
        found[K] = true;

        que.push_back(K);
        while (que.size()) {
            temp = que.front();
            que.erase(que.begin());
            for (int i = 0; i < len; i++) {
                if (times[i][0] == temp) {
                    if (dist[temp] + times[i][2] < dist[times[i][1]]) {
                        dist[times[i][1]] = dist[temp] + times[i][2];
                        found[times[i][1]] = true;
                        que.push_back(times[i][1]);
                    }
                }
            }
        }

        for (int i = 1; i <= N; i++) {
            if (found[i] == false) {
                return -1;
            }
        }

        for (int i = 1; i <= N; i++) {
            max = dist[i] > max ? dist[i] : max;
        }
        return max;
    }
};
```