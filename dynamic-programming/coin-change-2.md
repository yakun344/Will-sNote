## Coin Change 2 "NOT FINISHED"
_update Jan 10, 2018  14:10_

---
[LeetCode](https://leetcode.com/problems/coin-change-2/description/)

You are given coins of different denominations and a total amount of money. Write a function to compute the number of combinations that make up that amount. You may assume that you have infinite number of each kind of coin.

**Note:**   
You can assume that

  1.  `0 <= amount <= 5000`
  2.  `1 <= coin <= 5000`
  3.  the number of coins is less than 500
  4.  the answer is guaranteed to fit into signed 32-bit integer

**Example 1:** 

    Input: amount = 5, coins = [1, 2, 5]
    Output: 4
    Explanation: there are four ways to make up the amount:
    5=5
    5=2+2+1
    5=2+1+1+1
    5=1+1+1+1+1
    
**Example 2:**  

    Input: amount = 3, coins = [2]
    Output: 0
    Explanation: the amount of 3 cannot be made up just with coins of 2.
    
**Example 3:**  

    Input: amount = 10, coins = [10] 
    Output: 1

<br>

### Basic Idea:

 
<br>

### Implementation：
作为一道经典dp问题，实现的方法有 bottom-up 和 top-down 两种思路，时间空间复杂度类似。

#### Java Code (Bottom-up)



#### Java Code (Top-down)









