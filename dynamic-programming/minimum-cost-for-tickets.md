# Minimum Cost For Tickets
_update Feb 12 2019, 1:14_

---
[LeetCode](https://leetcode.com/problems/minimum-cost-for-tickets/)

In a country popular for train travel, you have planned some train travelling one year in advance.  The days of the year that you will travel is given as an array days.  Each day is an integer from 1 to 365.

Train tickets are sold in 3 different ways:

    a 1-day pass is sold for costs[0] dollars;
    a 7-day pass is sold for costs[1] dollars;
    a 30-day pass is sold for costs[2] dollars.

The passes allow that many days of consecutive travel.  For example, if we get a 7-day pass on day 2, then we can travel for 7 days: day 2, 3, 4, 5, 6, 7, and 8.

Return the minimum number of dollars you need to travel every day in the given list of days.

 

**Example 1:**

    Input: days = [1,4,6,7,8,20], costs = [2,7,15]
    Output: 11
    Explanation: 
    For example, here is one way to buy passes that lets you travel your travel plan:
    On day 1, you bought a 1-day pass for costs[0] = $2, which covered day 1.
    On day 3, you bought a 7-day pass for costs[1] = $7, which covered days 3, 4, ..., 9.
    On day 20, you bought a 1-day pass for costs[0] = $2, which covered day 20.
    In total you spent $11 and covered all the days of your travel.

**Example 2:**

    Input: days = [1,2,3,4,5,6,7,8,9,10,30,31], costs = [2,7,15]
    Output: 17
    Explanation: 
    For example, here is one way to buy passes that lets you travel your travel plan:
    On day 1, you bought a 30-day pass for costs[2] = $15 which covered days 1, 2, ..., 30.
    On day 31, you bought a 1-day pass for costs[0] = $2 which covered day 31.
    In total you spent $17 and covered all the days of your travel.
 

**Note:**

1. `1 <= days.length <= 365`
2. `1 <= days[i] <= 365`
3. `days is in strictly increasing order.`
4. `costs.length == 3`
5. `1 <= costs[i] <= 1000`

<br/>

### Basic Idea:
* ### DFS with cache (passed but slow)
  这是比较intuitive的思路，即对每个已经过期需要买票对日期，分别考虑买三种票的cost。用当前花销currCost 以及 endDate 做key进行cache，但是依然很慢。
  #### Java Code：
  ```java
  class Solution {
    private Map<Long, Integer> cache = new HashMap<>();
        
    public int mincostTickets(int[] days, int[] costs) {
        return dfs(days, costs, 0, 0, days[0] - 1);
    }
    
    private int dfs(int[] days, int[] costs, int pos, int currCost, int endDate) {
        if (pos >= days.length) return currCost;
        if (endDate >= days[days.length - 1]) return currCost;
        Long key = ((long)endDate << 31) + currCost;
        if (cache.containsKey(key)) return cache.get(key);
        
        int nextPos = pos;
        while (days[nextPos] <= endDate) nextPos++;
        int d1 = dfs(days, costs, nextPos, currCost + costs[0], days[nextPos]);
        int d7 = dfs(days, costs, nextPos, currCost + costs[1], days[nextPos] + 6);
        int d30 = dfs(days, costs, nextPos, currCost + costs[2], days[nextPos] + 29);
        int ret = Math.min(Math.min(d1, d7), d30);
        
        cache.put(key, ret);
        return ret;
    }
  }
  ```

  * ### DP (`O(365)` time `O(365)` space)
    由于这道题买不同的票的有效期不同，从前往后推的话我们需要考虑记录每一张票的有效期，很不方便。于是，我们应该从后往前考虑。
    
    `dp[i]`表示从第`i`天开始直到结束的cost之和。即我们对于每一天，如果这一天不需要旅行，则我们没有理由买票，于是只需要保持状态不变，则这天`dp[i]`的状态和`dp[i+1]`是一样的。而如果这一天需要旅行，则我们分别考虑三种票的情况：
    ```c
        dp[i + 1] + costs[0]
        dp[i + 7] + costs[1]
        dp[i + 30] + costs[2]
    ```
    取三者中最小的作为第 `i` 天的状态。最终我们返回的是`dp[1]`. 之所以我们可以只考虑在这一天买票，而不需考虑这一天可能会被之前买过的票cover，是因为如果之后计算之前某天的时候，在那一天买的票cover了这天，那么那一天的花销不会包括这一天买票的花销。

    具体实现的时候可以采用recursion的方法，从`dp(1)`开始调入，也可以采用从后往前数格子的方法。
    #### Java Code:
    * Recursion
    ```java
    class Solution {
        private int[] memo;
        private Set<Integer> daySet;
        private int[] costs;
        
        public int mincostTickets(int[] days, int[] costs) {
            this.costs = costs;
            memo = new int[366];
            Arrays.fill(memo, -1);
            daySet = new HashSet<>();
            for (int day : days) daySet.add(day);
            return dp(1);
        }
        
        private int dp(int i) {
            if (i > 365) return 0;
            if (memo[i] > 0) return memo[i];
            if (daySet.contains(i)) {
                memo[i] = Math.min(Math.min(dp(i + 1) + costs[0], 
                                            dp(i + 7) + costs[1]), 
                                dp(i + 30) + costs[2]);
            } else {
                memo[i] = dp(i + 1);
            }
            return memo[i];
        }
    }
    ```     
    * Iterative
    ```java
    class Solution {
        public int mincostTickets(int[] days, int[] costs) {
            Set<Integer> daySet = new HashSet<>();
            for (int day : days) daySet.add(day);
            
            int[] dp = new int[366 + 30]; 
            for (int i = 365; i >= 1; --i) {
                if (daySet.contains(i)) {
                    dp[i] = Math.min(dp[i + 1] + costs[0], 
                                    Math.min(dp[i + 7] + costs[1], 
                                            dp[i + 30] + costs[2]));
                } else {
                    dp[i] = dp[i + 1];
                }
            }
            return dp[1];
        }
    }
    ```

<br/>

**反思：**  

一方面要想到从后往前递推的思路，二是逐天考虑，解决输入天数不连续和不同票的时间不同的问题。
