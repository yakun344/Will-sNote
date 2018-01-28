# Valid Word Square (easy)
_update Jan 27,2018  19:10_

---
[LeetCode](https://leetcode.com/problems/valid-word-square/description/)

Given a sequence of words, check whether it forms a valid word square.

A sequence of words forms a valid word square if the kth row and column read the exact same string, where `0 ≤ k < max(numRows, numColumns)`.

**Note:**    

1. The number of words given is at least 1 and does not exceed 500.
2. Word length will be at least 1 and does not exceed 500.
3. Each word contains only lowercase English alphabet a-z.

**Example 1:**
  
    Input:
    [
      "abcd",
      "bnrt",
      "crmy",
      "dtye"
    ]
    
    Output:
    true
    
    Explanation:
    
    The first row and first column both read "abcd".
    The second row and second column both read "bnrt".
    The third row and third column both read "crmy".
    The fourth row and fourth column both read "dtye".
    
    Therefore, it is a valid word square.

**Example 2:**

    Input:
    [
      "abcd",
      "bnrt",
      "crm",
      "dt"
    ]
    
    Output:
    true
    
    Explanation:
    The first row and first column both read "abcd".
    The second row and second column both read "bnrt".
    The third row and third column both read "crm".
    The fourth row and fourth column both read "dt".
    
    Therefore, it is a valid word square.
    
**Example 3**:

    Input:
    [
      "ball",
      "area",
      "read",
      "lady"
    ]
    
    Output:
    false
    
    Explanation:
    The third row reads "read" while the third column reads "lead".
    
    Therefore, it is NOT a valid word square.
    
<br>

## Basic Idea:
基本思想就是用两层for循环，逐行逐列去比较每个字符是否相等，另外要注意长度问题；

* #### Java Code:
在实现中，用一个helper function: get() 来负责处理查看某坐标元素的问题，在主程序中则可以专注比较每行每列。
```java
    class Solution {
        public boolean validWordSquare(List<String> words) {
            if (words.size() == 0) return true;
            int R = words.size();
            for (int r = 0; r < R; ++r) {
                for (int c = 0; c < words.get(r).length(); ++c) {
                    Character c1 = get(words, r, c);
                    Character c2 = get(words, c, r);
                    if (c1 == null && c2 == null) {
                        continue;
                    } else if (c1 == null || c2 == null) {
                        return false;
                    } else if (! c1.equals(c2)) {
                        return false;
                    }
                }
            }
            return true;
        }
        
        private Character get(List<String> words, int r, int c) {
            if (words.size() <= r) return null;
            String row = words.get(r);
            if (row.length() <= c) return null;
            else return row.charAt(c);
        }
    }
```

















