## Best Time to Buy and Sell Stock III
_update Aug,3 2017 11:44_

---
[LeetCode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/description/)

Say you have an array for which the ith element is the price of a given stock on day i.

Design an algorithm to find the maximum profit. You may complete at most two transactions.

**Note:**
You may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).

#### Basic Idea:
**思路 1：**
首先想到既然只允许至多两次交易，我们可以把n天分成两段，然后分别考虑：
first[i]  表示从第 0 天到第 i 天的最大利润；
second[i] 表示从第 i 天到第 n-1 天的最大利润；
然后遍历，找到最大的 first[i]+second[i+1] 即可。

具体地，求first的时候，从左向右，maintain一个当前最低价格，每次计算更新maxProfit；
求second的时候，从右向左，maintain一个当前maxPrice，每次更新maxProfit；

python实现如下：
```python
    class Solution(object):
        def maxProfit(self, prices):
            """
            :type prices: List[int]
            :rtype: int
            """
            if not prices: return 0
            first = [0] * len(prices)
            second = [0] * len(prices)
            # 计算first
            minPrice = prices[0]
            for i in range(1, len(first)):
                minPrice = min(minPrice, prices[i])
                first[i] = max(first[i - 1], prices[i] - minPrice)
            # 计算second
            maxPrice = prices[-1]
            second[-1] = 0
            for i in range(len(prices) - 2, -1, -1):
                maxPrice = max(maxPrice, prices[i])
                second[i] = max(second[i + 1], maxPrice - prices[i])
            # 计算结果
            ret = 0
            for i in range(len(prices) - 1):
                ret = max(ret, first[i] + second[i])
            return ret
```

**思路 2：**
直接考虑最多允许 k 次交易的情况，在状态的表示中增加一个维度，表示在之前最多有几次交易：
```java
    buy[j][i] = max{ buy[j][i-1], sell[j][i-1]-prices[i] }
    sell[j][i]= max{ sell[j][i-1], buy[j-1][i-1]+prices[i] }
```
详细解释见下一题：IV。
Python Code：
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










