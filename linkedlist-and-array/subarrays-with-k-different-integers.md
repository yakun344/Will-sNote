# Subarrays with K Different Integers
_update Feb 11 2019, 20:02_

---
[LeetCode](https://leetcode.com/problems/subarrays-with-k-different-integers/)

Given an array A of positive integers, call a (contiguous, not necessarily distinct) subarray of A good if the number of different integers in that subarray is exactly K.

(For example, `[1,2,3,1,2]` has 3 different integers: 1, 2, and 3.)

Return the number of good subarrays of A.

 
**Example 1:**

    Input: A = [1,2,1,2,3], K = 2
    Output: 7
    Explanation: Subarrays formed with exactly 2 different integers: [1,2], [2,1], [1,2], [2,3], [1,2,1], [2,1,2], [1,2,1,2].

**Example 2:**

    Input: A = [1,2,1,3,4], K = 3
    Output: 3
    Explanation: Subarrays formed with exactly 3 different integers: [1,2,1,3], [2,1,3], [1,3,4].
 

**Note:**

1. `1 <= A.length <= 20000`
2. `1 <= A[i] <= A.length`
3. `1 <= K <= A.length`

<br/>

### Basic Idea:
拿到题目之后就会发现如果要硬算，直接求解distinct element number为 `K` 的 `subarray` 个数是比较困难的，因为这类题目一般会使用双指针或者sliding window求解，而要求所有subarray的个数就需要维持许多窗口中subarray的特征。

看了Lee215的解法之后豁然开朗，我们可以写一个function `getAtMostK()`来求 `distinct element number <= K subarray` 的个数，这样只需要返回 `getAtMost(K) - getAtMost(K-1)` 就可以了。

具体地，如何实现 `getAtMostK()` 呢？用sliding window，保证window中不同元素个数 `<= K`，每次右边界右移一位，而每次当前窗口中所有以right结尾的`subarray`的个数就是 `right - left + 1`，只要把每个元素结尾的符合条件的subarray个数相加，就是总的个数。

整体时间复杂度为 `O(2N)`, 空间复杂度 `O(N)`;

#### Java Code:
```java
class Solution {
    public int subarraysWithKDistinct(int[] A, int K) {
        if (K == 0) return 0;
        return getAtMostK(A, K) - getAtMostK(A, K - 1);
    }
    
    private int getAtMostK(int[] nums, int K) {
        if (K == 0) return 0;
        int ret = 0;
        int[] count = new int[nums.length + 1];
        int i = 0, j = 0, m = 0;
        while (j < nums.length) {
            if (count[nums[j]] == 0) {
                m++;
            }
            count[nums[j]]++;
            while (m > K) {
                if (--count[nums[i]] == 0) {
                    m--;
                }
                i++;
            }
            // 此时保证subarray [i,j] 部分只有小于等于K个不同数字，
            // 计算从i开始到j结尾的所有subarray个数
            ret += j - i + 1;
            j++;
        }
        return ret;
    }
}
```

---
_update Mar 25, 2019_

### Update concise java code
这道题的本质是每次将right指针右移一位，所以我们可以利用一个for loop。在loop中每次检查window中是否只有小于等于K个不同的数字。合理利用++让code更短。
```java
    class Solution {
        public int subarraysWithKDistinct(int[] A, int K) {
            return atMostK(A, K) - atMostK(A, K - 1);
        }
        private int atMostK(int[] A, int K) {
            int[] map = new int[A.length + 1];
            int ret = 0, left = 0, count = 0;
            for (int r = 0; r < A.length; ++r) {
                if (map[A[r]]++ == 0) count++;
                while (count > K) {
                    if (--map[A[left++]] == 0) count--;
                }
                ret += r - left + 1;
            }
            return ret;
        }
    }
```
