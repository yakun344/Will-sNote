## Combination Sum
_update Jul 21, 2017 17:58_

---
[lintcode](http://www.lintcode.com/en/problem/combination-sum/)  
[LeetCode](https://leetcode.com/problems/combination-sum/description/)

Given a set of candidate numbers (C) and a target number (T), find all unique combinations in C where the candidate numbers sums to T.

The same repeated number may be chosen from C unlimited number of times.

** Notice**

*  All numbers (including target) will be positive integers.
*  Elements in a combination (a1, a2, … , ak) must be in non-descending order. (ie, a1 ≤ a2 ≤ … ≤ ak).
*  The solution set must not contain duplicate combinations.

**Example**

    Given candidate set [2,3,6,7] and target 7, a solution set is:
    
    [7]
    [2, 2, 3]
    
#### Basic Idea:
整个程序肯定是Subsets的框架，但是又有一些细节需要注意。

1.  题目说每个数字可以使用多次，隐含了重复元素其实是没有意义的，所以我们可以先去重。去重的方法类似于quick sort中的partition函数，2 pointers。
2.  如何实现多次使用某个元素呢？只要在dfs传递参数时传递 i 而不是 i + 1 即可。

#### Python Code:
```python
    class Solution:
        # @param candidates, a list of integers
        # @param target, integer
        # @return a list of lists of integers
        def combinationSum(self, candidates, target):
            if target <= 0: return [[]]
            if not candidates: return [[]]
            res = []
            # 先排序，去重，因为每个元素可以使用多次，所以可以放心去重
            nums = self.removeDuplicates(candidates)
            self.helper(nums, 0, [], res, target)
            return res
            
        # core function
        def helper(self, nums, pos, path, res, remainSum):
            # int[] nums, int pos, list<int> path, list<list<int>> res, int remainSum
            # return: void
            if remainSum == 0:
                res.append(path[:])
                return
            for i in range(pos, len(nums)):
                if nums[i] > remainSum: break # 因为升序排列
                path.append(nums[i])
                # 这里用 i 而不是 i + 1 是因为可以重复用一个数
                self.helper(nums, i, path, res, remainSum - nums[i])
                del path[-1]
                
            
        # 排序并去重    
        def removeDuplicates(self, arr):
            # int[] arr, return: void
            arr.sort()
            i = 0
            for j in range(len(arr)):
                if arr[i] != arr[j]:
                    arr[i + 1] = arr[j]
                    i += 1
            return arr[: i + 1]
                
``` 

<br>

---
_update Jan 9, 2017  15:35_

### Update: 新的思考
首先我们分析一下之前这种方法的时间空间复杂度。之前的dfs算法中，一共会有 `target / min(candidates)` 层，每一层会有 `len(candidates)` 个分支，如果 input 的 target sum 非常大，会有很深的递归深度，同时也会有很高的时间复杂度（如果input: `{1,5,10,25}`, `target=99`, 则最大递归深度为 `99 / 1 = 99` 层，总时间复杂度 `O(4^99)`）；

接下来，我们换一种方法考虑（详见 **DFS notes**）。这次我们如下定义 DFS：

  1.  共有 `len(candidates)` 层；
  2.  每层考虑使用一个 candidate 不同的次数；
  3.  所以第 i 层有 `remain sum / candidates[i]` 个分支；
  
如此，如果按照刚刚的 input，时间复杂度就变为了 O(99^4)，显著降低。递归深度也从99变4，空间复杂度降低。

<br>

#### 实现思路：
这种方法实现的时候和之前有了较大的不同，但是只要理解了 dfs 具体在每层做了什么就不难写了。因为我们对于每个数考虑不同次数选用它，在dfs中一定会有一个 for loop 来进行这件事情，框架为:  
```java
    for (int i = 0; candidates[pos] * i <= remainSum; ++i) {
        path.add(candidates[pos]);
        dfs(pos + 1, remainSum - i * candidates[pos], ...);
        path.remove(candidates.length - 1);
    }
```




























