## Regular Expressions
Regex 是处理string非常有力的工具。使用Java可以很方便地利用regex。这里收录一些和regex相关的题目。

`java`的`string.matches(regex)`会直接匹配到整个字符串的最后，返回`true or false``. 但是`python`的`re.match(regex, string)`则是从头开始尽力匹配，如果有一部分匹配，则返回一个匹配对象，否则返回None。所以使用`python`的时候需要在regex的最后加上`$`来匹配结尾。

---
_Jun 9, 2017 update_ 
### [459, Repeated Substring Pattern](https://leetcode.com/problems/repeated-substring-pattern/#/description)
Given a non-empty string check if it can be constructed by taking a substring of it and appending multiple copies of the substring together. You may assume the given string consists of lowercase English letters only and its length will not exceed 10000.
#### Solution:
    1 用循环brute force 硬找，利用字串长度是source 长度的约数简化运算，O(n^2);
    2 复制一遍source头尾相接，去掉首尾两个Character，看剩下的字符串中是否contians source，O(n);
    3 利用KMP的最长公共前后缀table，O(n);
    4 利用regex
    
Regex code:
```java
    // java
     public boolean repeatedSubstringPattern(String s) {
         Pattern p = Pattern.compile("(\\w+)\\1+");
         Matcher m = p.matcher(s);
         return m.matches();
     }
     // 或者直接用 return s.matches("regex"); 这样就是java one line。
``` 
### [520, Detect Capital](https://leetcode.com/problems/detect-capital/#/description)
Given a word, you need to judge whether the usage of capitals in it is right or not.

We define the usage of capitals in a word to be right when one of the following cases holds:

All letters in this word are capitals, like "USA".
All letters in this word are not capitals, like "leetcode".
Only the first letter in this word is capital if it has more than one letter, like "Google".
Otherwise, we define that this word doesn't use capitals in a right way.

```java
    // java
    public boolean detectCapitalUse(String word) {
        return word.matches("[A-Z]+|[A-Z][a-z]*|[a-z]+");
    }
```
```python
    # python
    def detectCapitalUse(self, word):
        """
        :type word: str
        :rtype: bool
        """
        return bool(re.match('([A-Z]+|[A-Z][a-z]*|[a-z]+)$', word))
```