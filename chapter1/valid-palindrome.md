## Valid Palindrome
_update Jul 2, 2017 19:04_

---
[leetcode](https://leetcode.com/problems/valid-palindrome/#/solutions)

Given a string, determine if it is a palindrome, considering only alphanumeric characters and ignoring cases.
    
    For example,
    "A man, a plan, a canal: Panama" is a palindrome.
    "race a car" is not a palindrome.
    
**Note:**
Have you consider that the string might be empty? This is a good question to ask during an interview.

For the purpose of this problem, we define empty string as valid palindrome.

#### Basic Idea:
难点其实是如何去掉多余字符只保留字母和数字。可以有很多方法，可以用java stream 的 filter + lambda expression， 也可以考虑用 regex replaceAll。在python中，用regex进行pattern replacement 要使用 re.sub(pattern, repl, string)。

#### Java Code Using stream.filter();
```java
    public class Solution {
        public boolean isPalindrome(String s) {
            if (s.length() == 0) return true;
            int[] source = s.toLowerCase().chars().filter(c -> ('a' <= c && c <= 'z') || ('0' <= c && c <= '9')).toArray();
            for (int i = 0; i < source.length / 2; ++i) {
                if (source[i] != source[source.length - 1 - i]) return false;
            }
            return true;
        }
    }
```

#### Python Code Using Regex:
```python
    class Solution(object):
        def isPalindrome(self, s):
            """
            :type s: str
            :rtype: bool
            """
            s = re.sub('[^0-9a-zA-Z]', '', s).lower()
            return s == s[::-1]
```