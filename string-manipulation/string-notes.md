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
      input:  aaa_bb_cc
      return: a_b_c
```
还是two pointers。

```
      input:  abbacd
      output: cd
      
      即重复串消除之后，如果左右部分合并生成了新的重复串，继续消除
```
用 stack。

  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  

