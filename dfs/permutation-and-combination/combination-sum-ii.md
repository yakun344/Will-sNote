## Combination Sum II
_update Jul 21, 2017 18:21_

---
[LintCode](http://www.lintcode.com/en/problem/combination-sum-ii/)  
[LeetCode](https://leetcode.com/problems/combination-sum-ii/description/)


Given a collection of candidate numbers (C) and a target number (T), find all unique combinations in C where the candidate numbers sums to T.

Each number in C may only be used once in the combination.

**Notice**

*  All numbers (including target) will be positive integers.
*  Elements in a combination (a1, a2, … , ak) must be in non-descending order. (ie, a1 ≤ a2 ≤ … ≤ ak).
*  The solution set must not contain duplicate combinations.

**Example**    

Given candidate set [10,1,6,7,2,1,5] and target 8,
    
    A solution set is:
    
    [
      [1,7],
      [1,2,5],
      [2,6],
      [1,1,6]
    ]
    
#### Basic Idea:
这道题目和 Combination Sum I 的区别主要是有重复元素，而且每个只能用一次。所以重点就是如何避免重复。方法是，在每次 for loop 考虑pos之后的序列的时候，只考虑第一个重复的值。

#### Java Code (LeetCode version):
```java
class Solution {
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        List<List<Integer>> res = new ArrayList<>();
        Arrays.sort(candidates);
        dfs(candidates, target, 0, res, new ArrayList<Integer>());
        return res;
    }
    
    private void dfs(int[] candidates, int remainSum, int pos, List<List<Integer>> res, List<Integer> path) {
        if (remainSum == 0 || pos == candidates.length) {
            if (remainSum == 0) {
                res.add(new ArrayList<Integer>(path));
            }
            return;
        }
        // 从pos开始往后的所有candidates中选择一个作为该层递归所选择的元素, 注意去重，只选择重复元素序列的第一个
        for (int i = pos; i < candidates.length; ++i) {
            if (i > pos && candidates[i - 1] == candidates[i]) continue; // 去重
            if (candidates[i] > remainSum) break; // 因为已经从小到大排序，如果 ith 不满足，后面都不满足
            path.add(candidates[i]);
            dfs(candidates, remainSum - candidates[i], i + 1, res, path); // 注意下一层的 pos 是 i+ 1
            path.remove(path.size() - 1);
        }
        
    }
}
```

<br>

___
_update Jan 9, 2018 17:46_

### Update
根据前面的 **DFS notes** 中求 combination 的第一种思路解这道题的时候就很无力了，因为如果每层只考虑一个元素是否选择的话，很难去重，考虑如下递归树：
```python
                                 {}(1,1,2)
                       /                         \
                {}(1,2)                           {1}(1,2)
            /        \                          /           \
     {}(2)          {1}(2)                  {1}(2)         {1,1}(2)
   /    \          /     \                /     \         /       \
 {}()   {2}()    {1}()   {1,2}()      {1}()   {1,2}()  {1,1}()  {1,1,2}()

```
可以发现，很难去重，因为重复元素出现在 recursion tree 中的位置可能比较远，不能通过判断剪枝，除非维持全局 HashSet 之类的方法。

而如果使用这里提到的方法（也是在 **DFS notes** 中另外讨论的方法），就可以比较容易地去重。其 recursion tree 如下：
```python
















