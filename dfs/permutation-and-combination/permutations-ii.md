## Permutations II
_update Jul 21, 2017 21:04_

---
[LintCode](http://www.lintcode.com/en/problem/permutations-ii/)
Given a list of numbers with duplicate number in it. Find all unique permutations.

**Example**
      
      For numbers [1,2,2] the unique permutations are:
      
      [
        [1,2,2],
        [2,1,2],
        [2,2,1]
      ]
      
#### Basic Idea:
每次考虑一位数，维持一个boolean[] used 数组，选过的数字设为true。另外要注意去重。

#### Java Code:
```java
    class Solution {
        /**
         * @param nums: A list of integers.
         * @return: A list of unique permutations.
         */
        public List<List<Integer>> permuteUnique(int[] nums) {
            // Write your code here
            List<List<Integer>> res = new ArrayList<>();
            if (nums == null || nums.length == 0) {
                res.add(new ArrayList<Integer>());
                return res;
            }
            Arrays.sort(nums);
            dfs(nums, new boolean[nums.length], 0, new ArrayList<Integer>(), res);
            return res;
        } 
        private void dfs(int[] nums, boolean[] used, int pos, List<Integer> path, List<List<Integer>> res) {
            if (pos == nums.length) {
                res.add(new ArrayList<Integer>(path));
                return;
            }
            int prev = Integer.MAX_VALUE;
            for (int i = 0; i < nums.length; ++i) {
                if (used[i] || nums[i] == prev) continue;
                prev = nums[i];
                used[i] = true;
                path.add(nums[i]);
                dfs(nums, used, pos + 1, path, res);
                path.remove(path.size() - 1);
                used[i] = false;
            }
        }
    }
```

#### Python Code:
```python
    class Solution:
        """
        @param nums: A list of integers.
        @return: A list of unique permutations.
        """
        def permuteUnique(self, nums):
            if not nums:
                return [[]]
            nums.sort()
            res = []
            used = [False] * len(nums)
            self.helper(nums, 0, used, [], res)
            return res
            
            
        def helper(self, nums, pos, used, path, res):
            # int[] nums, int pos, boolean[] used, list<int> path, list<list<int>> res
            if pos == len(nums):
                res.append(path[:])
                return
            for i in range(len(nums)):
                if used[i]: continue
                if i != 0 and nums[i] == nums[i - 1] and not used[i - 1]: continue
                path.append(nums[i])
                used[i] = True
                self.helper(nums, pos + 1, used, path, res)
                used[i] = False
                del path[-1]
```

<br>

---
_update Jan 8,2017  12:24_

### Upate：使用 Swap-Swap 方法处理，降低了时间复杂度
参考前面的 《DFS notes》;

















