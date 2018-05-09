## Find All Anagrams in a String （window sliding alg）
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

[这里](https://discuss.leetcode.com/topic/30941/here-is-a-10-line-template-that-can-solve-most-substring-problems) 是一个很棒的分析，有提供window slide解决substring问题的模板。

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
上面的java实现采用的操作顺序是 移动right -- check need -- 移动left，下面的python实现采用 移动right -- 移动left -- check need，感觉更好理解。

这里的need其实就是九章中所讲的 “需要的字母数量减去window中相应字母数量的table的绝对值和”，维持这个need，就可以做到O(1)时间内更新因为移动window造成的所有变化。
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
            # move right bound
            if counter[s[right]] > 0:
                need -= 1
            counter[s[right]] -= 1
            right += 1
            
            # move left bound
            if right - left == len(p) + 1:
                # 这里可以这么判断是因为p中没有的或者少于当前window中的字符都已经在从right进入的时候
                # 减成了负数，等于0说明window中该字符数刚好和p中相等。
                if counter[s[left]] >= 0:
                    need += 1
                counter[s[left]] += 1
                left += 1
            
            # check if need == 0
            if need == 0:
                res.append(left)
        return res
```

---
[Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii/#/description)这道题也可以用window sliding的方法做，可以结合在一起。

<br>

---
_update Jan 27,2018 20:08_
## Update: 更新最新的 sliding window 写法
每层外循环开始时，先用一个while循环判断是否需要移动right，保证该循环结束之后，window size 是符合要求的
，然后做事情，最后右移left；另外，为了一开始的时候可以初始化第一个位置，可以令 right 初始化为 -1;
* ### Java Code:
```java
    class Solution {
        public List<Integer> findAnagrams(String s, String p) {
            List<Integer> res = new ArrayList<>();
            // 记录p中各字母个数，用 count 跟踪剩余字母个数，当count==0时，说明我们找到一个结果
            int[] counter = new int[256];
            for (int i = 0; i < p.length(); ++i) {
                counter[p.charAt(i)]++;
            }
            int count = p.length();
            
            // sliding window
            int left = 0, right = -1;
            final int WINDOWSIZE = p.length();
            while (true) {
                while (right - left + 1 < WINDOWSIZE) {
                    right++;
                    if (right == s.length()) break; 
                    counter[s.charAt(right)]--;
                    if (counter[s.charAt(right)] >= 0) count--;
                }
                if (right == s.length()) break; // 这行限制整个循环结束
                // now window size must be p.length()
                if (count == 0) res.add(left);
                // move left pointer to right
                counter[s.charAt(left)]++;
                if (counter[s.charAt(left)] > 0) count++;
                left++;
            }
            return res;
        }
    }
```

<br>

---
_update May 8,2018 23:24_

### C++ Code
更新一个C++的解法，和上面的Java解法基本思路是一致的，不同之处在于采用直接return的方法跳出循环。

```cpp
    class Solution {
    public:
        vector<int> findAnagrams(string s, string p) {
            int _map[256] = {0};
            for (char c : p) {
                ++_map[c];
            }
                
            vector<int> res;
            int windowSize = p.size();
            int remain = p.size();
            int left = 0, right = -1;
            while (true) {
                while (right - left + 1 < windowSize) {
                    ++right;
                    if (right >= s.size()) return res;
                    if (--_map[s[right]] >= 0) --remain;
                }
                // 此时window size 一定是p.size
                if (remain == 0) res.push_back(left);
                
                // move left pointer to right by 1
                if (++_map[s[left++]] > 0) ++remain;
            }
        }
    };
```















