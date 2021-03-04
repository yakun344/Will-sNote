# Lexicographical Numbers

_update Aug 11, 2017 16:40_

[LeetCode](https://leetcode.com/problems/lexicographical-numbers/description/)

Given an integer n, return 1 - n in lexicographical order.

For example, given 13, return: \[1,10,11,12,13,2,3,4,5,6,7,8,9\].

Please optimize your algorithm to use less time and space. The input size may be as large as 5,000,000.

## Basic Idea:

基本思想要从这组数字的生成方式上下手，画出recursion tree，然后写dfs。

![](../../.gitbook/assets/WechatIMG15%20%281%29.jpg)

## Java Code:

```java
    public class Solution {
        public List<Integer> lexicalOrder(int n) {
            List<Integer> res = new LinkedList<>();
            for (int i = 1; i < 10; ++i) {
                dfs(res, i, n);
            }
            return res;
        }
        private void dfs(List<Integer> res, int path, int n) {
            if (path <= n) {
                res.add(path);
            } else {
                return;
            }
            for (int i = 0; i < 10; ++i) {
                dfs(res, path * 10 + i, n);
            }
        }
    }
```

