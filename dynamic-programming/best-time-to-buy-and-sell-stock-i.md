## Best Time To Buy and Sell Stock I
_update Aug 2,2017 21:46_

---
[LeetCode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)

Say you have an array for which the ith element is the price of a given stock on day i.

If you were only permitted to complete at most one transaction (ie, buy one and sell one share of the stock), design an algorithm to find the maximum profit.

**Example 1:**

    Input: [7, 1, 5, 3, 6, 4]
    Output: 5
    
    max. difference = 6-1 = 5 (not 7-1 = 6, as selling price needs to be larger than buying price)
    Example 2:
    Input: [7, 6, 4, 3, 1]
    Output: 0
    
    In this case, no transaction is done, i.e. max profit = 0.
    
#### Basic Idea:
**[第一个](https://code.mforever78.com/algorithm/2016/02/24/a-series-of-dynamic-programming-problem/), [第二个](http://www.jianshu.com/p/e1512206957c), [第三个](http://www.cnblogs.com/en-heng/p/7257071.html)**, 这里有三个非常好的这个系列题目的讲解。

对于这道题，只要找到数组中右边和左边数字的最大差即可，我们可以利用 Kanane 算法的思想，一次累加两数之差，当这个值小于零的时候，我们从零重新开始，表示抛弃前面的数字重新开始累加。

另一种思路是跟踪左边最小值，沿途记录最大差。**这种思路更通用。**

**DP 思路的思考：**
假设dp[i]为第 i 天的最大利润，则在第 i 天有两种选择，即卖掉股票和已经卖掉股票，由此我们可以得到状态转移方程：`dp[i] = max{ dp[i-1], prices[i]-minPrices }`，其中minPrices为第 i 天之前的买点。之前的方法可以视为对dp的优化。

代码如下：
```java
    // dp
    public class Solution {
        public int maxProfit(int[] prices) {
            if (prices == null || prices.length == 0) return 0;
            int minPrice = prices[0];
            int[] dp = new int[prices.length];
            dp[0] = 0;
            for (int i = 1; i < dp.length; ++i) {
                minPrice = Math.min(prices[i], minPrice);
                dp[i] = Math.max(dp[i -1], prices[i] - minPrice);
            }
            return dp[prices.length - 1];
        }
    }
```

#### Code:
思路1：
```python
    class Solution(object):
        def maxProfit(self, prices):
            """
            :type prices: List[int]
            :rtype: int
            """
            maxProfit = 0
            curr = 0
            for i in range(1, len(prices)):
                curr += prices[i] - prices[i - 1]
                curr = curr if curr > 0 else 0
                maxProfit = maxProfit if maxProfit >= curr else curr
            return maxProfit   
```

思路2：
```java
    public class Solution {
        public int maxProfit(int[] prices) {
            if (prices == null || prices.length == 0) return 0;
            int minPrice = Integer.MAX_VALUE;
            int maxProfit = Integer.MIN_VALUE;
            for (int i = 0; i < prices.length; ++i) {
                minPrice = Math.min(prices[i], minPrice);
                maxProfit = Math.max(maxProfit, prices[i] - minPrice);
            }
            return maxProfit;
        }
    }
```