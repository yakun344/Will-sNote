## Longest Common Prefix
_update Jun 29, 2017, 22:42:43_

---
[leetcode](https://leetcode.com/problems/longest-common-prefix/#/solutions)

Write a function to find the longest common prefix string amongst an array of strings.

If there is no common prefix, return an empty string "".

**Example 1:**

    Input: ["flower","flow","flight"]
    Output: "fl"
    Example 2:
    
    Input: ["dog","racecar","car"]
    Output: ""
    Explanation: There is no common prefix among the input strings.
    
**Note:**

All given inputs are in lowercase letters a-z.

<br>

#### 思路 (排序比首尾)：
首先想到的是用BFS解决 shortest path 的方法，即每次比较每个string的同一位置的character看是否相同，不同则返回当前res。但是这样还是比较慢，更好的方法是先按字典顺序排序各String,然后每次只要比较首末两个string。

##### BFS 的java code：
从左往右，比较每个string在同一个位置的字符是否相等, `O(L*N)` 时间复杂度，其中 L 为 Common Prefix 的长度，N 为 String 的数量。
```java
    public class Solution {
        public String longestCommonPrefix(String[] strs) {
            if (strs.length == 0) return "";
            StringBuilder sb = new StringBuilder();
            int currLength = 0;
            char currChar = '0';
            while (true) {
                if (strs[0].length() > currLength) {
                    currChar = strs[0].charAt(currLength);
                } else return sb.toString();
                for (String s : strs) {
                    if (s.length() <= currLength) return sb.toString();
                    if (s.charAt(currLength) != currChar) return sb.toString();
                }
                currLength++;
                sb.append(currChar);
            }
        }
    }
```

##### BFS CPP Code:
```cpp
    class Solution {
    public:
        string longestCommonPrefix(vector<string>& strs) {
            if (strs.empty()) {
                return "";
            }
            string prefix;
            int curr_idx{0};
            while (true) {
                char curr_char{0};
                for (const string& s : strs) {
                    if (s.size() == curr_idx) {
                        goto endLoop;
                    } else if (curr_char == 0) {
                        curr_char = s[curr_idx];
                    } else if (curr_char != s[curr_idx]) {
                        goto endLoop;
                    }
                }
                prefix += curr_char;
                curr_idx++;
            }
    endLoop:
            return prefix;
        }
    };
```

##### sort 的 python code：
先对所有 String 排序，然后每次从左到右比较首末两个string相应index的char是否相等。 时间复杂度 `O(L + NlogN)`。

```python
    class Solution(object):
        def longestCommonPrefix(self, strs):
            """
            :type strs: List[str]
            :rtype: str
            """
            if len(strs) == 0: return ''
            if len(strs) == 1: return strs[0]
            strs.sort()
            ret = ''
            s1 = strs[0]
            s2 = strs[-1]
            i = 0
            while True:
                if len(s1) <= i or len(s2) <= i: return ret
                if s1[i] == s2[i]:
                    ret += s1[i]
                    i += 1
                else: return ret
```