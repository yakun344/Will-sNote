# 1375. Substring With At Least K Distinct Characters

Given a string S with only lowercase characters.

Return the number of substrings that contains at least k distinct characters. Example

**Example 1:**

```text
Input: S = "abcabcabca", k = 4
Output: 0
Explanation: There are only three distinct characters in the string.
```

**Example 2:**

```text
Input: S = "abcabcabcabc", k = 3
Output: 55
Explanation: Any substring whose length is not smaller than 3 contains a, b, c.
    For example, there are 10 substrings whose length are 3, "abc", "bca", "cab" ... "abc"
    There are 9 substrings whose length are 4, "abca", "bcab", "cabc" ... "cabc"
    ...
    There is 1 substring whose length is 12, "abcabcabcabc"
    So the answer is 1 + 2 + ... + 10 = 55.
```

**Notice**

```text
    10 ≤ length(S) ≤ 1,000,000
    1 ≤ k ≤ 26
```

## Basic Idea:

此题要找到所有包含至少k个不同字母的substring个数，我们可以通过sliding window（two pointers）的方法找到每个 只包含exactly k distinct字母的substring，而对于每个，我们只需要在其后加入更多字母即可形成不同的满足条件的substring。

例如对于 `|abc|de, k=3`，我们对于substring abc, 可以在其后加入更多字母，最终可以有 `abc, abcd, abcde` 三个substring。

## Java Code:

```java
public class Solution {
    /**
     * @param s: a string
     * @param k: an integer
     * @return: the number of substrings there are that contain at least k distinct characters
     */
    public long kDistinctCharacters(String s, int k) {
        Map<Character, Integer> map = new HashMap<>();
        long ret = 0;
        int left = 0, right = -1;
        while (true) {
            while (right + 1 < s.length() && map.size() < k) {
                char c = s.charAt(++right);
                map.put(c, map.getOrDefault(c, 0) + 1);
            }
            if (map.size() != k) {
                break;
            }
            ret += s.length() - right;
            char leftC = s.charAt(left++);
            map.put(leftC, map.get(leftC) - 1);
            if (map.get(leftC) == 0) {
                map.remove(leftC);
            }
        }
        return ret;
    }
}
```

