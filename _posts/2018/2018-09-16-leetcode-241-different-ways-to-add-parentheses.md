---
layout: post
title: LeetCode练习题241. Different Ways to Add Parentheses——分治策略
date: 2018-09-16 00:00:00
categories: 
- Leetcode
tags: 
- LeetCode
- 分治策略
- C++
description: LeetCode上的关于分治策略的练习题
---

# 题目  {#question}
Given a string of numbers and operators, return all possible results from computing all the different possible ways to group numbers and operators. The valid operators are +, - and *.

**Example 1:**
```bash
Input: "2-1-1"
Output: [0, 2]
Explanation: 
((2-1)-1) = 0
(2-(1-1)) = 2
```

**Example 2:**
```bash
Input: "2*3-4*5"
Output: [-34, -14, -10, -10, 10]
Explanation:
(2*(3-(4*5))) = -34
((2*3)-(4*5)) = -14
((2*(3-4))*5) = -10
(2*((3-4)*5)) = -10
(((2*3)-4)*5) = 10
```



# 分析  {#analyse}
这道题是典型的利用递归实现分而治之的题目。

基本思想是：若要求解一个字符串加上不同括号产生的所有结果，先对输入的字符串进行for循环遍历，若遇到'+'、'-'、'*'这三个运算符，则记录当前的运算符。然后分别对运算符左边和右边的子字符串递归，求解子字符串加上不同括号产生的所有结果。即是下面的两条语句：

```
lResult = diffWaysToCompute(input.substr(0, i));
rResult = diffWaysToCompute(input.substr(i + 1, len - i - 1));
```

最后，在计算完子字符串的结果后，根据之前记录的运算符，算出最终结果。

# 要点  {#important-point}
这个递归方法的结束条件是：当传入的字符串中不含任何运算符时，将该字符串转化为数字，然后将该数字push入Vector容器数组并返回。我用了一个bool变量flag来判断字符串中是否含运算符。

因为一个字符串加上不同括号会产生不止一个结果，所以diffWaysToCompute函数返回的是Vector容器数组，该Vector容器数组包含了该字符串可以产生的所有结果。所以利用左右子字符串产生的结果来计算最终结果时，是利用了两个嵌套的for循环，如下所示

```
for (int j = 0; j < lResult.size(); j++) {
    for (int k = 0; k < rResult.size(); k++) {
        switch (op) {
        case '+':
            result.push_back(lResult[j] + rResult[k]);
            break;
        case '-':
            result.push_back(lResult[j] - rResult[k]);
            break;
        case '*':
            result.push_back(lResult[j] * rResult[k]);
            break;
        default:
            break;
        }
    }
}
```

# 代码  {#code}
```
class Solution {
public:
    vector<int> diffWaysToCompute(string input) {
        vector<int> lResult;
        vector<int> rResult;
        vector<int> result;
        int len = input.size();
        bool flag = false;
        char op;

        if (len == 0) return result;

        for (int i = 0; i < len; i++) {
            switch (input[i]) {
                case '+':
                case '-':
                case '*':
                    flag = true;
                    op = input[i];
                    lResult = diffWaysToCompute(input.substr(0, i));
                    rResult = diffWaysToCompute(input.substr(i + 1, len - i - 1));
                    for (int j = 0; j < lResult.size(); j++) {
                        for (int k = 0; k < rResult.size(); k++) {
                            switch (op) {
                            case '+':
                                result.push_back(lResult[j] + rResult[k]);
                                break;
                            case '-':
                                result.push_back(lResult[j] - rResult[k]);
                                break;
                            case '*':
                                result.push_back(lResult[j] * rResult[k]);
                                break;
                            default:
                                break;
                            }
                        }
                    }
                    break;
                default:
                    break;
            }
        }

        if (flag == false) {
            result.push_back(atoi(input.data()));
        }

        return result;
    }
    
};
```