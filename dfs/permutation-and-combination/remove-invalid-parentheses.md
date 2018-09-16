# Remove Invalid Parentheses
_update Sep 14 2018, 21:37_

---

[LeetCode](https://leetcode.com/problems/remove-invalid-parentheses/description/)

Remove the minimum number of invalid parentheses in order to make the input string valid. Return all possible results.

Note: The input string may contain letters other than the parentheses ( and ).

**Example 1:**

    Input: "()())()"
    Output: ["()()()", "(())()"]

**Example 2:**

    Input: "(a)())()"
    Output: ["(a)()()", "(a())()"]

**Example 3:**

    Input: ")("
    Output: [""]

### Basic Idea
题目要求的是去掉最少括号使得括号序列valid的所有可能结果。提到求所有结果，一定会想到DFS，但是如何dfs需要讨论和优化。

如果先得知了左右括号各需要删除多少个，再进行DFS就可以极大减少搜索深度。所以我们可以先用O(n)时间扫描输入string，计算其左右括号各需要删除多少，然后再以该数量做为参数进行DFS。在DFS中，每次考虑是否删除当前括号的情况，并同时跟踪开放括号的数量。如果leftRemove和rightRemove都为0，并且开放括号数量pair也是0，则说明当前结果可行，加入res。

而如何判断左右括号各需要删除多少呢？只要从左向右扫描，维持两个变量leftRemove 和 rightRemove. 如果遇到left括号，则 leftRemove++，而如果遇到right括号，如果leftRemove大于0，即之前有未闭合左括号的话，则leftRemove--，否则rightRemove++。

#### Java Code：
```java
class Solution {
    public List<String> removeInvalidParentheses(String s) {
        int leftRemove = 0, rightRemove = 0;
        for (char c : s.toCharArray()) {
            if (c == '(') {
                leftRemove++;
            } else if (c == ')') {
                if (leftRemove > 0) {
                    leftRemove--;
                } else {
                    rightRemove++;
                }
            }
        }                                                   
        Set<String> res = new HashSet<>();
        dfs(s, leftRemove, rightRemove, 0, 0, "", res);
        return new ArrayList<>(res);
    }

    private void dfs(String s, int leftRemove, int rightRemove, int pair, int pos, String path, Set<String> res) {
        if (leftRemove < 0 || rightRemove < 0 || pair < 0) {
            return;
        } else if (pos == s.length()) {
            if (pair == 0 && leftRemove == 0 && rightRemove == 0) {
                res.add(path);
            }
            return;
        }
        if (s.charAt(pos) == '(') {
            dfs(s, leftRemove, rightRemove, pair + 1, pos + 1, path + "(", res);
            dfs(s, leftRemove - 1, rightRemove, pair, pos + 1, path, res);
        } else if (s.charAt(pos) == ')') {
            dfs(s, leftRemove, rightRemove, pair - 1, pos + 1, path + ")", res);
            dfs(s, leftRemove, rightRemove - 1, pair, pos + 1, path, res);
        } else {
            dfs(s, leftRemove, rightRemove, pair, pos + 1, path + s.charAt(pos), res);
        }
    }
}
```
