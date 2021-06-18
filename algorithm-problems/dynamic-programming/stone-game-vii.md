# Stone Game VII

_update Jun 11, 2021_

![image](https://user-images.githubusercontent.com/24964756/121767438-1313a500-cb0d-11eb-8a9a-70e4e1d26602.png)

## Basic Idea:

Stone Game是一个典型的DP系列，但是我一直都没有很好的整理过。

对于这道题来说，先动手的玩家一定赢，而要求的就是以当前玩家的视角，在两个玩家都做出最优选择的情况下能够比对方多出来的分数。我们注意到对于输入数组 `[5,3,1,4,2]`, 选择5或者选择2会分别留给下一个玩家不同的局面，而对面玩家同样需要保证能从下局开始获得尽可能大的优势。所以这里其实有一个递归的做法，即下面的dfs解法。另一种做法是bottom up DP，从只有一个数字开始到两个相邻的数字，然后到三个相邻数字，直到推导出整个数组。

这里有一个比较抽象的地方，类似于一点 min max 算法的思路，就是 `最终分数差距 = 本局得分 - 对手从剩下数字中最优选择继续游戏导致的对手能得到的最大差距`。

## DP 解法

![image](https://user-images.githubusercontent.com/24964756/121767764-4820f700-cb0f-11eb-8cd7-74389274a24f.png)

```java
class Solution {
    public int stoneGameVII(int[] stones) {
        int[] prefixSum = new int[stones.length + 1];
        for (int i = 0; i < stones.length; ++i) {
            prefixSum[i + 1] = prefixSum[i] + stones[i];
        }

        int[][] dp = new int[stones.length][stones.length];
        for (int i = 0; i < stones.length; ++i) {
            dp[i][i] = 0;
        }
        for (int l = 1; l <= stones.length; ++l) {
            for (int i = 0; i + l < stones.length; ++i) {
                int j = i + l;
                dp[i][j] = Math.max(
                    prefixSum[j + 1] - prefixSum[i + 1] - dp[i + 1][j],
                    prefixSum[j] - prefixSum[i] - dp[i][j - 1]);
            }
        }
        // for (int i = 0; i < stones.length; ++i) {
        //     System.out.println(Arrays.toString(dp[i]));
        // }
        return dp[0][stones.length - 1];
    }
}
```

## DFS with Cache 解法

```java
class Solution {
    public int stoneGameVII(int[] stones) {
        int[][] cache = new int[1000][1000];
        for (int i = 0; i < cache.length; ++i) {
            Arrays.fill(cache[i], Integer.MAX_VALUE);
        }
        return getMaxDiff(stones, 0, stones.length - 1, cache);
    }

    private int getMaxDiff(int[] arr, int left, int right, int[][] cache) {
        if (left == right) return 0;
        if (cache[left][right] != Integer.MAX_VALUE) {
            return cache[left][right];
        }
        int maxDiff = Math.max(
            sum(arr, left, right - 1) - getMaxDiff(arr, left, right - 1, cache),
            sum(arr, left + 1, right) - getMaxDiff(arr, left + 1, right, cache)
        );
        cache[left][right] = maxDiff;
        return maxDiff;
    }

    private int sum(int[] arr, int left, int right) {
        int ret = 0;
        for (int i = left; i <= right; ++i) {
            ret += arr[i];
        }
        return ret;
    }
}
```

