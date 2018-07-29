## Array Nesting
_update Jul 7, 2017 15:24_

---

[leetcode](https://leetcode.com/problems/array-nesting/#/description)

A zero-indexed array A consisting of N different integers is given. The array contains all integers in the range [0, N - 1].

Sets S[K] for 0 <= K < N are defined as follows:

    S[K] = { A[K], A[A[K]], A[A[A[K]]], ... }.

Sets S[K] are finite for each K and should NOT contain duplicates.

Write a function that given an array A consisting of N integers, return the size of the largest set S[K] for this array.

    Example 1:
        Input: A = [5,4,0,3,1,6,2]
        Output: 4
        Explanation:
        A[0] = 5, A[1] = 4, A[2] = 0, A[3] = 3, A[4] = 1, A[5] = 6, A[6] = 2.

        One of the longest S[K]:
        S[0] = {A[0], A[5], A[6], A[2]} = {5, 6, 2, 0}
**Note:**
N is an integer within the range [1, 20,000].
The elements of A are all distinct.
Each element of array A is an integer within the range [0, N-1].

#### Basic Idea:
实际上是要求所有环中最长的环。注意到因为没有重复元素，所以每个元素最多可以被一条路径指向，也就是说只会包含在一个环中。所以，我们只要做一次 dfs，跳过之前 visited 的 element，得出最大深度就好了。

#### Java Implementation：
在下面的实现中，因为我们不需要关注之前已经 visited 的 element，所以直接把他们都设为 -1，因为原始数的范围是 [1, 20000]。
```java
public class Solution {
    public int arrayNesting(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        int maxLength = 0;
        for (int i = 0; i < nums.length; ++i) {
            int length = 0, idx = i;
            while (idx >= 0 && idx < nums.length && nums[idx] != -1) {
                length++;
                int temp = nums[idx];
                nums[idx] = -1;
                idx = temp;
            }
            maxLength = Math.max(maxLength, length);
        }
        return maxLength;
    }
}
```

---
Update 2018-07-29 13:28:18

#### Java Solution, shorter
更短一些的实现方法，思路和上面的Java一样
```java
class Solution {
    public int arrayNesting(int[] nums) {
        int ret = 0;
        for (int i = 0; i < nums.length; ++i) {
            int len = 0;
            while (nums[i] >= 0) {
                int t = nums[i];
                nums[i] = -1;
                i = t;
                len++;
            }
            ret = Math.max(ret, len);
        }
        return ret;
    }
}
```
