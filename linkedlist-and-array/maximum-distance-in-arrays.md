## Maximum Distance in Arrays
_update Jun 29, 2017_

---
[leetcode](https://leetcode.com/problems/maximum-distance-in-arrays/#/description)
Given m arrays, and each array is sorted in ascending order. Now you can pick up two integers from two different arrays (each array picks one) and calculate the distance. We define the distance between two integers a and b to be their absolute difference |a-b|. Your task is to find the maximum distance.

**Example 1:**

      Input:  
       [ [1,2,3],
         [4,5],
         [1,2,3] ]
      Output: 4
    Explanation: 
    One way to reach the maximum distance 4 is to pick 1 in the first or third array and pick 5 in the second array.

**Note:**
Each given array will have at least 1 number. There will be at least two non-empty arrays.
The total number of the integers in all the m arrays will be in the range of [2, 10000].
The integers in the m arrays will be in the range of [-10000, 10000].

#### 思路
解题关键是利用好每个list已经排序的性质，逐一跟进，更新`currMax, currMin, res`。在更新的过程中，永远不会出现 `currMax - currMin`，这样就可以避免在同一个数组中选定了极大极小之后计算出错误答案。
这里的 `currMax 和 currMin` 其实可以理解为之前所有数组中的最大和最小，我们计算 res 的时候，永远在用**当前**数组的**最大最小**和之前的最大最小进行计算，所以自然不会出现同数组内两个值干扰答案。最终整个算法的时间复杂度是`O(n)`。

#### Python code:
```python
    class Solution:
        def maxDistance(self, arrays):
            """
            :type arrays: List[List[int]]
            :rtype: int
            """
            currMax = -10001
            currMin = 10001
            res = 0
            for lst in arrays:
                if lst:
                    res = max(res, max(currMax - lst[0], lst[-1] - currMin))
                    currMax, currMin = max(currMax, lst[-1]), min(currMin, lst[0])
            return res
```

#### Java code：
```java
    public class Solution {
        public int maxDistance(List<List<Integer>> arrays) {
            int currMax = -10000;
            int currMin = 10000;
            int res = 0;
            for (List<Integer> lst : arrays) {
                if (lst.size() == 0) continue;
                res = Math.max(res, Math.max(currMax - lst.get(0), lst.get(lst.size() - 1) - currMin));
                currMin = Math.min(currMin, lst.get(0));
                currMax = Math.max(currMax, lst.get(lst.size() - 1));
            }
            return res;
        }
    }
```