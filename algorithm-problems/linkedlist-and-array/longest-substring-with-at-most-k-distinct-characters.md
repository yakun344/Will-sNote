# Longest Substring with At Most K Distinct Characters

_update 2018-10-18 16:09:53_

[LeetCode](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/description/)

Given a string, find the length of the longest substring T that contains at most k distinct characters.

**Example 1:**

```text
Input: s = "eceba", k = 2
Output: 3
Explanation: T is "ece" which its length is 3.
```

**Example 2:**

```text
Input: s = "aa", k = 1
Output: 2
Explanation: T is "aa" which its length is 2.
```

&lt;/br&gt;

## Basic Idea:

利用sliding window的思路，维持一个hashmap记录窗口内每个字母的个数，如果map的大小超过k，则left向右移动，更新map，直到大小变为k. 解就是窗口size的最大值。

### Java Code:

```java
class Solution {
    public int lengthOfLongestSubstringKDistinct(String s, int k) {
        Map<Character, Integer> map = new HashMap<>();
        int ret = 0, left = 0, right = 0;
        for (right = 0; right < s.length(); ++right) {
            char c = s.charAt(right);
            map.put(c, map.getOrDefault(c, 0) + 1);
            if (map.size() > k) {
                // shrink left to make size == k
                while (map.size() > k) {
                    char last = s.charAt(left);
                    if (map.get(last) == 1) {
                        map.remove(last);
                    } else {
                        map.put(last, map.get(last) - 1);
                    }
                    left++;
                }
            }
            ret = Math.max(ret, right - left + 1);
        }
        return ret;
    }
}
```

