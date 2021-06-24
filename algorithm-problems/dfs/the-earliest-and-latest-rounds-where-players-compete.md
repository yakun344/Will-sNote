# The Earliest and Latest Rounds Where Players Compete

_update Jun 19, 2021_

![image](https://user-images.githubusercontent.com/24964756/122660547-3656d980-d137-11eb-87f0-109081d607e0.png)

## Basic Idea:

有一种比较巧妙的思路，就是只关注p1 p2 index的变化，因为在他们相遇之前，他们会在所有compete中取胜，于是我们可以根据他们的位置穷举可能出现的下一场的人数分布，从而算出下一场 p1 和 p2 所在的位置。因为最多进行 `log2(N)` 场，而每次最多尝试 `N^2` 次，总时间复杂度应该在 `O(N^(2logN))`，考虑到N比较小，还是很快的。

## Java Code:

```java
class Solution {
    public int[] earliestAndLatest(int n, int firstPlayer, int secondPlayer) {
        int p1 = Math.min(firstPlayer, secondPlayer);
        int p2 = Math.max(firstPlayer, secondPlayer);

        // 此时 p1 p2 直接交锋，结束
        if (p1 + p2 == n + 1) {
            return new int[]{1, 1};
        }
        if (n < 4) {
            return new int[]{2, 2};
        }

        // 让 p1 p2 更靠左边, 沿中线镜像翻转p1 p2不影响结果
        // 1 2 3 4 5 6 7 8 9 10 11 12
        //               1   2
        // 变成
        // 1 2 3 4 5 6 7 8 9 10 11 12
        //     1   2
        if (p1 - 1 > n - p2) {
            int t = n - p1 + 1;
            p1 = n - p2 + 1;
            p2 = t;
        }
        int[] ret = new int[]{100, -1};
        int nextRoundPlayers = (n + 1) / 2;
        // 当 p1 p2 都在中线左侧的时候, 或者p2在中线上
        // 1 2 3 4 5 6   7 8 9 10 11 12
        //     1   2
        // p1左边的12，以及中间的4可能赢又可能输，右边谁留下都不影响，下一局参赛总数不变
        if (p2 * 2 <= n + 1) {
            for (int i = 0; i < p1; ++i) {
                for (int j = 0; j < p2 - p1; ++j) {
                    int[] next = earliestAndLatest(nextRoundPlayers, i + 1, i + j + 2);
                    ret[0] = Math.min(ret[0], next[0]);
                    ret[1] = Math.max(ret[1], next[1]);
                }
            }
        }
        // 当 p1 p2 在中线两边
        // 1 2 3 4 5 6 7   8 9 10 11 12 13 14
        //     |   |           |
        //     1   lp2   l     2           因为之前转换过位置，此处一定p1p2一定偏左
        // 6789 一定留两个，p1左边以及p1p2之间不对称的4可能留可能不留
        // 而中间6789的长度l随N是奇偶变化, 只需要算出p2沿中心对称的位置lp2即可
        else {
            int lp2 = n + 1 - p2;
            int l = p2 - lp2 - 1;
            for (int i = 0; i < p1; ++i) {
                for (int j = 0; j < lp2 - p1; ++j) {
                    int[] next = earliestAndLatest(nextRoundPlayers, i + 1, i + 1 + j + (l + 1) / 2 + 1);
                    ret[0] = Math.min(ret[0], next[0]);
                    ret[1] = Math.max(ret[1], next[1]);
                }
            }
        }
        ret[0] += 1;
        ret[1] += 1;
        return ret;
    }
}
```

