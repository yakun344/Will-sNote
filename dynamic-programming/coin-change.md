## Coin Change
_update Jan 9,2018  21:16_

---
[LeetCode](https://leetcode.com/problems/coin-change/description/)

You are given coins of different denominations and a total amount of money amount. Write a function to compute the fewest number of coins that you need to make up that amount. If that amount of money cannot be made up by any combination of the coins, return -1.

**Example 1:**

    coins = [1, 2, 5], amount = 11
    return 3 (11 = 5 + 5 + 1)

**Example 2:**

    coins = [2], amount = 3
    return -1.

**Note:**  
You may assume that you have an infinite number of each kind of coin.

<br>

#### 思路 1，dfs，combination sum (TLE)：
首先想到直接用combination sum的方法求解，但是果然 TLE 了。思路是dfs分为 `len(coins)` 层，每层表示一种 coin。在每层中的分支表示使用当前 coin 不同次数，时间复杂度为 `O([target amount / min(coins)]^len(coins))`.

**Java Code:**

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int ret = dfs(coins, amount, 0, 0);
        return  ret == Integer.MAX_VALUE ? -1 : ret;
    }
    
    private int dfs(int[] coins, long remainSum, int pos, int path) {
        if (remainSum == 0 || pos == coins.length) {
            if (remainSum == 0) return path;
            return Integer.MAX_VALUE;
        }
        int minCount = Integer.MAX_VALUE;
        for (int i = 0; i * (long)(coins[pos]) <= remainSum; ++i) {
            int count = dfs(coins, remainSum - i * coins[pos], pos + 1, path + i);
            minCount = Math.min(minCount, count);
        }
        return minCount;
    }
}
```

#### 思路 2，优化，memorized search：
接下来考虑之前之所以 TLE 了，是因为有很多重复情况被重复考虑，例如：
```python
    input: [1,5,20], amount = 100
    当我们计算到 
               {5,5,5,5}, remain: 80
               {20}, remain: 80
               {1,1,1,1,1,5,5,5}, remain: 80
   这三种情况的时候，后面的 80 所需要的最小 coin 数量其实只需要被计算一次；
```
根据这种思路，我们可以用一个 `HashMap<remain, coinNum>` 来做记忆化搜索。但是要注意，如果按照之前在 **DFS notes** 中缩写的那种写法（即之前的dfs写法），因为每层除了remainSum之外，可选的coins也是不同的，所以不能直接使用。如果要用 HashMap 辅助，我们必须使用那种 naive 的每层分别考虑使用每个数的做法：

另外，在dfs的过程中，我们的dfs函数需要自下而上地返回每个remainSum的所需coin个数，这样才能进行记忆化搜索。

**Java Code：**  
```java





















