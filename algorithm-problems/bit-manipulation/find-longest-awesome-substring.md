---
description: 'Jul 5, 2021'
---

# Find Longest Awesome Substring

[Leetcode](https://leetcode.com/problems/find-longest-awesome-substring/)



Given a string `s`. An _awesome_ substring is a non-empty substring of `s` such that we can make any number of swaps in order to make it palindrome.

Return the length of the maximum length **awesome substring** of `s`.

**Example 1:**

```text
Input: s = "3242415"
Output: 5
Explanation: "24241" is the longest awesome substring, we can form the palindrome "24142" with some swaps.
```

**Example 2:**

```text
Input: s = "12345678"
Output: 1
```

**Example 3:**

```text
Input: s = "213123"
Output: 6
Explanation: "213123" is the longest awesome substring, we can form the palindrome "231132" with some swaps.
```

**Example 4:**

```text
Input: s = "00"
Output: 2
```

**Constraints:**

* `1 <= s.length <= 10^5`
* `s` consists only of digits.

### Basic Idea:

这道题目和[Number of Wonderful Substrings](number-of-wonderful-substrings.md) 比较接近。因为数字的范围只有0-9，而且只考虑奇偶次数，我们可以用一个mask来表示任意一个substring中每个数字奇偶次数的状态。我们用一个HashMap来存放之前出现过的mask，更新当前index i左边部分的mask，如果前面出现过相同的mask，则说明中间部分所有数字出现均为even，如果前面出现过和当前mask相差1bit的mask，则说明中间部分只有1个字母是odd。所以总时间复杂度为 `O(10N)` .

### Java Code:

```java
class Solution {
    public int longestAwesome(String s) {
        // 存放每个mask对应的最早出现的右边界index
        HashMap<Integer, Integer> prefixMaskIndexMap = new HashMap<>();
        prefixMaskIndexMap.put(0, -1); // 处理从头开始match的情况
        int mask = 0; // [0,i] 的mask
        int maxLen = 0;
        for (int i = 0; i < s.length(); ++i) {
            mask ^= 1 << (s.charAt(i) - '0');
            Integer prev = prefixMaskIndexMap.get(mask);
            if (prev != null) {// 和前面出现过的mask相等，表示中间这段每个数字都出现even次
                maxLen = Math.max(maxLen, i -  prev);
            }
            for (int j = 0; j < 10; ++j) {// 相差一个bit，表示只有一个数字是odd次
                prev = prefixMaskIndexMap.get(mask ^ (1 << j));
                if (prev != null) {
                    maxLen = Math.max(maxLen, i -  prev);
                }
            }
            // 确保每个mask只存放最左边的
            if (!prefixMaskIndexMap.containsKey(mask)) {
                prefixMaskIndexMap.put(mask, i);
            }
        }
        return maxLen;
    }
}
```



 

