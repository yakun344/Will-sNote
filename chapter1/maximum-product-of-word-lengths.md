## Maximum Product of Word Lengths
_update Aug 5,2017 0:37_

---
[LeetCode](https://leetcode.com/problems/maximum-product-of-word-lengths/description/)

Given a string array words, find the maximum value of length(word[i]) * length(word[j]) where the two words do not share common letters. You may assume that each word will contain only lower case letters. If no such two words exist, return 0.

**Example 1:**
    
    Given ["abcw", "baz", "foo", "bar", "xtfn", "abcdef"]
    Return 16
    The two words can be "abcw", "xtfn".

**Example 2:**

    Given ["a", "ab", "abc", "d", "cd", "bcd", "abcd"]
    Return 4
    The two words can be "ab", "cd".

**Example 3:**

    Given ["a", "aa", "aaa", "aaaa"]
    Return 0
    No such pair of words.
    
#### Basic Idea:
这道题要求所有没有相同字符的字符串长度乘积最大值，没有什么简便办法，只有考虑每一对，然后判断是否有相同字符。

判断**两字符串是否有相同字符**可以使用 Bit Map，因为 int 有 32 位，足以作为一个set来存放26位的信息（每个bit的 0 1 相当于一个字母 存在 或 不存在）。

#### Java Code:
```java
    // 类似思路，但是用bit mask来完成判断是否有重复字母的工作，相当于给每个word都maintain了一个set，然后进行与比较
    // 这里用bit map，因为int有32位，足以保存26位信息
    public class Solution {
        public int maxProduct(String[] words) {                
            if (words == null || words.length == 0) return 0;
            // 生成bit map
            int[] bitMap = new int[words.length];
            for (int i = 0; i < words.length; ++i) {
                String word = words[i];
                for (int j = 0; j < word.length(); ++j) {
                    bitMap[i] |= (1 << word.charAt(j) - 'a');
                }
            }
            // 两两求积，判断是否相同
            int ret = 0;
            for (int i = 0; i < words.length - 1; ++i) {
                for (int j = i + 1; j < words.length; ++j) {
                    String w1 = words[i];
                    String w2 = words[j];
                    int product = w1.length() * w2.length();
                    if (product <= ret) continue; // pruning
                    if ((bitMap[i] & bitMap[j]) == 0) {
                        ret = product;
                    }
                }
            }
            return ret;
        }
    }
```