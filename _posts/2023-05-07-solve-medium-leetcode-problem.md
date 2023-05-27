---
title: Solve Medium LeetCode Problem
date: 2023-05-07 19:35 +0300
math: true
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
|   ababcdww   |       abcdw       |             5              |
|    abcde     |       abcd        |             4              |

## Solution
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

  private static boolean hasOnlyUniqueChars(String substring) {
    char[] stringChars = substring.toCharArray();
    Set<Character> uniqueChars = new HashSet<>();
    for (char stringChar : stringChars) {
      uniqueChars.add(stringChar);
    }
    return stringChars.length == uniqueChars.size();
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
(`ababcdww` is ignored as it is the whole initial string rather than a substring). From this list, only the first two have unique
letters, the rest substrings will be ignored.
The second iteration takes letter `b` and these are the corresponding substrings `b`, `ba`, `bab`, `babc`, `babcd`, `babcdw`, `babcdww`.
Again, only the first two have unique characters and will be added to the set pf substrings.
And so on for the rest of letters in the input.

This image shows how substrings for each letter (iteration) are discovered. Invalid substrings are shown in grey. Please remember that sets ignore duplicate elements.
![Desktop View](/assets/2023-05-27/Medium LeetCode task.v3.png){: width="972" height="589" }

The total number of substrings this method will create and validate is: 
$$ N + (N - 1) + (N - 2) + (N - 3) + ... + 2 + 1 $$

This is known to be $$ O(N^2/2) $$, of which the $$ O(N^2) $$ part is important in terms of algorithm complexity.

`findLengthOfLongestSubstring` makes a call to `hasOnlyUniqueChars` on each inner loop iteration. The latter method iterates over
letters of a substring. Since an average substring has length of $$ N/2 $$, it gives us the complexity of `findLengthOfLongestSubstring` as
$$ O(N^2) * O(N/2) $$, which is $$ O(N^2) * O(N) $$ in terms of complexity, and it is equal to $$O(N^3)$$.

As for the complexity of `findLengthOfLongestSubstring`, it will be less than the above, because the set of substrings has less
elements than total number of substrings checked by the first method. Thus, the total complexity of the whole algorithm is 
$$ O(N^3) $$

## Conslusion

Congratulations! We have just solved a medium task. Wasn't it fascinating?

We created a simple implementation, which is easy to invent and understand.
The drawback of it is that the algorithm complexity is quite high. While the input string is short this may have no difference,
but with longer strings it can become a performance concern.
In the next post, we will discuss how to make the algorithm faster.

See you later and happy coding!
