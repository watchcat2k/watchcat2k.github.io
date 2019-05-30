---
layout: post
title: LeetCode练习题839. Similar String Groups——图搜索
date: 2018-10-14 00:00:00
categories: 
- Leetcode
tags: 
- LeetCode
- 图搜索
- C++
description: LeetCode上的关于图搜索的练习题
---


# 题目  {#question}
Two strings X and Y are similar if we can swap two letters (in different positions) of X, so that it equals Y.

For example, "tars" and "rats" are similar (swapping at positions 0 and 2), and "rats" and "arts" are similar, but "star" is not similar to "tars", "rats", or "arts".

Together, these form two connected groups by similarity: {"tars", "rats", "arts"} and {"star"}.  Notice that "tars" and "arts" are in the same group even though they are not similar.  Formally, each group is such that a word is in the group if and only if it is similar to at least one other word in the group.

We are given a list A of strings.  Every string in A is an anagram of every other string in A.  How many groups are there?

**Example 1:**
```bash
Input: ["tars","rats","arts","star"]
Output: 2
```

**Note:**

- A.length <= 2000
- A[i].length <= 1000
- A.length * A[i].length <= 20000
- All words in A consist of lowercase letters only.
- All words in A have the same length and are anagrams of each other.
- The judging time limit has been increased for this question.


# 分析  {#analyse}
首先我们要实现一个判断两个字符串相似的函数isSimilar，该函数的实现思路如下：
1. 若两个字符串长度不等，返回false，否则初始化整形变量num，记录两个字符串对应位置不相同字符的个数，并初始化字符型变量数组ch1[2]和ch2[2]，分别用来记录两个字符串头两个相应位置不相同的字符。
2. 用for循环遍历一次这两个字符串，记录两个字符串对应位置不相同字符的个数，和两个字符串头两个相应位置不相同的字符。
3. 若两个字符串对应位置不相同字符的个数不等于2，则返回false，否则判断ch1[0] == ch2[1] && ch1[1] == ch2[0]，若判断结果为真，则返回true，否则返回false。

注意，tars和arts这两个字符串并不相似，但这两个字符串都与rats相似，相当于tars和arts通过rats字符串拥有了间接的关系，所以tars、arts被划分为rats同一组。由此可见，同一组内的字符串要么相似，要么存在这种间接关系。

对于这种情况，我们可以利用dfs深度优先算法来求解，对一个字符串搜索出与之相似的字符串，然后又对新得到的字符串进行搜索，求出与之相似的字符串，这样递归下去，所有与初始字符串相似或有间接关系的字符串都能被找到。

具体做法如下：
1. 初始化一个整型数组group，记录每个字符串所处的分组。0代表未分组，1表示处在分组1，2表示处在分组2，以此类推。并令group[0] = 1，代表第一个字符串处在分组1。并初始化整型变量result，赋值为1，代表现有分组的个数为1。
2. 以第一个字符串为起点，以深度优先算法遍历所有字符串，找出所有与第一个字符串相似或有间接关系的字符串，并将这些字符串的group值设为起点字符串group值。
3. 遍历所有字符串，若找到group值为0的字符串，即未分组的字符串，则令result值增加1，并将该字符串的group值赋值为result，然后以该字符串为起点，重复步骤2。
4. 最终得到的result值，就是分组的个数。

# 代码  {#code}
```
class Solution {
public:
bool isSimilar(string str1, string str2) {
    if (str1 == str2) return true;
	int len1 = str1.size();
	int len2 = str2.size();
	if (len1 != len2) return false;
	
	//num表示不相等字符的个数
	int num = 0;
	char ch1[2], ch2[2];
	for (int i = 0; i < len1; i++) {
		if (str1[i] != str2[i]) {
			if (num >= 2) return false;  //已经有两不同字符
			ch1[num] = str1[i];
			ch2[num] = str2[i];
			num++;
		}
	}
	if (num <= 1) return false;

	if (ch1[0] == ch2[1] && ch1[1] == ch2[0]) {
		return true;
	}
	else {
		return false;
	}

}

void dfs(vector<string>& A, int start, vector<int> &group) {
	int len = A.size();
	for (int i = 0; i < len; i++) {
		if (i == start) continue;
		if (group[i] > 0) continue;
		if (isSimilar(A[start], A[i])) {
			group[i] = group[start];
			dfs(A, i, group);
		}
	}
}

int numSimilarGroups(vector<string>& A) {
	int result = 0;  //代表分组个数
	vector<int> group(2000); //代表字符串所在的组，值为0，代表还未分组
	int len = A.size();
	for (int i = 0; i < 2000; i++) {
		group[i] = 0;
	}
	//刚开始的第一个字符串为第一个组
	group[0] = 1;
	result++;
	dfs(A, 0, group);
	for (int i = 0; i < len; i++) {
		if (group[i] == 0) {
			result++;
			group[i] = result;
			dfs(A, i, group);
		}
	}

	return result;
} 
};
```