## Subsets
_update Jul 8, 2017 20:57_

---
[leetcode](https://leetcode.com/problems/subsets/#/description)

Given a set of distinct integers, nums, return all possible subsets.

Note: The solution set must not contain duplicate subsets.

    For example,
    If nums = [1,2,3], a solution is:
    
    [
      [3],
      [1],
      [2],
      [1,2,3],
      [1,3],
      [2,3],
      [1,2],
      []
    ]

#### Basic Idea:
1.  DFS， 根据决策树的思想，我们可以构建一棵二叉树，每个node代表选择或者不选择某个element，只要照此规则写出recursion即可；
2.  逐位考虑， 例如输入数组 [1,2,3,4], 第一层递归为前缀分别为 [1,2,3,4]，对 1 的第二层subtree，考虑 [2,3,4] ...。也可以理解为对结果的第一位，考虑 [1,2,3,4]，然后第二位、第三位，以此类推。事实上，能否看透这种解法是**逐位考虑非常关键**。
3.  DP， 对于每个数，我们考虑是否选择它，如果不选择，则结果不变，如果选择，则结果要加上（之前所有结果后面加上当前数）。所以实际上我们只要做：
```python
    res = [[]]
    for num in sorted(nums):
        res += [pre + [num] for pre in res]
    return res
```
4.  Bit manipulation. 因为长度为length的数组共有(2 ^ length)种subsets，所以我们只要用从 0 至 （2 ^ length - 1）的数作为bitMap，就可以搞定。

#### DFS 决策树，java code：
```java
    public class Solution {
        public List<List<Integer>> subsets(int[] nums) {
            List<List<Integer>> res = new ArrayList<>(); 
            if (nums == null || nums.length == 0) {
                res.add(new ArrayList<>());
                return res;
            }
            Arrays.sort(nums); // not necessary
            dfs(nums, 0, new ArrayList<Integer>(), res);
            return res;
        }
        private void dfs(int[] nums, int depth, List<Integer> path, List<List<Integer>> res) {
            if (depth == nums.length) {
                res.add(new ArrayList<Integer>(path));
                return;
            }
            // dont select nums[depth]
            dfs(nums, depth + 1, path, res);
            // select nums[depth]
            path.add(nums[depth]);
            dfs(nums, depth + 1, path, res);
            // trace back
            path.remove(path.size() - 1);
        }
    }
```

#### 逐位考虑，java code：
python 的在上面，非常简短而且巧妙。
java：
```java
    public class Solution {
        public List<List<Integer>> subsets(int[] nums) {
            List<List<Integer>> res = new ArrayList<>(); 
            if (nums == null || nums.length == 0) {
                res.add(new ArrayList<>());
                return res;
            }
            Arrays.sort(nums);
            dfs(nums, 0, new ArrayList<Integer>(), res);
            return res;
        }
        private void dfs(int[] nums, int pos, List<Integer> path, List<List<Integer>> res) {
            res.add(new ArrayList<Integer>(path));
            for (int i = pos; i < nums.length; ++i) {
                path.add(nums[i]);
                dfs(nums, i + 1, path, res);
                path.remove(path.size() - 1);
            }
        }
    }
```

#### DP, java code:
```java
    public class Solution {
        public List<List<Integer>> subsets(int[] nums) {
            List<List<Integer>> res = new ArrayList<>(); 
            res.add(new ArrayList<Integer>());
            Arrays.sort(nums);
            for (int num : nums) {
                int size = res.size();
                for (int i = 0; i < size; ++i) {
                    System.out.println(num);
                    List<Integer> lst = new ArrayList<>(res.get(i));
                    lst.add(num);
                    res.add(lst);
                }
            }
            return res;
        }
    }
```

#### Bit Manipulation, Java Code:
```java
    // non-recursion, bit manipulation
    public ArrayList<ArrayList<Integer>> subsets(int[] nums) {
        ArrayList<ArrayList<Integer>> res = new ArrayList<>();
        if (nums == null || nums.length == 0) {
            res.add(new ArrayList<Integer>());
            return res;
        } 
        Arrays.sort(nums);
        for (int subset = 0; subset < (1 << nums.length); ++subset) {
            bitMapToRes(nums, subset, res);
        }
        return res;
    }
    private void bitMapToRes(int[] nums, int bitMap, List<ArrayList<Integer>> res) {
        ArrayList<Integer> subset = new ArrayList<>();
        int i = 0;
        while (bitMap != 0) {
            if ((bitMap & 1) == 1) subset.add(nums[i]);
            i++;
            bitMap >>= 1;
        }
        res.add(subset);
    }
```