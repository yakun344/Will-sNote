## Find All Anagrams in a String
_update Jun 29, 2017_

---
[leetcode](https://leetcode.com/problems/find-all-anagrams-in-a-string/#/description)
Given a string s and a non-empty string p, find all the start indices of p's anagrams in s.

Strings consists of lowercase English letters only and the length of both strings s and p will not be larger than 20,100.

The order of output does not matter.
        
**Example 1:**
        
        Input:
        s: "cbaebabacd" p: "abc"
        
        Output:
        [0, 6]
        
        Explanation:
        The substring with start index = 0 is "cba", which is an anagram of "abc".
        The substring with start index = 6 is "bac", which is an anagram of "abc".
**Example 2:**
        
        Input:
        s: "abab" p: "ab"
        
        Output:
        [0, 1, 2]
        
        Explanation:
        The substring with start index = 0 is "ab", which is an anagram of "ab".
        The substring with start index = 1 is "ba", which is an anagram of "ab".
        The substring with start index = 2 is "ab", which is an anagram of "ab".
        
#### 思路
最简单的方法就是把pattern string的char进行统计，建counting map，然后brute force，但是这样做的速度太慢。一个优化的方法是使用 **sliding window algorithm** ，也就是 maintain 一个长度等于`len(pattern)`的 window， 每次向右移动一格，统计进入和离开 window的char，用一个 **int need**变量记录当前还需要match的char数量，如果need==0，则说明找到一个subString。
[这里](https://discuss.leetcode.com/topic/30941/here-is-a-10-line-template-that-can-solve-most-substring-problems)有一个window slide解决substring问题的模板。

#### Java code：
    ```java
    public class Solution {
        public List<Integer> findAnagrams(String s, String p) {
            if (s==null || p == null || s.length() == 0 || p.length() == 0) return new ArrayList<Integer>();
            // count each char in p using a counting map
            int[] counter = new int[26];
            for (char c : p.toCharArray()) {
                counter[c - 'a']++;
            }
            // using a sliding window with size == len(p) to traverse s, 
            // counting each char goes in and out the window
            List<Integer> res = new ArrayList<>();
            int left = 0;
            int right = 0;
            int need = p.length(); // first, we need to find len(p) chars to match
            while (right < s.length()) {
                if (counter[s.charAt(right) - 'a'] > 0) {
                    need--; // means we have found a match char
                }
                // do these every time, need to restore counter when chars goes out of the window
                counter[s.charAt(right) - 'a']--;
                right++; 
                // when need == 0, means we got a match subString, left is its start index
                if (need == 0) {
                    res.add(left);
                }
                // Then we need to restore need and counter when chars goes out.
                if (right - left == p.length()) { 
                    // because we just add 1 to right, so the window is actually from left to right - 1 now
                    if (counter[s.charAt(left) - 'a'] >= 0) {
                        need++;                    
                    }
                    counter[s.charAt(left) - 'a']++;
                    left++;
                }
            }
            return res;
        }
    }
```
#### Python code：
```python
    class Solution(object):
        def findAnagrams(self, s, p):
            """
            :type s: str
            :type p: str
            :rtype: List[int]
            """
            res = []
            if not s or not p or len(s) == 0 or len(p) == 0:
                return res
            counter = collections.Counter(p)
            left = 0
            right = 0
            need = len(p)
            while right < len(s):
                # check right and move right by one
                if counter[s[right]] > 0:
                    need -= 1
                counter[s[right]] -= 1
                right += 1
                # if need goes to 0, add left to res
                if need == 0:
                    res.append(left)
                # when window size goes to len(p), start moving left to right by one
                if right - left == len(p):
                    if counter[s[left]] >= 0:
                        need += 1
                    counter[s[left]] += 1
                    left += 1
            return res
```