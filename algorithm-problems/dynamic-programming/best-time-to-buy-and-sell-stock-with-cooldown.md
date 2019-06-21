# Best Time to Buy and Sell Stock with Cooldown

_update Aug 2,2017 20:58_

[LeetCode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/description/)

Say you have an array for which the i-th element is the price of a given stock on day i.

Design an algorithm to find the maximum profit. You may complete as many transactions as you like \(ie, buy one and sell one share of the stock multiple times\) with the following restrictions:

You may not engage in multiple transactions at the same time \(ie, you must sell the stock before you buy again\). After you sell your stock, you cannot buy stock on next day. \(ie, cooldown 1 day\)

**Example:**

```text
prices = [1, 2, 3, 0, 2]
maxProfit = 3
transactions = [buy, sell, cooldown, buy, sell]
```

## Basic Idea:

[**这里**](https://soulmachine.gitbooks.io/algorithm-essentials/java/dp/best-time-to-buy-and-sell-stock-with-cooldown.html)是算法珠玑中关于这道题目的解释，感觉非常棒，基本上很清楚地讲解了这道题目。

这里再概括一下： 1.两个状态，buy and sell，cold down 可以在sell的状态转换方程中表示。 2.状态转换方程：

```text
  sell[i] = max{ sell[i-1], buy[i-1] + prices[i] };
  buy[i]  = max{ buy[i-1], sell[i-1] - prices[i] };
```

3.关于上面**转换方程的解释**：buy的方程表示 如果当前状态只可以选择不动作和购入股票，那么必须在将现有股票卖掉再购入能赚钱的情况下再如此操作。而sell的则表示如果现在的股价高于收购价，则可以出售，否则保持原状。

## Java Code:

```java
    public class Solution {
        public int maxProfit(int[] prices) {
            if (prices == null || prices.length == 0) return 0;
            int[] sell = new int[prices.length];
            int[] buy = new int[prices.length];
            sell[0] = 0;
            buy[0] = -prices[0];
            for (int i = 1; i < prices.length; ++i) {
                buy[i] = Math.max(buy[i - 1], (i > 1 ? sell[i - 2] : 0) - prices[i]);
                sell[i] = Math.max(sell[i - 1], buy[i - 1] + prices[i]);
            }
            return sell[prices.length - 1];
        }
    }
```

