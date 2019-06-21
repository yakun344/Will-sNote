# Best Time To Buy And Sell Stock II

_udpate Aug,3 2017 10:08_

[LeetCode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/)

Say you have an array for which the ith element is the price of a given stock on day i.

Design an algorithm to find the maximum profit. You may complete as many transactions as you like \(ie, buy one and sell one share of the stock multiple times\). However, you may not engage in multiple transactions at the same time \(ie, you must sell the stock before you buy again\).

## Basic Idea:

**思路1（dp）：** 考虑到每天我们都有两种**最终状态**：持股（购入或已经购入buy）和未持股（卖掉sell），\(这种将**最终状态归纳为持股和未持股的思路非常利于理解这道题**\)。对于这两种状态，我们可以由此得出状态转移方程：

```java
    buy[i]  = max{ buy[i-1], sell[i-1] - prices[i] }
    sell[i] = max{ sell[i-1], buy[i-1] + prices[i] }
```

在第i天，

* 如果最终持股（buy），对比（**前一天已经持股**的状态）和（前一天未持股最好状态减去今日股价（**今日买入**））的最大收益；
* 如果当天最终未持股（sell），我们对比（**前一天未持股**状态）和（前一天持股状态加今日股价（**今日卖出**））的最大收益；

最终解在 `sell[-1]` \(即不持股\)。代码如下：

```java
    public class Solution {
        public int maxProfit(int[] prices) {
            if (prices == null || prices.length == 0) return 0;
            int[] buy = new int[prices.length];
            int[] sell = new int[prices.length];
            buy[0] = -prices[0];
            sell[0] = 0;
            for (int i = 1; i < prices.length; ++i) {
                buy[i] = Math.max(buy[i - 1], sell[i - 1] - prices[i]);
                sell[i] = Math.max(sell[i - 1], buy[i - 1] + prices[i]);
            }
            return sell[sell.length - 1];
        }
    }
```

**思路2（观察）：** 我们可以做到在所有上升序列都获利，所以只要prices\[i+1\]-prices\[i\]&gt;0, 就可以被加入ret；

```python
    class Solution(object):
        def maxProfit(self, prices):
            """
            :type prices: List[int]
            :rtype: int
            """
            ret = 0
            for i in range(1, len(prices)):
                diff = prices[i] - prices[i - 1]
                if diff > 0:
                    ret += diff
            return ret
```

