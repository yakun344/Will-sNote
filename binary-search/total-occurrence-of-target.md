## Total Occurrence of Target
_update Aug 17, 2017  2:04_

---
[LintCode](http://www.lintcode.com/en/problem/total-occurrence-of-target/)

Given a target number and an integer array sorted in ascending order. Find the total number of occurrences of target in the array.

**Example**

    Given [1, 3, 3, 4, 5] and target = 3, return 2.
    
    Given [2, 2, 3, 4, 6] and target = 4, return 1.
    
    Given [1, 2, 3, 4, 5] and target = 6, return 0.

**Challenge **
Time complexity in O(logn)

#### Basic Idea:
用二分法，先找第一个的index，再找最后一个的index，然后结果就是 `last - first + 1`;

#### Java Code:
```java
    public class Solution {
        /**
         * @param A an integer array sorted in ascending order
         * @param target an integer
         * @return an integer
         */
        public int totalOccurrence(int[] A, int target) {
            // Write your code here
            // find the first one
            if (A == null || A.length == 0) return 0;
            int p = 0, r = A.length - 1;
            while (p + 1 < r) {
                int q = (r - p) / 2 + p;
                if (A[q] < target) p = q;
                else r = q;
            }
            int first = -1;
            if (A[p] == target) first = p;
            else if (A[r] == target) first = r;
            else return 0;
            p = first;
            r = A.length - 1;
            // find the last one
            while (p + 1 < r) {
                int q = (r - p) / 2 + p;
                if (A[q] > target) r = q;
                else p = q;
            }
            int last = -1;
            if (A[r] == target) last = r;
            else last = p;
            return last - first + 1;
        }
    }
```