# Excel Sheet Column Title

_update Jan 20,2018 16:11_

[LeetCode](https://leetcode.com/problems/excel-sheet-column-title/description/)

Given a positive integer, return its corresponding column title as appear in an Excel sheet.

**For example:**

```text
1 -> A
2 -> B
3 -> C
...
26 -> Z
27 -> AA
28 -> AB 
```

## Basic Idea:

这道题类似进制转换，但难点在于 Excel column 中是没有 0 的。所以，每次当我们需要取出个位的时候，要做 `(n - 1) % 26`, 相当于将其变为 0 based。而当我们要消除这一位，准备考虑下一位的时候，要做 `n = (n - 1) / 26`, 因为当 `n=26` 时，第一步我们希望 `(n-1)%26 = 25 ---> 'Z'`, 然后 `n = (n-1)/26 = 0 ----> 结束`。

## Java Code:

```java
class Solution {
    public String convertToTitle(int n) {
        StringBuilder sb = new StringBuilder();
        while (n > 0) {
            char digit = (char)((n - 1) % 26 + 'A');
            sb.append(digit);
            n = (n - 1) / 26;
        }
        return sb.reverse().toString();
    }
}
```

