# Minimum Number of K Consecutive Bit Flips

_update Feb 18 2019, 0:43_

[LeetCode](https://leetcode.com/problems/minimum-number-of-k-consecutive-bit-flips/)

In an array A containing only 0s and 1s, a K-bit flip consists of choosing a \(contiguous\) subarray of length K and simultaneously changing every 0 in the subarray to 1, and every 1 in the subarray to 0.

Return the minimum number of K-bit flips required so that there is no 0 in the array. If it is not possible, return -1.

**Example 1:**

```text
Input: A = [0,1,0], K = 1
Output: 2
Explanation: Flip A[0], then flip A[2].
```

**Example 2:**

```text
Input: A = [1,1,0], K = 2
Output: -1
Explanation: No matter how we flip subarrays of size 2, we can't make the array become [1,1,1].
```

**Example 3:**

```text
Input: A = [0,0,0,1,0,1,1,0], K = 3
Output: 3
Explanation:
Flip A[0],A[1],A[2]: A becomes [1,1,1,1,0,1,1,0]
Flip A[4],A[5],A[6]: A becomes [1,1,1,1,1,0,0,0]
Flip A[5],A[6],A[7]: A becomes [1,1,1,1,1,1,1,1]
```

**Note:**

1. `1 <= A.length <= 30000`
2. `1 <= K <= A.length`

## Basic Idea:

### Greedy，Brute Force

这道题可以使用贪心算法来做，因为对于一个0，如果它没有被之前的flip操作变成1，我们必须在这里开始将其变为1。所以有了一个brute force的做法，就是从左往右扫描，每次遇到0就进行`flipK`的操作，统计所有的操作次数。但是这样的算法时间复杂度为`O(N*K)`, 在K比较大的时候会趋近于`O(N^2)`。

* **Java Code:**

  ```java
    class Solution {
        public int minKBitFlips(int[] A, int K) {
            int ret = 0;
            for (int i = 0; i < A.length; ++i) {
                if (A[i] == 0) {
                    flipK(A, i, K);
                    ret++;
                }
            }
            for (int i = 0; i < A.length; ++i) {
                if (A[i] == 0) return -1;
            }
            return ret;
        }

        private void flipK(int[] nums, int start, int k) {
            if (start + k - 1 >= nums.length) return;
            for (int i = 0; i < k; ++i) {
                nums[start + i] ^= 1;
            }
        }
    }
  ```

  **Greedy, Sliding Window**

  我们注意到一个数字在考虑到它之前的状态会受到它之前`K-1`个位置的flip的影响，于是我们如果可以跟踪更新flip的状态，就有机会将时间优化到`O(N)`。

用另一个数组fliped来记录之前每个位置的flip状态，需要注意这里需要记录的是在考虑当前位置时是否需要进行flip，而不是最终的flip与否的状态。

* **Java Code:**

  ```java
    class Solution {
        public int minKBitFlips(int[] A, int K) {
            int[] flipped = new int[A.length];
            // fFlipped 表示当前的反转状态，如果偶数次，则fFlipped为0，奇数次则为1
            int fFlipped = 0, res = 0;
            for (int i = 0; i < A.length; ++i) {
                if (i >= K) {
                    // 消除i-k位置之前位置的影响
                    fFlipped ^= flipped[i - K];
                }
                if ((A[i] ^ fFlipped) == 0) {
                    // i 位置需要被翻转
                    if (i + K > A.length) return -1; // 长度不够，不能flip
                    res++;
                    fFlipped ^= 1;
                    flipped[i] = 1; // 因为确实需要进行一次反转，所以设为1
                }
            }
            return res;
        }
    }
  ```

