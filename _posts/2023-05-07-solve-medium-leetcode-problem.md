---
title: Solve medium LeetCode problem
date: 2023-05-07 19:35 +0300
---
In this article, we are going to solve
[this problem](https://leetcode.com/problems/longest-substring-without-repeating-characters/) from LeetCode

## Problem
In this task, we are given a string, and our goal is to find the length of the longest substring that has no repeated
characters in it. 

## Solution
### Simple solution
An easy way to address this is to split the task into two steps:
1. Find all possible substrings for the given string
2. For each of the substrings, check if it has repeating characters and if its length is greater than the length of the
current longest substring

To implement this approach, we will need two nested for-loops for the first step, also a var to store the current
longest substring's length and one more for-loop for the second step.

Let's get to it!
