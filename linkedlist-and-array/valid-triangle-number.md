## Valid Triangle Number
_update Aug 2,2017 22:36_

---
[LeetCode](https://leetcode.com/problems/valid-triangle-number/description/)

Given an array consists of non-negative integers, your task is to count the number of triplets chosen from the array that can make triangles if we take them as side lengths of a triangle.

**Example 1:**

    Input: [2,2,3,4]
    Output: 3
    Explanation:
    Valid combinations are: 
    2,3,4 (using the first 2)
    2,3,4 (using the second 2)
    2,2,3

**Note:**

The length of the given array won't exceed 1000.
The integers in the given array are in the range of [0, 1000].

#### Basic Idea:
[这里](http://fisherlei.blogspot.com/2017/07/leetcode-valid-triangle-number-solution.html)有一个双指针解法的说明.

有**三种思路**，大致说一下：
1.  首先想到的是暴力枚举三条边然后判断是否valid，用combination的dfs算法，时间复杂度是(C(n,3))，O(n^3)，结果TLE了；
2.  接下来想到我们可以先排序原数组，固定两条边a,b，计算出第三条边的范围(a + b > c)，然后用binary search找，找到之后target和b之间的点都可以构成第三边。枚举所有两边的组合耗时O(n^2)，bi search每次耗时O(logN)，总的 time complexity 是 O(n^2 * log(n))。AC了;
3.  接下来我们想到可以先排序，然后固定第三边 c 从最右端往左移。对每个 c，使用双指针找与之相配的a, b的范围（a 从最左端向右，b 从 c-1 向左，如果满足 a+b>c 则将 a,b 之间点的个数加入结果，作为 a 的可能值，然后 b--。如果不满足 a+b>c ，则令 a 右移一位，继续判断。到 a >= b 结束，c--，进行下一波判断）。

#### Python Code:
```python
    # 解法1，排序，对于每个a，b，用二分法确定c的最大值(excluded)
    class Solution(object):
        def triangleNumber(self, nums):
            """
            :type nums: List[int]
            :rtype: int
            """
            # binary search to find the index of last element that less than target
            def biSearch(start, target):
                if start >= len(nums): return -1
                p = start
                r = len(nums) - 1
                while p + 1 < r:
                    q = p + (r - p) / 2
                    if nums[q] >= target:
                        r = q
                    else:
                        p = q
                if nums[r] < target: return r
                if nums[p] < target: return p
                else: return -1
            
            nums.sort()
            ret = 0
            for i in range(len(nums) - 1):
                for j in range(i + 1, len(nums)):
                    if nums[i] == 0 or nums[j] == 0: continue
                    target = nums[i] + nums[j]
                    idx_c = biSearch(j + 1, target)
                    if idx_c < 0: continue
                    ret += idx_c - j 
            return ret if ret > 0 else 0
```

```python
    # 解法2， 双指针遍历，每次固定第三条边，找a,b的范围
    class Solution:
        """
        @param: S: A list of integers
        @return: An integer
        """
        def triangleCount(self, S):
            # 两个短边的和大于第三边就可以组成三角形
            # 所以可以排序后，从右向左选择边长作为第三边，用双指针确定另外两边组合数量
            # 三边分别为 a,b,c, 时间复杂度为O(n^2)
            if not S or len(S) < 3:
                return 0
            S.sort()
            res = 0
            for c in range(len(S) - 1, 1, -1):
                ## 接下来转变为求a，b的和大于c的组合个数的问题
                ## 每次固定b，找a的最小值，然后res加上b-a
                a, b = 0, c - 1
                while a < b:
                    if S[a] + S[b] > S[c]:
                        res += b - a
                        b -= 1
                    else:
                        a += 1
            return res
```

#### Java Code：
```java
    // brute force then check
    public class Solution {
        private int count = 0;
        public int triangleNumber(int[] nums) {
            dfs(nums, 0, 3, new ArrayList<Integer>());
            return count;
        }
        private void dfs(int[] nums, int pos, int remainLength, List<Integer> path) {
            if (remainLength == 0) {
                if (isValid(path)) count++;
                return;
            }
            if (remainLength > 0 && pos >= nums.length) return;
            for (int i = pos; i < nums.length; ++i) {
                path.add(nums[i]);
                dfs(nums, i + 1, remainLength - 1, path);
                path.remove(path.size() - 1);
            }
        }
        private boolean isValid(List<Integer> edges) {
            int a = edges.get(0);
            int b = edges.get(1);
            int c = edges.get(2);
            if (a + b <= c || a + c <= b || c + b <= a) return false;
            return true;
        }
    }
```

```java
    // two pointers
    public class Solution {
        public int triangleNumber(int[] nums) {
            Arrays.sort(nums);
            int ret = 0;
            for (int c = nums.length - 1; c >= 2; --c) {
                int a = 0;
                int b = c - 1;
                while (a < b) {
                    if (nums[a] + nums[b] > nums[c]) {
                        ret += b - a;
                        b--;
                    } else {
                        a++;
                    }
                }
            }
            return ret;
        }
    }
```