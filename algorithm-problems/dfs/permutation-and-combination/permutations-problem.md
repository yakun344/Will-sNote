# Permutations

_update Jan 8,2017 12:24_

[LeetCode](https://leetcode.com/problems/permutations/description/)

Given a collection of distinct numbers, return all possible permutations.

**For example,**

```text
[1,2,3] have the following permutations:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

## Basic Idea \(Swap-Swap\):

首先思路一定是使用dfs，根据之前 DFS-notes 中所述的思路，我们考虑该dfs共有几层，每层代表什么，有几个分支：

* 该 dfs 可以分为 n 层；
* 每层表示一个 position 上的元素；
* 每层的分支个数为当前位置所有候选元素的个数，根据 level==position，可知分支个数为 `n - level`；

具体实现的时候，不再采用之前的维持一个 `boolean[] used`，每次循环 n 次查找可用元素的方法（会造成`O(n^n)`的时间复杂度），而是用Swap-Swap的方法，即在每层递归中，我们用把选中的元素换到当前position的方法，将 input array 直接当做path，这样到了下一层，就可以在当前position到末尾选一个元素换到position，然后返回后在 backtrack 回去。

**时间复杂度为：** `O(n!)`, 根据数学方法分析，答案的规模为 `n!`；根据递归树分析，共有 n 层，每层每点分支个数从 n 递减到 0，为 `n * (n-1) * (n-2) * ... * 1 = n!`

## Java Code:

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums == null || nums.length == 0) return res;
        dfs(nums, res, 0);
        return res;
    }

    private void dfs(int[] nums, List<List<Integer>> res, int pos) {
        if (pos == nums.length) { // 所有位都已经确定
            List<Integer> lst = new ArrayList<>();
            for (int num : nums) lst.add(num);
            res.add(lst);
            return;
        }
        // 循环考虑每个候选数字，将其换到当前pos之后继续向后递归
        for (int i = pos; i < nums.length; ++i) {
            swap(nums, pos, i);
            dfs(nums, res, pos + 1);
            swap(nums, pos, i); // backtracking
        }
    }

    private void swap(int[] nums, int a, int b) {
        int t = nums[a];
        nums[a] = nums[b];
        nums[b] = t;
    }
}
```

_update 2018-10-04 01:33:36_

## Update：Iterative Solution

```c
举例说明，如果input array 为 [1,2,3]
我们先生成： res = [[1], [2], [3]]
然后将其中每个list拿出来，在其后append上下一个没有重复过的数字，再放回res，
  我们得到：res = [[1,2], [1,3], [2,1], [2,3], [3,1], [3,2]]
重复上面步骤，直到res中每个list的长度等于nums.length, 我们就结束。
```

* **Java Code:**

  ```java
  class Solution {
      public List<List<Integer>> permute(int[] nums) {
          List<List<Integer>> res = new ArrayList<>();
          if (nums.length == 0) return res;
          res.add(new ArrayList<>());
          for (int i = 0; i < nums.length; ++i) {
              List<List<Integer>> newRes = new ArrayList<>();
              for (List<Integer> path : res) {
                  for (int num : nums) {
                      if (! path.contains(num)) {
                          List<Integer> newPath = new ArrayList<>(path);
                          newPath.add(num);
                          newRes.add(newPath);
                      }
                  }
              }
              res = newRes;
          }
          return res;
      }
  }
  ```

