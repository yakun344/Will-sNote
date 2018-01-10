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

#### 思路 2，优化，memorized search (AC)：
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

另外，在dfs的过程中，我们的dfs函数需要自下而上地返回每个remainSum的所需coin个数，而不是自上而下传入path，这样才能进行记忆化搜索。

**Java Code：**  
```java
// 用一个hashmap 存储之前已经计算出最小 coin number 的 amount
//  dfs的方法是，在每层中分别考虑使用每个coin
class Solution {
    private HashMap<Integer, Integer> map;
    public int coinChange(int[] coins, int amount) {
        map = new HashMap<>();
        return dfs(coins, amount);
    }
    
    private int dfs(int[] coins, int remain) {
        if (remain == 0) return 0;
        if (map.containsKey(remain)) return map.get(remain);
        int minCount = Integer.MAX_VALUE;
        for (int coin : coins) {
            if (coin > remain) continue;
            int count = dfs(coins, remain - coin);
            if (count >= 0 && count < minCount) minCount = count;
        }
        // 如果全部返回-1，说明此remain不可能产生，则将其匹配值-1，表示不可能
        if (minCount == Integer.MAX_VALUE) map.put(remain, -1);
        else map.put(remain, minCount + 1);
        return map.get(remain);
    }
}
```

#### 思路 3，DP, bottom up（ AC ）：
首先我们可以有递推式：
```python
    input coins: {1,2,5}
    则我们有：
    
                       dp[i - 1]
        dp[i] = min(   dp[i - 2]   ) + 1 
                       dp[i - 5] 

    Base case: 
    
        dp[0] = 0, dp[others] = inf
```

没什么神奇的，直接用 `O(amount * len(coins))` 时间从前到后递推直到得到 `dp[amount]`，空间复杂度 `O(amount)`;

**Java Code:**
```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        if (amount == 0) return 0;
        int[] dp = new int[amount + 1];
        // dp[i] 表示 i 元需要的最小coin数量
        Arrays.fill(dp, Integer.MAX_VALUE); 
        dp[0] = 0;
        // i 为当前需要递推的金额数，dp[i] 应该是 i 金额所需要的最少coin数量
        for (int i = 1; i < amount + 1; ++i) {
            int minCount = Integer.MAX_VALUE;
            for (int coin : coins) {
                if (i - coin >= 0 && dp[i - coin] < Integer.MAX_VALUE) {
                    minCount = Math.min(minCount, dp[i - coin]);
                }
            }
            dp[i] = minCount < Integer.MAX_VALUE ? minCount + 1 : Integer.MAX_VALUE;
        }
        if (dp[amount] == Integer.MAX_VALUE) return -1;
        return dp[amount];
    }
}
```

#### 思路 4： DP，top-down （AC）
仍然是前面的递推式，只是我们用recursion从上至下求解。

**Java Code:**
```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        // dpArray 的每个元素dpArray[i]表示金额为 i 所需要的最少 coin 数量
        // 有三种状态，INF 为未计算，-1 为不可能，其余正数为最少 coin 数量
        int[] dpArray = new int[amount + 1]; 
        Arrays.fill(dpArray, Integer.MAX_VALUE);
        dpArray[0] = 0;
        return dp(coins, amount, dpArray);
    }
    
    private int dp(int[] coins, int amount, int[] dpArray) {
        if (amount < 0) return Integer.MAX_VALUE; // shouldn't be here
        if (dpArray[amount] != Integer.MAX_VALUE) return dpArray[amount];
        int minCount = Integer.MAX_VALUE;
        for (int coin : coins) {
            if (amount < coin) continue;
            int count = dp(coins, amount - coin, dpArray);
            // 注意这里判断条件是 count >= 0
            if (count >= 0 && count < minCount) {
                minCount = count;
            }
        }
        if (minCount == Integer.MAX_VALUE) dpArray[amount] = -1;
        else dpArray[amount] = minCount + 1;
        return dpArray[amount];
    }
}
```

<br>

### 总结：
这道题目用dp做和用 memorized search 的时间复杂度其实是差不多的，因为问题的规模是 `O(amount * len(coins))`。用dfs直接做的话，因为会计算很多重复情况，会TLE。这也印证了那句话：_求解的数量用dp，如果求解的空间则必须用dfs，因为即使两个问题的子问题相同，他们也是不同的两个解_。












