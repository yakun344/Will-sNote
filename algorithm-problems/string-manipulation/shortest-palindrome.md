# Shortest Palindrome

_update Sep 5 2018, 23:11_

Given a string s, you are allowed to convert it to a palindrome by adding characters in front of it. Find and return the shortest palindrome you can find by performing this transformation.

Example 1:

```text
Input: "aacecaaa"
Output: "aaacecaaa"
```

Example 2:

```text
Input: "abcd"
Output: "dcbabcd"
```

## Basic Idea:

在一个字符串前添加一段，使得其形成一个回文字符串，求最短的回文字符串。首先，如果将字符串s本身reverse之后接到前面，一定会形成一个回文串，那么如何让它尽可能短呢，就是要先找到从左端开始的最长回文串，然后只要将该最左边回文串右边的部分reverse后接在前面就可以了。如何 O\(n\) 时间内找到从最左边开始的最长回文串呢？我们可以将其reverse后接在最后，中间用特殊字符分隔，例如：`abcbaggg -> abcbaggg#gggabcba`，然后利用KMP算法的思路，求出KMP所需的next数组，我们就会得到 `00001000000012345`，则可知因为 `next` 数组最后一个数字为 5 ，说明以该位置结束的长度为5的substring和最左边的相同。由于`#`右部分是由s reverse而来，我们就得知了从 s 最左端开始的最长回文串长度为 5. 接下来只要复制 s.substring\(5\), reverse 之后接到前面就可以了。最终结果为 `gggabcbaggg` 。

### Java Code:

```java
  class Solution {
      public String shortestPalindrome(String s) {
          String t = s + "#" + new StringBuilder().append(s).reverse().toString();
          int[] next = new int[t.length()];
          int i = 0, j = 1;
          while (j < t.length()) {
              if (t.charAt(i) == t.charAt(j)) {
                  next[j++] = ++i;
              } else {
                  if (i == 0) {
                      j++;
                  } else {
                      i = next[i - 1];
                  }
              }
          }
          int sufix = next[next.length - 1];
          return new StringBuilder().append(s.substring(sufix)).reverse().toString() + s;
      }
  }
```

