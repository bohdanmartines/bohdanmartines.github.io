---
title: Solve Medium LeetCode Problem
date: 2023-05-07 19:35 +0300
---
In this article, we are going to solve
[this problem](https://leetcode.com/problems/longest-substring-without-repeating-characters/) from LeetCode.
We will focus on a simple solution and will analyze what its complexity is.

## Problem
According to the task, we are given a string, and our goal is to find the length of the longest substring that has no repeated
characters in it. 

Let's put a sets of example inputs and corresponding expected results:

| Input string | Longest substring | Longest substring's length |
|:------------:|:-----------------:|:--------------------------:|
|   aaaabbbb   |        ab         |             2              |
|  qazqazqzq   |        qaz        |             3              |
|   ababcdww   |       abcd        |             5              |
|    abcde     |       abcd        |             4              |

## Simple solution
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
the details in the next section.

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
                if (hasOnlyUniqueChars(substring)) {
                    substrings.add(substring);
                }
            }
        }
        // We'll remove the input string from the set, as we don't it as a substring
        substrings.remove(input);
        return substrings;
    }

    private static int findLengthOfLongestSubstring(Set<String> substrings) {
        int maxLength = 0;
        for (String substring: substrings) {
            if (substring.length() > maxLength) {
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

## Explanation and complexity

Shall we dive into the details and analyze the complexity of the algorithm?

Firstly, `findAllSubstrings` method creates a set of valid substrings for the given input string. 
Each iteration of the outer loop takes a letter from the input, while the inner loop finds substrings starting with
this letter. Substrings with duplicated characters are ignored to not consume extra memory,
as those are not valid according to the task.

Let's consider an example when we have `ababcdww` as the input word. 
The first iteration will take letter `a` and find such substrings: `a`, `ab`, `aba`, `abab`, `ababc`, `ababcd` and `ababcdw`
(`abbde` is ignored as it is the whole input string rather than a substring). From this list, only the first two have unique
letter, the rest will be ignored.
The second iteration takes letter `b` and these are the corresponding substrings `b`, `ba`, `bab`, `babc`, `babcd`, `babcdw`, `babcdww`.
Again, only the first two have unique characters and will be added to the set pf substrings.
And so on for the rest of letters in the input.

If we consider each letter in the input string as a substring start letter, this will be the total number of substrings:
```
(O(N^2) - 1) + (O(N^2) - 1) + (O(N^2) - 2) + (O(N^2) - 3) + ... + 2 + 1
```
This is known to be O(N<sup>2</sup>)/2, of which the O(N<sup>2</sup>) part is important in terms of algorithm complexity.

