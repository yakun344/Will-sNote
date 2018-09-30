# Coins in a Line II

---
_update Sep 27 2018, 14:58_

[LintCode](https://www.lintcode.com/problem/coins-in-a-line-ii/description)

There are n coins with different value in a line. Two players take turns to take one or two coins from left side until there are no more coins left. The player who take the coins with the most value wins.

Could you please decide the first player will win or lose?

**Example**

    Given values array A = [1,2,2], return true.

    Given A = [1,2,4], return false.

<br/>

### Basic Idea:
因为取硬币是从左到右，所以我们可以定义子问题 dp[i] 为 `values[i:]`范围中，先拿硬币的玩家能获得的最大利益。从右向左计算。则`dp[0]`就是先拿硬币玩家能获得的总的最大收益。

于是我们有如下base case和rule：
```c
  n = values.length

  Base Case:

    dp[n] = 0; // 表示取完所有硬币之后就没有收益了
    dp[n-1] = values[n-1]; // 剩一个全拿
    dp[n-2] = values[n-1] + values[n-2]; // 剩两个全拿

  Rule:

    dp[i] = max{
      values[i] + min{ dp[i+2], dp[i+3] }, // 拿一个，然后对面拿一个或者两个
      values[i] + values[i+1] + min{ dp[i+3], dp[i+4] } // 拿两个，然后对面拿一个或者两个
    }

  出口：
    return dp[0] > sum(values) / 2; // 拿到超过总和一半的价值就赢了
```
解释一下，对于`dp[i]`，有两种拿法，拿一个或者拿两个，而对于这两种情况，下一次对面都会通过选择拿一个还是拿两个来使我们的收益最小，而我们需要在两种对面留给我们的最差情况中选择较好的那种。

**反思**：  

1. 注意dp数组的定义，`dp[i]` 表示的永远是先拿硬币的玩家能获得的`[i:n]范围`的最大收益。
2. 从右向左计算，这样才能保证`dp[0]`表示先拿硬币的玩家最终的最大收益。
3. `dp[i]`也可以理解为到 i 位置剩下的硬币价值中能获得的最大收益，而另一个玩家的每次选择都会尽量将先拿硬币玩家剩余的价值最小化。

#### Java Code:
一个细节，可以先将dp数组的长度声明为values.length + 2, 这样可以为后面留出两个0，就不需要特殊判断了。
```java
public class Solution {
    /**
     * @param values: a vector of integers
     * @return: a boolean which equals to true if the first player will win
     */
    public boolean firstWillWin(int[] values) {
        if (values.length == 0) return false;
        else if (values.length < 3) return true;
        int N = values.length;
        int dp[] = new int[values.length + 2];
        dp[N - 1] = values[N - 1];
        dp[N - 2] = values[N - 1] + values[N - 2];
        for (int i = N - 3; i >= 0; --i) {
            int get1 = values[i] + Math.min(dp[i + 2], dp[i + 3]);
            int get2 = values[i] + values[i + 1] + Math.min(dp[i + 3], dp[i + 4]);
            dp[i] = Math.max(get1, get2);
        }
        int sum = 0;
        for (int val : values) sum += val;
        return dp[0] > sum / 2;
    }
}
```
