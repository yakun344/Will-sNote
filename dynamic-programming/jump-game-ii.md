## Jump Game II
_update Jan 18,2018  22:00_

---
[LeetCode](https://leetcode.com/problems/jump-game-ii/description/)

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Your goal is to reach the last index in the minimum number of jumps.

**For example:**

    Given array A = [2,3,1,1,4]

The minimum number of jumps to reach the last index is 2. (Jump 1 step from index 0 to 1, then 3 steps to the last index.)

**Note:** 

You can assume that you can always reach the last index.

<br>

### Basic Idea:
* #### DP Solution (TLE):
从右往左进行dp，对于每个格子 `dp[i]`，考虑 `step from 1 to nums[i]` 的步数所到达的格子中步数最少的(minStep)，则  `dp[i] = minStep + 1`；
```java
    Induction Rule:
        dp[i] = min{ dp[i + step] for step in (1 to nums[i]) } + 1;
    Base Case:
        dp[-1] = 0;
```
<br>
**Java Code:**
```java
class Solution {
    public int jump(int[] nums) {
        int[] dp = new int[nums.length];
        Arrays.fill(dp, Integer.MAX_VALUE);
        dp[nums.length - 1] = 0;
        for (int i = nums.length - 2; i >= 0; --i) {
            int minStep = Integer.MAX_VALUE;
            for (int step = 1; step <= nums[i]; ++step) {
                if (i + step < nums.length && dp[i + step] < minStep) {
                    minStep = dp[i + step];
                }
            }
            dp[i] = minStep == Integer.MAX_VALUE ? Integer.MAX_VALUE : minStep + 1;
        }
        return dp[0];
    }
}
```
<br>
* #### BFS (TLE, MLE):
我们可以把所有路径看做一个有向简单图，那么找最少步数的路径相当于找最短路径，所以我们可以使用一个BFS来做。但是由于BFS需要考虑每一条路径，而且还需要储存中间节点，导致时间和空间双双爆炸。  
<br>
**Java Code:**
```java
class Solution {
    public int jump(int[] nums) {
        if (nums.length == 1) return 0;
        Deque<Integer> queue = new ArrayDeque<>();
        queue.offerFirst(0);
        int step = 0;
        while (! queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; ++i) {
                int curr = queue.pollLast();
                for (int j = 1; j <= nums[curr]; ++j) {
                    if (curr + j < nums.length) {
                        if (curr + j == nums.length - 1) return step + 1;
                        else queue.offerFirst(curr + j);
                    }
                }
            }
            step++;
        }
        return -1;
    }
}
```
<br>
* #### 
















