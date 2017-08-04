## Generate Parentheses
_update Aug,2 2017, 11:47_

---
[LeetCode](https://leetcode.com/problems/generate-parentheses/description/)

Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

**For example** 
    
    given n = 3, a solution set is:
    [
      "((()))",
      "(()())",
      "(())()",
      "()(())",
      "()()()"
    ]
    
#### Basic Idea:
首先会有一个思路：生成所有的组合，再判断是否valid，但是这种思路显然是太慢了，弃之。

之后，我们注意到括号的性质，就是open和close总是成对出现，我们只要保证每添加一个close之前都有足够的（至少一个）open 与之对应即可。因此，我们在每个状态下，依次考虑添加open和添加close的情况，用left和right的个数进行限制。

#### Java Code:
```java
    public class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        dfs(0, 0, n, new StringBuilder(), res);
        return res;
    }
    private void dfs(int left, int right, int n, StringBuilder path, List<String> res) {
        if (left == right && left == n) {
            res.add(path.toString());
            return;
        }
        // 当open数量没有到限制的时候，我们都可以添加open
        if (left < n) {
            path.append("(");
            dfs(left + 1, right, n, path, res);
            path.deleteCharAt(path.length() - 1);
        }
        // 但是要添加close，我们必须保证前面有足够的open
        if (right < left) {
            path.append(")");
            dfs(left, right + 1, n, path, res);
            path.deleteCharAt(path.length() - 1);
        }
    }
}
```