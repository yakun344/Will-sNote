# Coins in a Line

_update Sep 26 2018, 13:16_

[LintCode](https://www.lintcode.com/problem/coins-in-a-line/description)

Description There are n coins in a line. Two players take turns to take one or two coins from right side until there are no more coins left. The player who take the last coin wins.

Could you please decide the first play will win or lose?

**Example**

```text
n = 1, return true.

n = 2, return true.

n = 3, return false.

n = 4, return true.

n = 5, return true.
```

**Challenge**  
O\(n\) time and O\(1\) memory

## Basic Idea:

利用dp的思路，定义`dp[i]`为当剩余硬币数量为 i 时，当前玩家是否可以获胜。所以对于 `dp[i]` 的状态，该玩家可以取 1 或 2 个硬币，那么这个玩家能否取胜就取决于当取完硬币之后，另外一位玩家能否获胜。所以我们有 `dp[i] = ! dp[i - 1] || ! dp[i - 2]`（即拿 1 个硬币之后另外一个玩家不可获胜或者拿 2 个之后另一名玩家不可获胜）。

### Java Code:

* **无优化**

  ```java
  public class Solution {
      /**
       * @param n: An integer
       * @return: A boolean which equals to true if the first player will win
       */
      public boolean firstWillWin(int n) {
          if (n == 0) return false;
          else if (n < 3) return true;
          boolean[] dp = new boolean[n + 1];
          dp[0] = false;
          dp[1] = true;
          dp[2] = true;
          for (int i = 3; i < n + 1; ++i) {
              dp[i] = ! dp[i - 1] || ! dp[i - 2];
          }
          return dp[n];
      }
  }
  ```

* **滚动数组优化 O\(1\) Space**

  用九章的方法，利用取余来计算滚动数组中实际的index。

  ```java
  public class Solution {
      /**
       * @param n: An integer
       * @return: A boolean which equals to true if the first player will win
       */
      public boolean firstWillWin(int n) {
          if (n == 0) return false;
          else if (n < 3) return true;
          boolean[] dp = new boolean[3];
          dp[0] = true;
          dp[1] = true;
          for (int i = 3; i <= n; ++i) {
              dp[(i - 1) % 3] = ! dp[(i - 2) % 3] || ! dp[(i - 3) % 3];
          }
          return dp[(n - 1) % 3];
      }
  }
  ```

