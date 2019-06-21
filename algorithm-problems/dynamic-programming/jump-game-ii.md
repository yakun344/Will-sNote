# Jump Game II

_update Jan 18,2018 22:00_

[LeetCode](https://leetcode.com/problems/jump-game-ii/description/)

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Your goal is to reach the last index in the minimum number of jumps.

**For example:**

```text
Given array A = [2,3,1,1,4]
```

The minimum number of jumps to reach the last index is 2. \(Jump 1 step from index 0 to 1, then 3 steps to the last index.\)

**Note:**

You can assume that you can always reach the last index.

## Basic Idea:

* **DP Solution \(TLE\):**

  从右往左进行dp，对于每个格子 `dp[i]`，考虑 `step from 1 to nums[i]` 的步数所到达的格子中步数最少的\(minStep\)，则  `dp[i] = minStep + 1`；

  ```java
    Induction Rule:
        dp[i] = min{ dp[i + step] for step in (1 to nums[i]) } + 1;
    Base Case:
        dp[-1] = 0;
  ```

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

* **BFS \(TLE, MLE\):**

  我们可以把所有路径看做一个有向简单图，那么找最少步数的路径相当于找最短路径，所以我们可以使用一个BFS来做。但是由于BFS需要考虑每一条路径，而且还需要储存中间节点，导致时间和空间双双爆炸。    

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

* **Greedy Algorithm \(AC\):**

    因为DP都不行，就只能考虑greedy了。考虑到此题中所给的 `nums[i]` 表示从 i 格子开始能跳的最大距离，所以我们其实没有必要逐一尝试这些距离，我们只需要跟踪之前最远可以跳到的位置，这样向右遍历的时候，只要当前位置在最大距

  离之前，就可以保证当前位置是可以reach的。  

    对于这道题，我们要计算到达终点的最小step数量，我们仍然采用从左往右遍历每个点，并且根据每个点更新最远当前可以reach点的办法。为了追踪step，我们需要额外维持一个变量，就是之前一次的最远可以reach的点\(prevRight\)。当我们遍历的位置超过了 prevRight 的时候，说明我们已经开始了下一步，则令 `prevRight = right; step++;`.

  ```java
    例如，对于input：nums [2,3,1,1,4]
        首先初始化 prevRight = 0, right = 0, step = 0
        第一个点是 nums[0], 更新 right = 0+nums[0] = 2;
        下一个是 nums[1], 因为 1>prevRight, step++, prevRight = right；
            然后更新 right = 1+nums[1] = 4
            此时 step=1，prevRight=2, right = 4， 已经到达end，返回 step=2
  ```

  **Java Code:**

  ```java
  class Solution {
    public int jump(int[] nums) {
        int prevRight = 0, right = 0;
        int step = 0;
        for (int i = 0; i < nums.length; ++i) {
            if (i > prevRight) {
                step++;
                prevRight = right;
            }
            if (i + nums[i] > right) {
                right = i + nums[i];
            }
        }
        return step;
    }
  }
  ```

