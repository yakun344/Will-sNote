# Jump Game VI

_update Jun 9 2021_

![image](https://user-images.githubusercontent.com/24964756/121459126-87a8e100-c95f-11eb-88d8-df5a72ee5389.png)

## Basic Idea

基本思路就是DP，每次更新当前最优的最大score sum，看之前K个dp数组中的最大值，取最大的来更新当前的dp\[i\]. 而问题在于当k接近N的时候时间复杂度可能会变成 `N^2`，所以我们需要对求dp数组中前面K个元素最大值进行优化。我们有如下两种思路

1. **单调双端队列：** 用这种方法我们可以用平均 `O(1)` 的时间完成更新和求当前最大，需要注意的是 Deque 中存的其实是index而不是真实的score sum的值，这样在左边移除的时候才能操作。另外注意因为我们需要维持的是最大值，所以我们需要一个递减的单调队列。
2. **PriorityQueue：** 一开始没有想到这种方法，因为 heap 的remove操作是 `O(N)` 的，但是实际上类似于在 Dijkstra 中，我们可以做lazy deletion，当 heap 给我们的最大值的index超出了当前 i 左边 k 个的边界我们就忽略它，再从heap中取下一个，这样时间复杂度最多是 `O(NlogN)`

## Java Code：

单调双端队列解法

```java
class Solution {
    public int maxResult(int[] nums, int k) {
        int[] dp = new int[nums.length];
        dp[0] = nums[0];

        // 单调递减队列，维持最大值的index
        Deque<Integer> mDeque = new ArrayDeque<>();
        mDeque.offerFirst(0);

        for (int i = 1; i < dp.length; ++i) {
            int maxIndex = mDeque.peekLast();
            dp[i] = dp[maxIndex] + nums[i];
            while (!mDeque.isEmpty() && dp[mDeque.peekFirst()] < dp[i]) {
                mDeque.pollFirst();
            }
            mDeque.offerFirst(i);
            if (i - k == mDeque.peekLast()) {
                mDeque.pollLast();
            }
        }
        return dp[dp.length - 1];
    }
}
```

