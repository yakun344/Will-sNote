# Reorder Array
_update Feb 16,2018 16:45_

---
Given an array of elements, reorder it as follow:
```python
{ N1, N2, N3, …, N2k } → { N1, Nk+1, N2, Nk+2, N3, Nk+3, … , Nk, N2k }

{ N1, N2, N3, …, N2k+1 } → { N1, Nk+1, N2, Nk+2, N3, Nk+3, … , Nk, N2k, N2k+1 }
```

Try to do it in place.

**Assumptions**

The given array is not null

**Examples**
```python
{ 1, 2, 3, 4, 5, 6} → { 1, 4, 2, 5, 3, 6 }

{ 1, 2, 3, 4, 5, 6, 7, 8 } → { 1, 5, 2, 6, 3, 7, 4, 8 }

{ 1, 2, 3, 4, 5, 6, 7 } → { 1, 4, 2, 5, 3, 6, 7 }
```

## Basic Idea:
* ### 思路 1：O(n) space
这种思路非常简单，用两个指针分别从 `0` 和 `len/2` 的位置开始向右扫描，将结果按顺序存入 ret 数组中返回即可。
  * #### Java Code:
  ```java 
      public class Solution {
        public int[] reorder(int[] array) {
          if (array == null || array.length < 4) return array; 
          int[] ret = new int[array.length];
          int i = 0, j = array.length / 2;
          while (i < array.length / 2) {
            ret[2 * i] = array[i];
            ret[2 * i + 1] = array[j];
            i++;
            j++;
          }
          if (j < array.length) ret[ret.length - 1] = array[array.length - 1];
          return ret;
        }
      }
  ```
  
* ### 思路 2：逆向merge，inplace
