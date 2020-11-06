# Dungeon Games
_update Nov 5th, 2020_

---
The demons had captured the princess (P) and imprisoned her in the bottom-right corner of a dungeon. The dungeon consists of M x N rooms laid out in a 2D grid. Our valiant knight (K) was initially positioned in the top-left room and must fight his way through the dungeon to rescue the princess.

The knight has an initial health point represented by a positive integer. If at any point his health point drops to 0 or below, he dies immediately.

Some of the rooms are guarded by demons, so the knight loses health (negative integers) upon entering these rooms; other rooms are either empty (0's) or contain magic orbs that increase the knight's health (positive integers).

In order to reach the princess as quickly as possible, the knight decides to move only rightward or downward in each step.

 

Write a function to determine the knight's minimum initial health so that he is able to rescue the princess.

For example, given the dungeon below, the initial health of the knight must be at least 7 if he follows the optimal path `RIGHT-> RIGHT -> DOWN -> DOWN.`
```
-2(K)	-3	  3
-5	   -10	  1
10	    30	 -5 (P)
```

**Note:**

1. The knight's health has no upper bound.
2. Any room can contain threats or power-ups, even the first room the knight enters and the bottom-right room where the princess is imprisoned.

## Basic Idea
有一个地牢，需要从左上角通过向右或者向下走到右下角，地牢中每个格子中的数字代表加血或者减血，血量下降到0或者低于0就会死，求初始的时候至少需要多少血量。

这道题显然需要使用DP的方法，但需要注意的是在任意一个中间的位置血量都不可以低于0，这使得从左上开始的DP比较困难，我们可以从右下角出发向左上dp，这样就可以从那条路走可以少掉血。dp数组中存的就是该点到右下角至少需要多少血量，此时允许为0，但不可为负，最终返回的就是 `dp[0][0] + 1`

## Java Code
```java
class Solution {
    public int calculateMinimumHP(int[][] dungeon) {
        int R = dungeon.length;
        int C = dungeon[0].length;
        int[][] dp = new int[R][C];
        for (int r = R - 1; r >= 0; --r) {
            for (int c = C - 1; c >= 0; --c) {
            // 注意其中的 Math.max(0, xxx), 这里保证了不会出现负值，即如果下一步会加血，也不允许这一步的状态是负血量
                if (r == R - 1 && c == C - 1) {
                    dp[r][c] = Math.max(0, -dungeon[r][c]);
                } else if (r == R - 1) {
                    dp[r][c] = Math.max(0, dp[r][c + 1] - dungeon[r][c]);
                } else if (c == C - 1) {
                    dp[r][c] = Math.max(0, dp[r + 1][c] - dungeon[r][c]);
                } else {
                    dp[r][c] = Math.max(0, Math.min(dp[r][c + 1], dp[r + 1][c]) - dungeon[r][c]);
                }
            }
        }
        return dp[0][0] + 1;
    }
}
```
