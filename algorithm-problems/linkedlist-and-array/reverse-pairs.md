# Reverse Pairs

_update Nov 13, 2018_

[LeetCode](https://leetcode.com/problems/reverse-pairs)

Given an array nums, we call `(i, j)` an important reverse pair `if i < j` and `nums[i] > 2*nums[j]`.

You need to return the number of important reverse pairs in the given array.

**Example1:**

```text
Input: [1,3,2,3,1]
Output: 2
```

**Example2:**

```text
Input: [2,4,3,5,1]
Output: 3
```

**Note:**

1. The length of the given array will not exceed 50,000.
2. All the numbers in the input array are in the range of 32-bit integer.

## Basic Idea:

这道题目要找到所有 `i<j && nums[i]>2*nums[j]` 的 pair 的个数，首先想到的一定是brute force，对每个元素检查其后面的每个元素，耗时 `O(N^2)`。最优解的思路就是对这种 `O(N^2)` 的解法进行了优化，居然利用了 merge sort 的中间过程，思路之巧妙真是令人叹为观止。思路如下：

我们知道merge sort的时间复杂度为 `O(NlogN)`，递归式为 `T(n) = 2T(n/2) + O(n)`。也就是说，如果在每次递归中我们能用 O\(n\) 时间统计pair个数，最后就可以做到 `O(NlogN)` 的时间复杂度。在每次递归中，先sort左右两部分，此时我们就得到了两个排序的数组，同时可以保证左边数组的所有元素在原数组中的位置都在右边部分之左。接下来我们尝试用O\(n\)的时间来找到左右两个排序数组之间，左边元素大于右边元素二倍的pair个数，直接看code。

### Java Code:

```java
class Solution {
    public int reversePairs(int[] nums) {
        return mergeSort(nums, 0, nums.length - 1);
    }

    private int mergeSort(int[] nums, int p, int r) {
        if (p >= r) return 0;
        int q = p + (r - p) / 2;
        int count = mergeSort(nums, p, q) + mergeSort(nums, q + 1, r);
        int i = p, j = q + 1;
        while (i <= q) {
            while (j <= r && nums[i] > 2 * (long)nums[j]) {
                j++;
            }
            count += j - (q + 1);
            i++;
        }
        merge(nums, p, q, r);
        return count;
    }

    private void merge(int[] nums, int p, int q, int r) {
        int[] aux = Arrays.copyOfRange(nums, p, r + 1);
        int i = 0, j = q + 1 - p;
        for (int k = p; k <= r; ++k) {
            if (i > q - p) nums[k] = aux[j++];
            else if (j > r - p) nums[k] = aux[i++];
            else if (aux[i] > aux[j]) nums[k] = aux[j++];
            else nums[k] = aux[i++];
        }
    }
}
```

