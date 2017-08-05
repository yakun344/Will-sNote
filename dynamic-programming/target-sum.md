## Target Sum
_update Aug 5,2017 12:13_

---
[LeetCode](https://leetcode.com/problems/target-sum/description/)


You are given a list of non-negative integers, a1, a2, ..., an, and a target, S. Now you have 2 symbols + and -. For each integer, you should choose one from + and - as its new symbol.

Find out how many ways to assign symbols to make sum of integers equal to target S.

**Example 1:**

    Input: nums is [1, 1, 1, 1, 1], S is 3. 
    Output: 5
    Explanation: 
    
    -1+1+1+1+1 = 3
    +1-1+1+1+1 = 3
    +1+1-1+1+1 = 3
    +1+1+1-1+1 = 3
    +1+1+1+1-1 = 3
    
    There are 5 ways to assign symbols to make the sum of nums be target 3.

**Note:**
1. The length of the given array is positive and will not exceed 20.
2. The sum of elements in the given array will not exceed 1000.
3. Your output answer is guaranteed to be fitted in a 32-bit integer.

#### Basic Idea:
**思路1，**暴力枚举，dfs：
考虑所有加减路径，统计总path数。

**思路2，**memoization dfs：
这是上面思路的优化。我们注意到在brute force的过程中会遇到很多 remainSum 及 pos 相同的情况，于是乎我们只要少许改动，将更新全局变量改为返回路径数量。这样，我们就可以缓存 `map[pos][remainSum] = 已经算出的路径数量`， 时间复杂度从 O(2^n) 缩减为 O(l*n) (l 为sum的范围，在这里是2000)

**思路3，**dynamic programming:
考虑到每次添加一个数（+ 或者 -），都会产生重复，我们可以构造一个二维数组，对没个num，用index存储新的sum，对应的value为该pos，该sum的路径数量，同理，用hashMap也可以。

空间复杂度O(l*n), 经过优化，可以到达O(l).

整体思路如图：
![](/assets/WechatIMG9.jpg)

可以看到，这样的确合并了很多重复情况。

#### Java Code:
**思路1：**bfs
```java
    public class Solution {
        private int ret = 0;
        public int findTargetSumWays(int[] nums, int S) {
            dfs(nums, 0, S);
            return ret;
        }
        private void dfs(int[] nums, int pos, int remainSum) {
            if (pos == nums.length) {
                if (remainSum == 0) ret += 1;
                return;
            }
            dfs(nums, pos + 1, remainSum + nums[pos]);
            dfs(nums, pos + 1, remainSum - nums[pos]);
        }
    }
```

**思路2：**memoization
```java
    // with memoization
    public class Solution {
        public int findTargetSumWays(int[] nums, int S) {
            Integer[][] map = new Integer[nums.length + 1][2001];
            return dfs(nums, 0, S, map);
        }
        // 将每个 pos 、 remainSum 的情况存入map
        private int dfs(int[] nums, int pos, int remainSum, Integer[][] map) {
            if (remainSum > 1000 || remainSum < -1000) return 0;
            if (pos == nums.length) {
                if (remainSum == 0) {
                    return 1;
                }
                return 0;
            }
            if (map[pos][remainSum + 1000] != null) return map[pos][remainSum + 1000];
            int ret = 0;
            ret += dfs(nums, pos + 1, remainSum + nums[pos], map);
            ret += dfs(nums, pos + 1, remainSum - nums[pos], map);
            map[pos][remainSum + 1000] = ret;
            return ret;
        }
    }
```

**思路3：**dp
```java
    // dynamic programming, 使用数组index记录和的大小，value记录当前和path个数,然后每次向后扩展（当前path个数 + 新来路path个数）
    public class Solution {
        public int findTargetSumWays(int[] nums, int S) {
            if (nums == null || nums.length == 0 || S > 1000 || S < -1000) return 0;
            int[][] map = new int[nums.length + 1][2001];
            map[0][0 + 1000] = 1;
            for (int pass = 1; pass < map.length; ++pass) {
                for (int i = -1000; i < 1001; ++i) {
                    if (map[pass - 1][i + 1000] > 0) {
                        int pathNum = map[pass - 1][i + 1000];
                        int sum1 = i + nums[pass - 1];
                        int sum2 = i - nums[pass - 1];
                        map[pass][sum1 + 1000] += pathNum;
                        map[pass][sum2 + 1000] += pathNum;
                    }
                }
            }
            return map[nums.length][S + 1000];
        }
    }
```

```java
    // dynamic programming, 使用hashMap，key: curr_sum, value: path_number
    public class Solution {
        public int findTargetSumWays(int[] nums, int S) {
            if (nums == null || nums.length == 0 || S > 1000 || S < -1000) return 0;
            Map<Integer, Integer> last = new HashMap<>();
            last.put(0, 1); // key: curr_sum, value: path_number
            for (int i = 0; i < nums.length; ++i) {
                Map<Integer, Integer> curr = new HashMap<>();
                for (int last_sum : last.keySet()) {
                    int path_num = last.get(last_sum);
                    int sum1 = last_sum + nums[i];
                    int sum2 = last_sum - nums[i];
                    curr.put(sum1, curr.getOrDefault(sum1, 0) + path_num);
                    curr.put(sum2, curr.getOrDefault(sum2, 0) + path_num);
                }
                last = curr;
            }
            return last.getOrDefault(S, 0);
        }
    }
```
   
    
     
      
        
