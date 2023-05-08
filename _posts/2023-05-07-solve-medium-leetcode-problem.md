---
title: Solve Medium LeetCode Problem
date: 2023-05-07 19:35 +0300
---
In this article, we are going to solve
[this problem](https://leetcode.com/problems/longest-substring-without-repeating-characters/) from LeetCode

## Problem
According to the tasks, we are given a string, and our goal is to find the length of the longest substring that has no repeated
characters in it. 

Let's put a couple of sets of the input and expected result:

| Input string | Longest substring | Longest substring's length |
|:------------:|:-----------------:|:--------------------------:|
|   aaaabbbb   |        ab         |             2              |
|  qazqazqzq   |        qaz        |             3              |
|   ababcdww   |       abcd        |             5              |
|    abcde     |       abcd        |             4              |

## Solution
### Simple solution
An easy way to address this is to split the task into two steps:
1. Find all possible substrings for a given string
2. For each of the substrings, check if it has repeating characters and if its length is greater than the length of the
current longest substring

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

Now that we have a clear vision of the main steps needed, let's implement the private methods. We will discuss 
the details just after the snippet bellow.

```java
public class Solution {
    
    public static int lengthOfLongestSubstring(String input) {
        Set<String> substrings = findAllSubstrings(input);
        return findLengthOfLongestSubstring(substrings);
    }
    
    private static Set<String> findAllSubstrings(String input) {
        Set<String> substrings = new HashSet<>();
        for (int beginIndex = 0; beginIndex < input.length(); beginIndex++) {
            for (int endIndex = beginIndex; endIndex < input.length(); endIndex++) {
                String substring = input.substring(beginIndex, endIndex + 1);
                substrings.add(substring);
            }
        }
        // We'll remove the input string from the set, as we don't it as a substring
        substrings.remove(input);
        return substrings;
    }

    private static int findLengthOfLongestSubstring(Set<String> substrings) {
        int maxLength = 0;
        for (String substring: substrings) {
            if (hasOnlyUniqueChars(substring) && substring.length() > maxLength) {
                maxLength = substring.length();
            }
        }
        return maxLength;
    }
    
    private static boolean hasOnlyUniqueChars(String substring) {
        char[] stringChars = substring.toCharArray();
        Set<Character> uniqueChars = new HashSet<>();
        for (char stringChar : stringChars) {
          uniqueChars.add(stringChar);
        }
        return stringChars.length == uniqueChars.size();
    }
}
```

Shall we dive into the details and analyze the complexity of the algorithm?

Firstly, `findAllSubstrings` method creates a set of all the possible substrings for the given input string. 
The outer loop picks letters which will be used as the first letter for substrings. The inner loop finds all substrings
for a given first letter. We also make sure the input strings is not stored in the resulting set at the end 
of the method. Complexity for this method is O(N<sup>2</sup>). Actually, it's O(N<sup>2</sup>/2) to be precise, 
but we don't care about constants in the O notation, thus can ignore /2 part.
