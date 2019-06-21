# Is Subsequence

_update Aug 4,2017 21:51_

[LeetCode](https://leetcode.com/problems/is-subsequence/description/)

Given a string s and a string t, check if s is subsequence of t.

You may assume that there is only lower case English letters in both s and t. t is potentially a very long \(length ~= 500,000\) string, and s is a short string \(&lt;=100\).

A subsequence of a string is a new string which is formed from the original string by deleting some \(can be none\) of the characters without disturbing the relative positions of the remaining characters. \(ie, "ace" is a subsequence of "abcde" while "aec" is not\).

**Example 1:**

```text
s = "abc", t = "ahbgdc"
Return true.
```

**Example 2:**

```text
s = "axc", t = "ahbgdc"
Return false.
```

**Follow up:** If there are lots of incoming S, say S1, S2, ... , Sk where k &gt;= 1B, and you want to check one by one to see if T has its subsequence. In this scenario, how would you change your code?

## Basic Idea:

最简单的方法就是遍历一遍，两个指针分别跟踪当前对比的t和s中的字符，O\(len\(t\)\)时间。 对于follow up，如果对于单一的 t 有很多 s 需要对比，我们可以把 t 进行预处理，存入一个 hashmap，key是t中的每个字符，value则是这些字符出现的indice。这样，我们只要遍历一遍 s，每次对于每个字符，用二分法找它们出现的位置即可。 例如：

```text
// Follow-up: O(N) time for pre-processing, O(Mlog?) for each S.
// Eg-1. s="abc", t="bahbgdca"
// idx=[a={1,7}, b={0,3}, c={6}]
//  i=0 ('a'): prev=1
//  i=1 ('b'): prev=3
//  i=2 ('c'): prev=6 (return true)
// Eg-2. s="abc", t="bahgdcb"
// idx=[a={1}, b={0,6}, c={5}]
//  i=0 ('a'): prev=1
//  i=1 ('b'): prev=6
//  i=2 ('c'): prev=? (return false)
```

## Java Code:

方法1：

```java
    public class Solution {
        public boolean isSubsequence(String s, String t) {
            if (t == null || s == null || s.length() > t.length()) return false;
            if (s.length() == 0) return true;
            int j = 0;
            for (int i = 0; i < t.length(); ++i) {
                if (j == s.length()) return true;
                if (t.charAt(i) == s.charAt(j)) j++;
            }
            if (j == s.length()) return true;
            return false;
        }
    }
```

方法2：

```java
    // follow up
    public class Solution {
        public boolean isSubsequence(String s, String t) {
            if (t == null || s == null || s.length() > t.length()) return false;
            if (s.length() == 0) return true;
            // 预处理 t，每个字母的出现index序列存入hashmap，然后用s检查的时候，二分法找index
            Map<Character, List<Integer>> indice = new HashMap<>();
            for (int i = 0; i < t.length(); ++i) {
                char c = t.charAt(i);
                if (! indice.containsKey(c)) indice.put(c, new ArrayList<Integer>());
                indice.get(c).add(i);
            }
            // binary search，匹配
            int pre_index = -1;
            for (int i = 0; i < s.length(); ++i) {
                char c = s.charAt(i);
                if (! indice.containsKey(c)) return false;
                List<Integer> list = indice.get(c);
                int this_index = biSearch(list, pre_index);
                if (this_index < 0) return false;
                pre_index = this_index;
            }
            return true;
        }
        // 找到第一个大于 target 的元素，若没有，返回-1
        private int biSearch(List<Integer> list, int target) {
            int p = 0, r = list.size() - 1;
            while (p + 1 < r) {
                int q = (r - p) / 2 + p;
                if (list.get(q) <= target) p = q;
                else r = q;
            }
            if (list.get(p) > target) return list.get(p);
            if (list.get(r) > target) return list.get(r);
            else return -1;
        }
    }
```

