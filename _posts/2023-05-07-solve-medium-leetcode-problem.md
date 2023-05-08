---
title: Solve Medium LeetCode Problem
date: 2023-05-07 19:35 +0300
---
In this article, we are going to solve
[this problem](https://leetcode.com/problems/longest-substring-without-repeating-characters/) from LeetCode

## Problem
In this task, we are given a string, and our goal is to find the length of the longest substring that has no repeated
characters in it. 

Let's put a couple of sets of the input and expected result:

| Input string | Longest substring | Longest substring's length |
|:------------:|:-----------------:|:--------------------------:|
|   aaaabbbb   |        ab         |             2              |
|  qazqazqzq   |        qaz        |             3              |
|   ababcdww   |       abcd        |             4              |
|    abcde     |       abcd        |             4              |

## Solution
### Simple solution
An easy way to address this is to split the task into two steps:
1. Find all possible substrings for the given string
2. For each of the substrings, check if it has repeating characters and if its length is greater than the length of the
current longest substring

To implement this approach, we will need two nested for-loops for the first step, also a var to store the current
longest substring's length and one more for-loop for the second step.

To start with, let's create a skeleton for the methods and invoke them. This will give you an idea about the algorithm.

```java
public class Solution {
    
    public int lengthOfLongestSubstring(String input) {
        Set<String> substrings = findAllSubstrings(input);
        return findLengthOfLongestSubstring(substrings);
    }
    
    private Set<String> findAllSubstrings(String input) {
      // Implement me
    }

    private int findLengthOfLongestSubstring(Set<String> substrings) {
      // Implement me
    }
}
```
Now that we have a clear vision of the steps needed, let's implement the private methods.

```java
public class Solution {
    
    public int lengthOfLongestSubstring(String input) {
        Set<String> substrings = findAllSubstrings(input);
        return findLengthOfLongestSubstring(substrings);
    }
    
    private Set<String> findAllSubstrings(String input) {
        Set<String> substrings = new HashSet<>();
        for (int beginIndex = 0; beginIndex < input.length(); beginIndex++) {
            for (int endIndex = beginIndex; endIndex < input.length(); endIndex++) {
                String substring = input.subtring(beginIndex, endIndex + 1);
                substrings.add(substring);
            }
        }
    }

    private int findLengthOfLongestSubstring(Set<String> substrings) {
      // Implement me
    }
}
```
