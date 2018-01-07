## Longest Repeating Character Replacement
_udpate Aug 8,2017  14:16_

---
[LeetCode](https://leetcode.com/problems/longest-repeating-character-replacement/description/)

Given a string that consists of only uppercase English letters, you can replace any letter in the string with another letter at most k times. Find the length of a longest substring containing all repeating letters you can get after performing the above operations.

**Note:**
Both the string's length and k will not exceed 104.

**Example 1:**

    Input:
    s = "ABAB", k = 2
    
    Output:
    4

Explanation:
Replace the two 'A's with two 'B's or vice versa.
Example 2:

    Input:
    s = "AABABBA", k = 1
    
    Output:
    4
    
Explanation:
Replace the one 'A' in the middle with 'B' and form "AABBBBA".
The substring "BBBB" has the longest repeating letters, which is 4.

#### Basic Idea:
**Sliding Window:**
我们注意到，如果不限制交换次数 k，答案应该是整个字符串长度 len(s) 减去出现最多的字母的次数，所以对于这里限制了 k 的情况，我们可以maintain一个window，保证在window内部有 `windowSize - mostTimes <= k`。每次先令 right 右边界右移，如果违背了之前的性质，则让left右移来恢复，跟踪最大 windowSize 就是解。

具体地，我们需要每次统计当前window内部各字母出现的次数，所以要maintain一个int[26]作为counter，时间复杂度为O(26n).

#### Java Code:
```java
    // maintain a window，跟踪其内出现最多字母的次数，window size，以及两者之差 <= k
    // 解就是过程中最大的 window size
    public class Solution {
        public int characterReplacement(String s, int k) {
            if (s == null || s.length() == 0) return 0;
            int[] count = new int[26];
            int maxSize = 0; // res
            char prev = '0';
            
            int left = 0, right = 0;
            maxSize = 1;
            prev = s.charAt(0);
            count[prev - 'A']++;
            while (right < s.length() - 1) {
                right++;
                count[s.charAt(right) - 'A']++;
                // 如果右边新进入字母和当前最多字母相同, 则不需要操作
                if (s.charAt(right) != prev) {
                    prev = getMostFreq(count);
                    // 如果window size - 当前最多字母次数 > k, 则left右移
                    while (right - left + 1 - count[prev - 'A'] > k) {
                        count[s.charAt(left) - 'A']--;
                        left++;
                        prev = getMostFreq(count);
                    }
                }
                // 每次循环结束，跟踪最大 window size
                maxSize = Math.max(right - left + 1, maxSize);
            }
            return maxSize;
        }
        private char getMostFreq(int[] count) {
            int max = Integer.MIN_VALUE;
            int ret = 0;
            for (int i = 0; i < count.length; ++i) {
                if (count[i] > max) {
                    max = count[i];
                    ret = i;
                }
            }
            return (char)(ret + 'A');
        }
    }
```