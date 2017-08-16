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
**思路1：使用 Quick Selection**
Quick Select, 关键在于写出 bug free 的 quickselect。

Java Code：
```java
    class Solution {
        /**
         * @param nums an integer unsorted array
         * @param k an integer from 1 to n
         * @return the kth largest element
         */
        public int kthLargestElement2(int[] nums, int k) {
            if (nums == null || nums.length == 0) return -1;
            return quickSelect(nums, k - 1);
        }
        // 这个函数是精髓，使用while循环避免过程中需要改变k的值
        private int quickSelect(int[] nums, int k) {
            int p = 0, r = nums.length - 1;
            while (p < r) {
                int q = partition(nums, p, r);
                if (q == k) return nums[q];
                if (q < k) {
                    p = q + 1;
                } else {
                    r = q - 1;
                }
            }
            return nums[p];
        }
        private int partition(int[] nums, int p, int r) {
            int pivot = nums[r];
            int i = p - 1;
            for (int j = p; j < r; j++) {
                if (nums[j] >= pivot) swap(nums, ++i, j);
            }
            swap(nums, ++i, r);
            return i;
        }
        private void swap(int[] nums, int i, int j) {
            int t = nums[i];
            nums[i] = nums[j];
            nums[j] = t;
        }
    }
```
**思路2：使用 priority queue**
java就是PriorityQueue, python 就是 heapq.

python code:
```python
    class Solution:
        # @param nums {int[]} an integer unsorted array
        # @param k {int} an integer from 1 to n
        # @return {int} the kth largest element
        def kthLargestElement2(self, nums, k):
            import heapq
            arr = [(-a, a) for a in nums]
            heapq.heapify(arr)
            for i in range(k - 1):
                heapq.heappop(arr)
            return heapq.heappop(arr)[1]        
```