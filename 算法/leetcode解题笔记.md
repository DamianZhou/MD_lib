#leetcode解题笔记

[TOC]

##Easy

##Medium
###Longest Palindromic Substring 最长回文子串
原题：
>Given a string S, find the longest palindromic substring in S. 
You may assume that the maximum length of S is 1000, and there exists one unique longest palindromic substring.

我自己的思路：
 1. 首先写出回文的判断条件。区分奇偶。
 2. 查看中间位置是否符合回文要求。若符合，整合句子就是几个回文；若不符合，分别从左右两边来查找最大的回文（此处需要将中间位置的点添加到左右两边！对应偶数对偶的情况）。找到==局部最大的回文==以后，向两边扩充，以匹配最大回文

遇到的问题：
 1. 对于局部最大回文的查找。递归算法？


##Hard