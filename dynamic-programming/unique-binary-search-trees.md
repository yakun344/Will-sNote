## Unique Binary Search Trees
_update Aug 13,2017  13:18_

---
[LeetCode](https://leetcode.com/problems/unique-binary-search-trees/description/)

Given n, how many structurally unique BST's (binary search trees) that store values 1...n?

**For example,**

Given n = 3, there are a total of 5 unique BST's.

     1         3     3      2      1
      \       /     /      / \      \
       3     2     1      1   3      2
      /     /       \                 \
     2     1         2                 3
     
#### Basic Idea:
[这里](https://discuss.leetcode.com/topic/8398/dp-solution-in-6-lines-with-explanation-f-i-n-g-i-1-g-n-i) 是dp思路的分析。

基本思路就是利用dp的思想，把长度为n的序列的BST个数转化为关于它的子问题的结果的推导公式。即对于长度为n的序列，BST的个数就是以每个元素为root时的bst个数的和。而当某元素k为root时，左边有 k-1 个元素，共有 dp[k-1] 个 BST，右边有 n-k 个元素，共有 dp[n-k] 个BST，则一共为 dp[k-1] + dp[n-k]

例如：
对于 n=4 (dp[4]), 我们考虑以每个元素 k 为root的bst的个数。
*  when k=1, f1 = dp[0]*dp[3]
*  when k=2, f2 = dp[1]*dp[2]
*  when k=3, f3 = dp[2]*dp[1]
*  when k=4, f4 = dp[3]*dp[0]

则 dp[4] = f1 + f2 + f3 + f4 = 5+2+2+5 = 14.

求**所有解本身**的题目分析在[这里](https://will-gxz.gitbooks.io/xiaozheng_algo/content/Tree/unique-binary-search-trees-ii.html);

#### Python Code:
```python
    class Solution(object):
        def numTrees(self, n):
            """
            :type n: int
            :rtype: int
            """
            dp = [0] * (n + 1)
            dp[0] = dp[1] = 1
            for i in range(2, n + 1):
                for j in range(0, n):
                    dp[i] += dp[j] * dp[i - j - 1]
            return dp[n]
```