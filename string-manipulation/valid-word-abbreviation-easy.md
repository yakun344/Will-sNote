# Valid Word Abbreviation
_update Jan 27,2018  12:05_

---
[LeetCode](https://leetcode.com/problems/valid-word-abbreviation/description/)

Given a non-empty string s and an abbreviation abbr, return whether the string matches with the given abbreviation.

A string such as "word" contains only the following valid abbreviations:

    ["word", "1ord", "w1rd", "wo1d", "wor1", "2rd", "w2d", "wo2", "1o1d", 
     "1or1", "w1r1", "1o2", "2r1", "3d", "w3", "4"]
     
Notice that only the above abbreviations are valid abbreviations of the string "word". Any other string is not a valid abbreviation of "word".

**Note:**   
Assume s contains only lowercase letters and abbr contains only lowercase letters and digits.

**Example 1:**

    Given s = "internationalization", abbr = "i12iz4n":
    Return true.
    
**Example 2:**

    Given s = "apple", abbr = "a2e":
    Return false.
    
<br>

## Basic Idea:
基本思路还是双指针分别指向两string，然后 linear scan。如果在abbr中遇到数字，则继续读出所有数字，然后令指向word的指针右移相应位数，否则比较左右指针指向的字母是否相同。要注意数字的第一位不能为0.

* ### Java Code:
```java
    class Solution {
        public boolean validWordAbbreviation(String word, String abbr) {
            if (word.length() == 0 && abbr.length() == 0) return true;
            if (word.length() == 0 || abbr.length() == 0) return false;
            int i = 0, j = 0;
            while (i < word.length() && j < abbr.length()) {
            // i，j 分别扫描 word 和 abbr
            // 每次先检查 j 指向是否为数字，如果是，读出所有数字，并令j指向数字最后一位右边，读出数字之后
            // 将 i 右移相应数字位；
            // 如果 j 指向不是数字，则比较 i，j 所指是否相同字符，若不是，直接返回false
                char c1 = word.charAt(i);
                char c2 = abbr.charAt(j);
                if (c2 > '0' && c2 <= '9') { // first digit cannot be '0'
                    int start = j;
                    // 读所有数字，存入number中
                    while (j < abbr.length() && abbr.charAt(j) >= '0' && abbr.charAt(j) <= '9') {
                        j++;
                    }
                    int number = Integer.parseInt(abbr.substring(start, j));
                    i += number;
                } else if (c1 == c2) {
                    i++;
                    j++;
                } else {
                    return false;
                }
            }
            if (i != word.length() || j != abbr.length()) return false;
            else return true;
        }
    }
```








