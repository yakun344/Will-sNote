# Combination Sum III

_update Aug,2 2017 10:14_

[LeetCode](https://leetcode.com/problems/combination-sum-iii/description/)

Find all possible combinations of k numbers that add up to a number n, given that only numbers from 1 to 9 can be used and each combination should be a unique set of numbers.

**Example 1:**

```text
Input: k = 3, n = 7

Output:

[[1,2,4]]

Example 2:

Input: k = 3, n = 9

Output:

[[1,2,6], [1,3,5], [2,3,4]]
```

## Basic Idea:

需要注意的是这道题目限制了每个combination的size，所以要多跟踪一个参数 remainLen。

## Java Code:

```java
    public class Solution {
        public List<List<Integer>> combinationSum3(int k, int n) {
            List<List<Integer>> res = new ArrayList<>();
            dfs(1, k, n, new ArrayList<Integer>(), res);
            return res;
        }
        // 对给定path（前缀）生成所有的组合，跟踪path和累和，如果最终（remainLen == 0）的时候 remainSum == 0，则加入res
        // 如果在过程中 remainSum < 0, 则说明当前path不可行，直接返回. 
        // 为了不出现相同元素，使用start标志下一层开始考虑的数字
        private void dfs(int start, int remainLen, int remainSum, 
                         List<Integer> path, List<List<Integer>> res) {
            if (remainLen == 0 && remainSum == 0) {
                res.add(new ArrayList<Integer>(path));
                return;
            }
            if (remainSum <= 0 || remainLen == 0) return;
            for (int i = start; i <= 9; ++i) {
                path.add(i);
                dfs(i + 1, remainLen - 1, remainSum - i, path, res);
                path.remove(path.size() - 1);
            }
        }
    }
```

## Python Code：

```python
    class Solution(object):
        def combinationSum3(self, k, n):
            """
            :type k: int
            :type n: int
            :rtype: List[List[int]]
            """
            def dfs(start, remainLen, remainSum, path, res):
                if remainLen == 0 and remainSum == 0:
                    res.append(path[:])
                    return
                if remainLen == 0 or remainSum <= 0:
                    return
                for i in range(start, 10):
                    path.append(i)
                    dfs(i + 1, remainLen - 1, remainSum - i, path, res)
                    del path[-1]


            res = []
            dfs(1, k, n, [], res)
            return res
```

