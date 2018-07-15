## Median of two Sorted Arrays
_update Aug 15, 2017 22:41_

---
[LintCode](http://www.lintcode.com/en/problem/median-of-two-sorted-arrays/#)   
[LeetCode](https://leetcode.com/problems/median-of-two-sorted-arrays/description/)

There are two sorted arrays A and B of size m and n respectively. Find the median of the two sorted arrays.

**Example**

    Given A=[1,2,3,4,5,6] and B=[2,3,4,5], the median is 3.5.

    Given A=[1,2,3] and B=[4,5], the median is 3.

**Challenge **
The overall run time complexity should be O(log (m+n)).

#### Basic Idea:
思路的关键：

1.  把找median转换成求第 `(m+n)/2` 个数的问题；
2.  明确当要找第K个数时，移除两数组中较小的第 k/2 个数及其之前的数是安全的；

于是，我们就可以得到一个 log(n) 时间的解法。

实现的细节：当两个数组不一样长的时候，可能会出现 k/2 比短的那个数组长度还大，此时我们可以假设两个数组都是无限长，超过有效值部分都是 INF；

#### Java Code：
**recursive solution:**
```java
    class Solution {
        /**
         * @param A: An integer array.
         * @param B: An integer array.
         * @return: a double whose format is *.5 or *.0
         */
        public double findMedianSortedArrays(int[] A, int[] B) {
            if ((A.length + B.length) % 2 != 0) {
                // 如果是奇数个，则中位数是第(m+n)/2+1个数
                return findKth(A, 0, B, 0, (A.length + B.length) / 2 + 1);
            } else {
                // 如果是偶数，则是(m+n)/2 和 (m+n)/2+1 的平均值
                return (findKth(A, 0, B, 0, (A.length + B.length) / 2) +
                       findKth(A, 0, B, 0, (A.length + B.length) / 2 + 1)) / 2;
            }
        }
        private double findKth(int[] A, int indexA, int[] B,int indexB, int k) {
            if (indexA >= A.length) {  // A 为空
                return B[indexB + k - 1];
            }
            if (indexB >= B.length) {  // B 为空
                return A[indexA + k - 1];
            }
            if (k == 1) {  // 出口，k表示第k个，从1开始
                return Math.min(A[indexA], B[indexB]);
            }
            // 分别找出两数组中的第 k/2 个数
            double a = Integer.MAX_VALUE;
            double b = Integer.MAX_VALUE;
            if (indexA + k / 2 - 1 < A.length) {
                a = A[indexA + k/2 - 1];
            }
            if (indexB + k / 2 - 1 < B.length) {
                b = B[indexB + k / 2 - 1];
            }
            if (a < b) {
                return findKth(A, indexA + k / 2, B, indexB, k - k / 2);
            } else {
                return findKth(A, indexA, B, indexB + k / 2, k - k / 2);
            }
        }
    }
```
---

_update Dec 18, 2017_

### Update
时隔数月，写出的code更加精简了。这次是 iterative 的 solution：

**Java Code:**
```java
    class Solution {
        public double findMedianSortedArrays(int[] nums1, int[] nums2) {
            int M = nums1.length, N = nums2.length;
            if ((M + N) % 2 == 0) {// even
                return (findKth(nums1, nums2, 0, 0, (M + N) / 2) +
                        findKth(nums1, nums2, 0, 0, (M + N) / 2 + 1)) / 2.0;
            } else { // odd
                return findKth(nums1, nums2, 0, 0, (M + N) / 2 + 1);
            }
        }

        // return the kth number, 1 based
        private int findKth(int[] nums1, int[] nums2, int l, int r, int k) {
            while (k > 1) {
                int mid = k / 2;
                // 越界之后就按照 inf 计算
                int n1 = l + mid - 1 < nums1.length ? nums1[l + mid - 1] : Integer.MAX_VALUE;
                int n2 = r + mid - 1 < nums2.length ? nums2[r + mid - 1] : Integer.MAX_VALUE;
                if (n1 <= n2) {
                    l += mid;
                } else {
                    r += mid;
                }
                k -= mid;
            }
            // 当k==1时，只需要比较当前 l 和 r 选出较小的返回即可
            int n1 = l < nums1.length ? nums1[l] : Integer.MAX_VALUE;
            int n2 = r < nums2.length ? nums2[r] : Integer.MAX_VALUE;
            return Math.min(n1, n2);
        }
    }
```

**Python recursive code:**
```python
    class Solution:
        def findMedianSortedArrays(self, nums1, nums2):
            """
            :type nums1: List[int]
            :type nums2: List[int]
            :rtype: float
            """
            # return the kth number in nums1 and nums2, recursively
            def findKth(l, r, k):
                if l >= self.M: return nums2[r + k - 1]
                elif r >= self.N: return nums1[l + k - 1]
                elif k == 1: return min(nums1[l], nums2[r])
                else:
                    mid = k // 2
                    n1 = nums1[l + mid - 1] if l + mid - 1 < self.M else float('inf')
                    n2 = nums2[r + mid - 1] if r + mid - 1 < self.N else float('inf')
                    if n1 <= n2: return findKth(l + mid, r, k - mid)
                    else: return findKth(l, r + mid, k - mid)


            self.M = len(nums1)
            self.N = len(nums2)
            if (self.M + self.N) % 2 == 0: # even
                return (findKth(0, 0, (self.M + self.N) // 2) +
                        findKth(0, 0, (self.M + self.N) // 2 + 1)) / 2.0;
            else:
                return findKth(0, 0, (self.M + self.N) // 2 + 1);
```

---
_update 2018-07-13 18:59:52_

#### Update: 时间复杂度分析
如果我们需要得到两个 sorted array 中第k个数字，由于每次iteration我们都会丢掉上次丢掉数字一半的数字 `(第一次 k/2, 然后 k/4, k/8 ...)`，这样的时间复杂度应该是 `log(k)`。然后因为我们最终的 `k == (lenA + lenB) / 2`，所以总的时间复杂度为 `O(log((lenA + lenB)/2)) == O(log(lenA + lenB))`。
