---
layout: post
title: LeetCode练习题400. Nth Digit——数组
date: 2018-09-04 00:00:00
categories: 
- Leetcode
tags: 
- LeetCode
- 数组
- C++
description: LeetCode上的关于数组的练习题
---


# 题目  {#question}
Find the nth digit of the infinite integer sequence 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ...

**Note:**

n is positive and will fit within the range of a 32-bit signed integer (n < 231).

```bash
Example 1:
Input:3
Output:3

Example 2:
Input:11
Output:0
Explanation:The 11th digit of the sequence 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ... is a 0, which is part of the number 1
```


# 第一种方法  {#first-solution}
## 分析  {#first-solution-analyse}
我首先是声明一个栈num，一个初始值为0的计数器count，循环遍历1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11，... 这些数，在每次循环时，把循环到的数字拆为单个数字，如10拆为1和0，先push低位0进栈num中，然后push高位1进栈num中，这样栈顶是数列位置靠前的数，栈底是数列位置靠后的数。当栈不为空时，令计数器count++，判断count是否等于传入的参数n，若等于，则返回栈顶值，即所求值；若不等，则将栈顶的值pop出去。直到栈为空，然后进入下一个循环。这样的话，每次循环只涉及数列中一个数的操作，

## 代码  {#first-solution-code}
```
int findNthDigit(int n) {
	int yu = 0, shang = 0;
	int count = 0;
	stack<int> num;

	for (int i = 1; i <= n; i++) {
		shang = i;			//令余数为n，方便循环进行
		while (shang != 0) {
			yu = shang % 10;
			shang = shang / 10;
			num.push(yu);
		}

		while (num.size() != 0) {
			count++;
			if (count == n) {
				return num.top();
			}
			else {
				num.pop();
			}
		}	
	}
}
```

这种写法逻辑上是正确，但是评测时出现了**超时错误**，也就是说，代码是正确的，但运行效率不高，该代码的复杂度为O(n^2)，所以必须要改变想法。

# 正确方法  {#right-solution}
## 分析  {#right-solution-analyse}
在仔细观察数列和所求值之间的关系之后，发现它们之间确实存在一定规律，利用该规律是可以直接构造出复杂度为O(n)的算法。

|数的位数n（位） |n位数一共有多少个（个） |范围为 |
|--|--|--|
|1 |9 |0-9 |
|2 |90 |10-99 |
|3 |900 |100-999 |
|4 |9000 |1000-9999 |

通过观察数的位数，该位数上一共有多少个数，从哪个数开始等等这些规律，可以轻松地实现基于数列规律的算法。大致方法是，首先确定所求的位置n处落在哪一个数上，该数有多少位，如位置n=11时，落在数字10上，数字10有两位数；然后确定位置n落在该数的哪一位，如位置n=11时，落在数字10的十位。

## 代码  {#right-solution-code}
```
class Solution {
public:
    int findNthDigit(int n) {
        long long int digitLength = 0;
        long long int shang = 0, yu = 0;
        long long int resultN = n;
        long long int targetNum = 0;
        long long int result = 0;
        for (int i = 0; ; i++) {
             digitLength = (i + 1) * 9 * pow(10, i);
             if (resultN < digitLength) {
                 shang = resultN / (i + 1);
                 yu = resultN % (i + 1);

                 if (yu == 0) {
                     yu = i + 1;
                     shang--;
                 }
                 targetNum = shang + pow(10, i);
                 //然后取targetNum的第yu位
                 result = targetNum % (long long int)pow(10, i + 2 - yu);
                 result = result / (long long int)pow(10, i + 1 - yu);
                 return result;
             }
             else {
                 resultN -= digitLength;
             }
        }
    }
};
```