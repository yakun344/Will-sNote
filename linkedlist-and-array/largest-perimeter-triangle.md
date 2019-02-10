# Largest Perimeter Triangle
_update Jan 13 2019, 13:51_

---
[LeetCode](https://leetcode.com/problems/largest-perimeter-triangle/)

Given an array A of positive lengths, return the largest perimeter of a triangle with non-zero area, formed from 3 of these lengths.

If it is impossible to form any triangle of non-zero area, return 0.


**Example 1:**

    Input: [2,1,2]
    Output: 5

**Example 2:**

    Input: [1,2,1]
    Output: 0

**Example 3:**

    Input: [3,2,3,4]
    Output: 10

**Example 4:**

    Input: [3,6,2,3]
    Output: 8
 
**Note:**

1. `3 <= A.length <= 10000`
2. `1 <= A[i] <= 10^6`

<br/>

### Basic Idea:
首先需要明确一个三边组成三角形的性质：“两短边之和大于第三边是三边可构成三角形的充分必要条件”。于是我们知道，如果长边c固定，我们只要检查余下长度小于等于c的所有边中最长的两条，看它们的长度之和是否大于第三边即可。于是我们只要先对输入的数组排序，然后进行一个 `O(n)`的扫描就可以了。时间复杂度 `O(nlogn)`.

#### Java Code:
```java
class Solution {
    public int largestPerimeter(int[] A) {
        // 由大到小排序
        Arrays.sort(A);
        for (int i = A.length - 1; i >= 2; --i) {
            if (isValid(A[i], A[i - 1], A[i - 2])) return A[i] + A[i - 1] + A[i - 2];
        }
        return 0;
    }

    // 输入保证 a >= b && a >= c
    private boolean isValid(int a, int b, int c) {
        return b + c > a;
    }
}
```
