# Longest Mountain in Array

_update Nov 10, 2018_

[LeetCode](https://leetcode.com/problems/longest-mountain-in-array)

Let's call any \(contiguous\) subarray B \(of A\) a mountain if the following properties hold:

`B.length >= 3` There exists some `0 < i < B.length - 1` such that `B[0] < B[1] < ... B[i-1] < B[i] > B[i+1] > ... > B[B.length - 1]` \(Note that B could be any subarray of A, including the entire array A.\)

Given an array A of integers, return the length of the longest mountain.

Return 0 if there is no mountain.

**Example 1:**

```text
Input: [2,1,4,7,3,2,5]
Output: 5
Explanation: The largest mountain is [1,4,7,3,2] which has length 5.
```

**Example 2:**

```text
Input: [2,2,2]
Output: 0
Explanation: There is no mountain.
```

**Note:**

```text
0 <= A.length <= 10000
0 <= A[i] <= 10000
```

**Follow up:**

1. Can you solve it using only one pass?
2. Can you solve it in O\(1\) space?

## Basic Idea:

这道题就是求最长的山峰的长度，山峰至少有三个元素，并且需要严格递增再严格递减。

有两种思路，比较简单的就是对于每个元素，向两边扩展找以其自身为中点的山峰长度。但是这样做有可能造成很多一侧山峰被重复计算，最坏情况下时间复杂度为`O(n^2)`，但是可以通过先进行一次检查当前元素左边和右边是否都比他小来优化。

另一种思路就是只进行一次扫描，每次从山峰最左边开始，找到峰顶，再向右找到右边底，然后以右边底为下一个山峰最左边元素开始下次扫描，这样时间复杂度为`O(n)`。

### Java Code:

* **左右扩展的做法**

  使用预检查的方法判断每个点是否是山峰顶点，如果不是就直接返回0，这种优化后可以极大降低时间复杂度。

  ```java
  class Solution {
    public int longestMountain(int[] A) {
        int ret = 0;
        for (int i = 0; i < A.length; ++i) {
            ret = Math.max(ret, getMountainLen(A, i));
        }
        return ret;
    }

    private int getMountainLen(int[] nums, int mid) {
        // 优化，检查当前点是否是顶点
        if (mid == 0 || mid == nums.length - 1 || nums[mid - 1] >= nums[mid] || nums[mid] <= nums[mid + 1]) {
            return 0;
        }
        int leftLen = 0, rightLen = 0;
        for (int i = mid - 1; i >= 0; --i) {
            if (nums[i] < nums[i + 1]) leftLen++;
            else break;
        }
        if (leftLen == 0) return 0;
        for (int i = mid + 1; i < nums.length; ++i) {
            if (nums[i] < nums[i - 1]) rightLen++;
            else break;
        }
        if (rightLen == 0) return 0;
        return leftLen + rightLen + 1;
    }
  }
  ```

* **One pass solution**

  ```java
  class Solution {
    public int longestMountain(int[] A) {
        int ret = 0, i = 0;
        while (i < A.length - 2) {
            // 先定位最左边元素
            while (i < A.length - 1 && A[i] >= A[i + 1]) {
                i++;
            }
            // 确定左边部分长度
            int leftLen = 0;
            while (i < A.length - 1 && A[i] < A[i + 1]) {
                leftLen++;
                i++;
            }
            // 确定右边部分长度
            int rightLen = 0;
            while (i < A.length - 1 && A[i] > A[i + 1]) {
                rightLen++;
                i++;
            }
            // 检查当前扫描过的部分是否是valid的山峰
            if (leftLen > 0 && rightLen > 0) {
                ret = Math.max(ret, leftLen + rightLen + 1);
            }
        }
        return ret;
    }
  }
  ```

