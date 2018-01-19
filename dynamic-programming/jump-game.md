## Jump Game
_update Jan 18, 2018  21:01_

---
[LeetCode](https://leetcode.com/problems/jump-game/description/)

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Determine if you are able to reach the last index.

**For example:**

    A = [2,3,1,1,4], return true.
    A = [3,2,1,0,4], return false.    
    
<br>

### Basic Idea:
*  #### DP solution (TLE):
利用DP的思路，我们可以从最右端尽头出发，一路向左看。每到一个新的格子，我们就看可否通过这个格子跳到可以到达终点的格子。我们有如下 induction rule：
```java
    Induction Rule:
        dp[i] = true, if dp[i + available step number] == true
    Base Case:
        dp[-1] = true, 即终点一定为true
```
这种做法的时间复杂度为`O(n * k)`, k 为可能的步数，worst case 为 `O(n^2)`;  
<br>
**Java Code：**  
```java
class Solution {
    public boolean canJump(int[] nums) {
        boolean[] dp = new boolean[nums.length];
        dp[nums.length - 1] = true;
        for (int i = nums.length - 2; i >= 0; --i) {
            for (int step = nums[i]; step > 0; step--) {
                if (i + step < nums.length && dp[i + step]) { // 如果在 i 格子所能走的步数范围内能走到 true 的格子，则表示能走到终点
                    dp[i] = true;
                    continue;
                }
            }
        }
        return dp[0] == true;
    }
}
```













    
    