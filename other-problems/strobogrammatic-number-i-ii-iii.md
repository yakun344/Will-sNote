# Strobogrammatic Number 系列
_udpate Jan 23,2018  19:16_

---
## Strobogrammatic Number I  
[LeetCode](https://leetcode.com/problems/strobogrammatic-number/description/)  
A strobogrammatic number is a number that looks the same when rotated 180 degrees (looked at upside down). Write a function to determine if a number is strobogrammatic. The number is represented as a string.  

**For example**   
the numbers "69", "88", and "818" are all strobogrammatic.
<br>
* ### Solution
符合条件的数一定只包含 `[0, 1, 6, 8, 9]` 这几个数，而且一定左右成对出现，可以先把每一对存入 HashMap，然后用双指针从左右向中间检查。
  * #### Java Code：
  ```java
  