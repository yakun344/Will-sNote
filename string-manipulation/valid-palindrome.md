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
In Java7，we cannot use Stream（）：
```java
    public class Solution {
        public boolean isPalindrome(String s) {
            List<Character> lst = new ArrayList<>();
            for (char c : s.toCharArray()) {
                char a = Character.toUpperCase(c);
                if (Character.isLetter(a) || Character.isDigit(a)) {
                    lst.add(a);
                }
            }
            int left = 0, right = lst.size() - 1;
            while (left < right) {
                if (lst.get(left) != lst.get(right)) return false;
                left++;
                right--;
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

另一种方式，不用regex：
```python
    class Solution:
        """
        @param: s: A string
        @return: Whether the string is a valid palindrome
        """
        def isPalindrome(self, s):
            arr = [c.lower() for c in s if c.isalpha() or c.isdigit()]
            left = 0
            right = len(arr) - 1
            while left < right:
                if not arr[left] == arr[right]:
                    return False
                left += 1
                right -= 1
            return True
```

<br>

___
_update Jan 20, 2018  13:12_

### Update 手动写法
这里通过手动实现了 `isAlphanumeric()` 和 `toUpperCase()`, 过滤出纯字母数字之后存入一个 Stringbuilder，然后比较 `sb.toString() 和 sb.reverse().toString()`;
```java
class Solution {
    public boolean isPalindrome(String s) {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < s.length(); ++i) {
            if (isValid(s.charAt(i))) {
                sb.append(toUpperCase(s.charAt(i)));
            }
        }
        return sb.toString().equals(sb.reverse().toString());
    }
    
    private char toUpperCase(char c) {
        if (c > 'Z') return (char)(c - 'a' + 'A');
        else return c;
    }
    
    private boolean isValid(char c) {
        if ('0' <= c && c <= '9' || 'a' <= c && c <= 'z' || 'A' <= c && c <= 'Z') {
            return true;
        } else {
            return false;
        }
    }
}
```







            