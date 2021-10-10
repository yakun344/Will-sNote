# Kth Largest Element II

_update Aug 16,2017 0:02_

[LintCode](http://www.lintcode.com/en/problem/kth-largest-element-ii/)

Find K-th largest element in an array. and N is much larger than k.

**Notice** You can swap elements in the array

**Example:**

```
In array [9,3,2,4,8], the 3rd largest element is 4.

In array [1,2,3,4,5], the 1st largest element is 5, 2nd largest element is 4, 3rd largest element is 3 and etc.
```

## Basic Idea:

**思路1：使用 Quick Selection** Quick Select, 关键在于写出 bug free 的 quickselect。

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

**思路2：使用 priority queue** java就是PriorityQueue, python 就是 heapq. 但是要注意，要找第k大的数，pq中就需要维持当前k个最大数，则需要使用minheap；

python code:

```python
    class Solution:
        # @param nums {int[]} an integer unsorted array
        # @param k {int} an integer from 1 to n
        # @return {int} the kth largest element
        def kthLargestElement2(self, nums, k):
            import heapq
            # 维持k个当前最大值的pq，应该用minheap，每次和当前k个最大中最小的比较
            pq = []
            for num in nums:
                heapq.heappush(pq, num)
                if len(pq) > k:
                    heapq.heappop(pq)
            return pq[0]
```

_update 2018-07-15 21:28:314_

## Update: 关于判断条件的思考

**为什么quickSelection和quickSort中要保证每次 partition index 的左边都 **`<= pivot`** （或者**`>=`**），而不是小于或者大于？**\
因为如果没有小于或者大于pivot的数字，当前次的partition就什么都做不了了，加入等于条件保证至少有一个数字（即当前pivot）可以被分到左边。
