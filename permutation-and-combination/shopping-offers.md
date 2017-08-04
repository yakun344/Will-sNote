## Shopping Offers
_update Aug,3 2017 20:21_

---
[LeetCode](https://leetcode.com/problems/shopping-offers/description/)

In LeetCode Store, there are some kinds of items to sell. Each item has a price.

However, there are some special offers, and a special offer consists of one or more different kinds of items with a sale price.

You are given the each item's price, a set of special offers, and the number we need to buy for each item. The job is to output the lowest price you have to pay for exactly certain items as given, where you could make optimal use of the special offers.

Each special offer is represented in the form of an array, the last number represents the price you need to pay for this special offer, other numbers represents how many specific items you could get if you buy this offer.

You could use any of special offers as many times as you want.

**Example 1:**

    Input: [2,5], [[3,0,5],[1,2,10]], [3,2]
    Output: 14
    
Explanation: 

There are two kinds of items, A and B. Their prices are $2 and $5 respectively. 
In special offer 1, you can pay $5 for 3A and 0B
In special offer 2, you can pay $10 for 1A and 2B. 
You need to buy 3A and 2B, so you may pay $10 for 1A and 2B (special offer #2), and $4 for 2A.

**Example 2:**

    Input: [2,3,4], [[1,1,0,4],[2,2,1,9]], [1,2,1]
    Output: 11
    
Explanation: 

The price of A is $2, and $3 for B, $4 for C. 
You may pay $4 for 1A and 1B, and $9 for 2A ,2B and 1C. 
You need to buy 1A ,2B and 1C, so you may pay $4 for 1A and 1B (special offer #1), and $3 for 1B, $4 for 1C. 
You cannot add more items, though only $9 for 2A ,2B and 1C.

**Note:**

There are at most 6 kinds of items, 100 special offers.
For each item, you need to buy at most 6 of them.
You are not allowed to buy more items than you want, even if that would lower the overall price.

#### 题意：
输入是价格表，special offer，以及需求表。要求输出恰好买到需求表所示数量商品的最低价格。

#### 思路：
思路就是搜索所有用各种 special offer 的情况，同时每层都要对比不适用 special offer 的情况，以防止 special offer 坑爹。代码就是 dfs 的写法。

#### Java Code:
    ```java
    public class Solution {
        private int lowestPrice;
        public int shoppingOffers(List<Integer> price, List<List<Integer>> special, List<Integer> needs) {
            lowestPrice = noOfferPrice(price, needs);
            dfs(price, special, needs, 0);
            return lowestPrice;
        }
        private void dfs(List<Integer> price, 
                         List<List<Integer>> special,
                         List<Integer> remainNeeds,
                         int currPrice) {
            // 如果恰好完成
            if (noNeed(remainNeeds)) {
                lowestPrice = Math.min(lowestPrice, currPrice);
                return;
            }
            // 如果下面不用offer
            lowestPrice = Math.min(lowestPrice, currPrice + noOfferPrice(price, remainNeeds));
            // 逐个考虑special offer
            for (int i = 0; i < special.size(); ++i) {
                List<Integer> offer = special.get(i);
                List<Integer> needs_used = useOffer(remainNeeds, offer);
                if (needs_used == null) continue;
                int new_price = currPrice + offer.get(offer.size() - 1);
                dfs(price, special, needs_used, new_price);
            }
        }
        // 计算当前need list中的商品的 原价 的和
        private int noOfferPrice(List<Integer> price, List<Integer> needs) {
            int ret = 0;
            for (int i = 0; i < needs.size(); ++i) {
                ret += needs.get(i) * price.get(i);
            }
            return ret;
        }
        // 计算使用某 special offer 之后剩余的 need list，如果有物品数量变为负数，则return null
        private List<Integer> useOffer(List<Integer> needs, List<Integer> offer) {
            // 如果可以用，返回减去offer之后的needs，否则返回null
            List<Integer> ret = new ArrayList<>(needs);
            for (int i = 0; i < needs.size(); ++i) {
                int good_need = ret.get(i);
                int good_offer = offer.get(i);
                int new_good = good_need - good_offer;
                if (new_good < 0) return null;
                ret.set(i, new_good);
            }
            return ret;
        }
        // 判断当前的 need list 是否刚好所有物品数量都为 0 .
        private boolean noNeed(List<Integer> needs) {
            for (int n : needs) if (n > 0) return false;
            return true;
        }
    }
```

#### Python Code:
```python
    class Solution:
        def __init__(self):
            self.minPrice = float('INF')
            
        def shoppingOffers(self, price, special, needs):
            """
            :type price: List[int]
            :type special: List[List[int]]
            :type needs: List[int]
            :rtype: int
            """ 
            self.dfs(price, special, needs, 0)
            return self.minPrice
            
            
        # int[] price, int[][] special, int[] remainNeeds, int currPrice
        def dfs(self, price, special, remainNeeds, currPrice):
            if self.isDone(remainNeeds): self.minPrice = min(self.minPrice, currPrice)
            # 考虑如果剩余商品不适用 special offer
            self.minPrice = min(self.minPrice, currPrice + self.originalPrice(price, remainNeeds))
            # dfs
            for offer in special:
                offerPrice = offer[-1]
                usedNeeds = self.useOffer(offer, remainNeeds)
                if not usedNeeds: continue
                self.dfs(price, special, usedNeeds, currPrice + offerPrice)
                
            
        # int[] offer, int[] needs
        # 计算应用offer之后的needs，如果有商品数量变为负数则返回 None
        def useOffer(self, offer, needs):
            ret = []
            for i in range(len(needs)):
                goodNum = needs[i] - offer[i]
                if goodNum < 0: return None
                ret.append(goodNum)
            return ret
        
        # 判断是否已经恰好没有需要
        def isDone(self, needs):
            return not [item for item in needs if item > 0]    
            
        # 计算原价总和
        def originalPrice(self, price, needs):
            return sum(a * b for (a, b) in zip(price, needs))
```