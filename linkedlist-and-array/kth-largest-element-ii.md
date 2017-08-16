## Kth Largest Element II
_update Aug 16,2017  0:02_

---
[LintCode](http://www.lintcode.com/en/problem/kth-largest-element-ii/)

Find K-th largest element in an array. and N is much larger than k.

**Notice**
You can swap elements in the array

**Example:**

    In array [9,3,2,4,8], the 3rd largest element is 4.
    
    In array [1,2,3,4,5], the 1st largest element is 5, 2nd largest element is 4, 3rd largest element is 3 and etc.
    
#### Basic Idea:
Quick Select, 关键在于写出 bug free 的 quickselect。