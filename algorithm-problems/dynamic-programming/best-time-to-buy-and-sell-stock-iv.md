# Best Time to Buy and Sell Stock IV

_update Aug 4,2017 9:54_

[LeetCode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/description/)

Say you have an array for which the ith element is the price of a given stock on day i.

Design an algorithm to find the maximum profit. You may complete at most k transactions.

**Note:** You may not engage in multiple transactions at the same time \(ie, you must sell the stock before you buy again\).

## Basic Idea:

延续前面的 I, II and III，这里把 III 的要求从最多允许 2 次交易推广到了最多允许 k 次交易，仅仅分两段考虑就很难满足需求了，于是我们需要在状态方程中增加一个维度 j，表示当前操作之前已经有最多 j 次完整交易。如下图：

![](../../.gitbook/assets/wechatimg7%20%281%29.jpg)

还需要注意 base case，当天数 i=0 的时候，sell的获利一定是0，buy的一定是 -prices\[0\]。当之前最大交易次数 j=0 时，sell 的最大收益一定也是 0，因为 j=0 说明到 i 天为止未进行过完整交易，也就没有 买-&gt;卖 的过程。之后的情况只要按照状态转移方程递推就可以了。

这个方法时间复杂度和空间复杂度都是 O\(k\*n\). 注意到 计算buy只需要同样 j 的sell，计算sell只要前一天的buy，所以我们可以把空间优化到 O\(n\)。

另外还有一种情况，因为每次完整交易都需要至少两天，所以如果 k &gt; n/2，那么我们就可以随意交易，问题就退化为 问题II 了。

## Python Code：

```python
    class Solution(object):
        def maxProfit(self, k, prices):
            """
            :type k: int
            :type prices: List[int]
            :rtype: int
            """
            if not prices: return 0
            if k > len(prices) / 2: return self.easySolution(prices)
            # 初始化 buy 和 sell
            buy = []
            sell = []
            for i in range(k):
                buy.append([0] * len(prices))
                buy[i][0] = -prices[0]
            for i in range(k + 1):
                sell.append([0] * len(prices))
            # 开始递推，依照上图即可
            for j in range(1, k + 1):
                for i in range(1, len(prices)):
                    buy[j - 1][i] = max(buy[j - 1][i - 1], sell[j - 1][i - 1] - prices[i])
                    sell[j][i] = max(sell[j][i - 1], buy[j - 1][i - 1] + prices[i])
            return sell[-1][-1]


        def easySolution(self, prices):
            ret = 0
            for i in range(1, len(prices)):
                t = prices[i] - prices[i - 1]
                if t > 0: ret += t
            return ret
```

## Java Code \(O\(n\) 空间\):

```java
    public class Solution {
        public int maxProfit(int k, int[] prices) {
            if (prices == null || prices.length == 0) return 0;
            int n = prices.length;
            if (k > n / 2) return easySolution(prices);
            int[] buy_last = null;
            int[] buy_this = new int[n];
            int[] sell = new int[n];
            buy_this[0] = -prices[0];
            for (int j = 0; j < k + 1; ++j) {
                for (int i = 1; i < n; ++i) {
                    buy_this[i] = Math.max(buy_this[i - 1], sell[i - 1] - prices[i]);
                    sell[i] = Math.max(sell[i - 1], (j > 0 ? buy_last[i - 1] + prices[i] : 0));
                }            
                buy_last = buy_this;
                buy_this = new int[n];
                buy_this[0] = -prices[0];
            }
            return sell[n - 1];
        }
        private int easySolution(int[] prices) {
            int ret = 0;
            for (int i = 1; i < prices.length; ++i) {
                if (prices[i] > prices[i - 1]) {
                    ret += prices[i] - prices[i - 1];
                }
            }
            return ret;
        }
    }
```

