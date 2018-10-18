# Fruit into baskets
_update 2018-10-18 13:37:10_

---
[LeetCode](https://leetcode.com/problems/fruit-into-baskets/)

In a row of trees, the `i-th` tree produces fruit with type `tree[i]`.

You start at any tree of your choice, then repeatedly perform the following steps:

Add one piece of fruit from this tree to your baskets.  If you cannot, stop.
Move to the next tree to the right of the current tree.  If there is no tree to the right, stop.
Note that you do not have any choice after the initial choice of starting tree: you must perform step 1, then step 2, then back to step 1, then step 2, and so on until you stop.

You have two baskets, and each basket can carry any quantity of fruit, but you want each basket to only carry one type of fruit each.

What is the total amount of fruit you can collect with this procedure?

 

**Example 1:**

    Input: [1,2,1]
    Output: 3
    Explanation: We can collect [1,2,1].

**Example 2:**

    Input: [0,1,2,2]
    Output: 3
    Explanation: We can collect [1,2,2].
    If we started at the first tree, we would only collect [0, 1].

**Example 3:**

    Input: [1,2,3,2,2]
    Output: 4
    Explanation: We can collect [2,3,2,2].
    If we started at the first tree, we would only collect [1, 2].

**Example 4:**

    Input: [3,3,3,1,2,1,1,2,3,3,4]
    Output: 5
    Explanation: We can collect [1,2,1,1,2].
    If we started at the first tree or the eighth tree, we would only collect 4 fruits.
    

**Note**:

1. `1 <= tree.length <= 40000`
2. `0 <= tree[i] < tree.length`
   
<br/>

### Basic Idea:
这道题就是要找给定数组中最长的只包含至多两个不同数字的子数组的长度。我们可以维持两个变量分别表示两个数字，并且跟踪当前最右边数字左边连续相同数字的长度currLen，以及当前满足条件的subarray的长度len。然后根据遇到的新数字，更新这四个变量。解就是所有len中最大的值。

#### Java Code:
```java
class Solution {
    public int totalFruit(int[] tree) {
        int typeA = Integer.MAX_VALUE;
        int typeB = Integer.MAX_VALUE;
        int currLen = 0;
        int len = 0;
        int ret = 0;
        for (int i = 0; i < tree.length; ++i) {
            int currType = tree[i];
            if (currType == typeB) {
                currLen++;
                len++;
            } else if (currType == typeA) {
                typeA = typeB;
                typeB = currType;
                len++;
                currLen = 1;
            } else {
                typeA = typeB;
                typeB = currType;
                len = currLen + 1;
                currLen = 1;
            }
            ret = Math.max(ret, len);
        }
        return ret;
    }
}
```
