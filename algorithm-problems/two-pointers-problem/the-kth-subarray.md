---
description: 'Jul 6, 2021'
---

# the kth subarray

[lintcode](https://www.lintcode.com/problem/1139/description)

![](../../.gitbook/assets/image%20%284%29.png)

### Basic Idea

可以看到这道题目的数据量很大，需要想一种优于brute force的做法。题目所求的是第k大的subarray sum的值，也就是有k个subarray的和小于等于这个值。如果我们能够在`O(N)` 的时间内判断所有和小于等于target的subarray的个数是否小于等于k，我们就可以利用二分法求的最小的满足条件的target，也就是解。

那么如何`O(n)`时间得到sum小于等于target的所有subarray的个数呢？因为原数组中没有负数，我们可以利用双指针。对于每个start，找到对应的end使得`sum[start,end]`小于等于target，然后count之间的以start开始的subarray个数。然后右移start，重复上述步骤。因为右移start之后其间的sum一定变小，所以end只会右移，总时间复杂度 `O(n)` .

### Java Code:

```java
public class Solution {
    /**
     * @param a: an array
     * @param k: the kth
     * @return: return the kth subarray
     */
    public long thekthSubarray(int[] a, long k) {
        long[] prefixSum = new long[a.length + 1];
        for (int i = 0; i < a.length; ++i) {
            prefixSum[i + 1] = prefixSum[i] + a[i];
        }

        long left = 1, right = prefixSum[a.length];
        while (left + 1 < right) {
            long mid = (left + right) / 2;
            long count = countSumLessThan(prefixSum, mid);
            if (count < k) left = mid;
            else right = mid;
        }
        if (countSumLessThan(prefixSum, left) == k) return left;
        else return right;
    }

    // 返回和小于等于target的subarray的个数
    private long countSumLessThan(long[] prefixSum, long target) {
        long count = 0;
        int left = 0, right = prefixSum.length - 1;
        for (; left < right; ++left) {
            while (prefixSum[right] - prefixSum[left] > target) {
                // 只会在第一次run，之后right只会右移
                right--;
            }
            while (right + 1 < prefixSum.length 
                && prefixSum[right + 1] - prefixSum[left] <= target
            ) {
                right++;
            }
            count += right - left;
        }
        return count;
    }
}
```

