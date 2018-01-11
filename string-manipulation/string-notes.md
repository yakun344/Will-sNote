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

#### 1. Char Removal
```
    input：  ____aaa___bb_cc__
    output: aaa_bb_cc
```
即去掉input string的首位空格，并且每个词之前只保留一个空格。

