## String Notes
_update Jan 10, 2018  20:16_

---
### 概述
String 的常见问题可以归纳为如下几类：

1.  Char Removal
2.  De-duplication
3.  replace
4.  Reversal (swap)
5.  Substring
6.  shuffling
7.  Permutation
8.  Decoding/Encoding
9.  DP, longest substring
10. matching
<br>
#### 1. Char Removal
```
    input：  ____aaa___bb_cc__
    output: aaa_bb_cc

    即去掉input string的首位空格，并且每个词之前只保留一个空格。
```
基本思想是使用 two-pointers 的方法，i 和 j 分别对应左右指针。维持已经筛选过的部分在 i 的左边(i指向最后一个位置)，用 j 一路向右扫描，需要更新的时候就把需要的字符赋值给 ++i；<br>
**Java Code:**   
```java
    public class Solution {
      public String removeSpaces(String input) {
        char[] arr = input.toCharArray();
        int i = -1, j = 0;
        // 如果见到单词，则设为true，如果见到空格且flag为true，表示刚离开单词，补空格
        boolean flag = false; 
        for (j = 0; j < arr.length; ++j) {
          if (arr[j] != ' ') {
            flag = true;
            arr[++i] = arr[j];
          } else if (flag) {
            // 刚离开一个单词 
            arr[++i] = ' ';
            flag = false;
          }
        }
        if (i == -1) return "";
        StringBuilder sb = new StringBuilder();
        // 如果input以空格结尾，此时 i 指向最后一个多余空格，否则指向最后一个字母
        for (int k = 0; k <= i; ++k) {
          sb.append(arr[k]);
        }
        if (sb.charAt(sb.length() - 1) == ' ') sb.deleteCharAt(sb.length() - 1);
        return sb.toString();
      }
    }
```
<br>
#### 2. De-duplicate
```  
    (1)
      input:  aaa_bb_cc
      return: a_b_c
```
还是two pointers。
```
    (2)
      input:  abbacd
      output: cd
      
      即重复串消除之后，如果左右部分合并生成了新的重复串，继续消除
```
用 stack。
<br>
#### 3. Str-str
经典题目，求一个string是否是另一个string的substring。基本方法是 `O(len(small string)^2)` 的时间复杂度，还有 KMP 和 Robin Karp 两个常见的 `O(n)` 时间的实现，但是一般来说不会考实现。
```java
    public class Solution {
      public int strstr(String large, String small) {
        if ("".equals(small)) return 0;
        for (int k = 0; k < large.length(); ++k) {
          int i = 0;
          while (i < small.length() && k + i < large.length()) {
            if (large.charAt(k + i) != small.charAt(i)) break;
            i++;
          }
          if (i == small.length()) return k;
        }
        return -1;
      }
    }
```
<br>
#### 4. Replacement
```
    Assumptions
    
    input, S and T are not null, S is not empty string
    Examples
    
    input = "appledogapple", S = "apple", T = "cat", input becomes "catdogcat"
    input = "dodododo", S = "dod", T = "a", input becomes "aoao"
```
用一个指针扫描input string，用一个StringBuilder保存结果， 每次发现接下来的部分和 S match后，就append T，指针跳过len(S)个位置，否则的话就append当前字符，指针右移一位。
```java
    public class Solution {
      public String replace(String input, String s, String t) {
        int r = 0;
        StringBuilder sb = new StringBuilder();
        while (r < input.length()) {
          if (match(input, s, r)) {
            sb.append(t);
            r += s.length();
          } else {
            sb.append(input.charAt(r++));
          }
        }
        return sb.toString();
      }
      private boolean match(String input, String s, int start) {
        if (start + s.length() - 1 >= input.length()) return false;
        int i = 0;
        while (i < s.length()) {
          if (input.charAt(start + i) != s.charAt(i)) return false;
          i++;
        }
        return true;
      }
    }
```
<br>
#### 5. Decoding-Encoding, Compress
[LeetCode: String Compression](https://leetcode.com/problems/string-compression/description/)  
整体思路是扫描input string，每次检查当前字母连续重复个数，然后相应inplace地去重新赋值。具体地，维持 l 和 r 两个指针，l 指向要返回的结果的最后一个位置，r 作为探测指针向右扫描。详见下面的comments。
```java
    class Solution {
        public int compress(char[] chars) {
            int l = -1, r = 0;
            while (r < chars.length) {
                int num = count(chars, r);
                chars[++l] = chars[r]; // 先复制字母，然后添加数字
                if (num > 1) {
                    // 从 l+1 开始添加数字，并且更新l的值，为数字最后一位的index
                    l = insertNumber(chars, l + 1, num);
                }
                r += num;
            }
            return l + 1;
        }
        
        // insert given number in chars at start, return new left pointer
        private int insertNumber(char[] chars, int start, int num) {
            String numString = String.valueOf(num);
            for (int i = 0; i < numString.length(); ++i) {
                chars[start + i] = numString.charAt(i);
            }
            return start + numString.length() - 1;
        }
        
        // 返回连续出现的个数
        private int count(char[] chars, int start) {
            int ret = 1;
            char c = chars[start];
            start += 1;
            while (start < chars.length) {
                if (chars[start++] == c) ret++;
                else break;
            }
            return ret;
        }
    }
```

 
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  

