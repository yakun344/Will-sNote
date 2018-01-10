## Permutation in String
_update Jan 10, 2018 15:54_

---
[LeetCode](https://leetcode.com/problems/permutation-in-string/description/)

Given two strings s1 and s2, write a function to return true if s2 contains the permutation of s1. In other words, one of the first string's permutations is the substring of the second string.

**Example 1:**
    
    Input:s1 = "ab" s2 = "eidbaooo"
    Output:True
    Explanation: s2 contains one permutation of s1 ("ba").

**Example 2:**

    Input:s1= "ab" s2 = "eidboaoo"
    Output: False

**Note:**
  
  1. The input strings only contain lower case letters.
  2. The length of both given strings is in range [1, 10,000].
  
<br>

### Basic Idea
**Sliding window**， window size 为 `len(s1)`，每次移动 window 后检查当前 window 内的字符串是否和 s1 有着完全相同的字母组成，是的话则返回 true，例如：
``` 
    s1:  abdc
    s2:  aksdjl | bdac | kljdl
          ->    l      r    ->
    这种情况的时候，window中的 bdac 有着和 s1 相同的字母组成，返回 true；
```
具体地，判断窗口内字母和 s1 是否组成相同的话，可以先用一个 `int[26] map` 存储 s1 的每个字母的数量，并且维持一个 count 初始化为 `len(s1)`。随着窗口的移动，对 map 数组和 count 进行更新，直到 count==0 时，返回 true.

#### Java Implementation
在右移left和right边界的时候要注意，右移left时候是否造成了map中对应数值变为正数，右移right的时候是否造成map中数值变为0或者正数。如下图所示：
```python
    s1:  a b c a b  ,   map: [a b c]   , count = 5
    s2:  gheabf              [2 2 1]
    
    当窗口移动至如下位置时：      , map:                  , count = 3  
            g | h e a b | f         [a  b  c  e  h]  
              l   ->    r           [1  1  1 -1 -1]
              
    即当我们右移left的时候，如果 map[s2[left]]+1 > 0, 我们才需要 count+++;
    当右移right的时候，如果 map[s2[right]] -1 >= 0, 我们才需要 count--;
```

```java
class Solution {
    public boolean checkInclusion(String s1, String s2) {
        // 初始化 map 数组，存储 s1 每个字母出现的次数
        int[] map = new int[26];
        for (int i = 0; i < s1.length(); ++i) {
            map[s1.charAt(i) - 'a'] += 1;
        }
        int count = s1.length(); // count 初始化为 len(s1), 当 count==0 时返回 true
        
        // sliding window, right 初始化为-1，这样 0-th 会被第一次 right++ 时候更新
        // 也表示 window size 初始化为 0
        int left = 0, right = -1; 
        while (right < s2.length()) {
            // 如果window size小于s1的长度，则right++
            while (right - left + 1 < s1.length()) {
                right++;
                if (right == s2.length()) return false; // failed, 没找到，结束循环
                char c = s2.charAt(right);
                map[c - 'a']--;
                // 如果原本s1中仍有尚未匹配的 c 字母，减一之后该位置最少也为 0
                if (map[c - 'a'] >= 0) count--; 
            }
            if (count == 0) { // 如果 window size == len(s1), 检查此时 count 是否为 0 
                return true;
            }
            // left 右移
            char c = s2.charAt(left);
            map[c - 'a']++;
            if (map[c - 'a'] > 0) count++; // 说明left弹出window之后，多了一个 c 字母需匹配
            left++;
        }
        return false;
    }
}
```

#### Python Code:
```python
class Solution:
    def checkInclusion(self, s1, s2):
        """
        :type s1: str
        :type s2: str
        :rtype: bool
        """
        charMap = [0] * 26
        for c in s1:
            charMap[ord(c) - ord('a')] += 1
        count = len(s1)
        
        left, right = 0, -1
        while right < len(s2):
            while right - left + 1 < len(s1):
                right += 1
                if right >= len(s2): 
                    return False
                charMap[ord(s2[right]) - ord('a')] -= 1
                if charMap[ord(s2[right]) - ord('a')] >= 0:
                    count -= 1
            if count == 0:
                return True
            charMap[ord(s2[left]) - ord('a')] += 1
            if charMap[ord(s2[left]) - ord('a')] > 0:
                count += 1
            left += 1
            
        return False
```


