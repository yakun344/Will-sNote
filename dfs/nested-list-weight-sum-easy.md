# Nested List Weight Sum
_update Jan 26,2018 11:12_

---
[LeetCode](https://leetcode.com/problems/nested-list-weight-sum/description/)

Given a nested list of integers, return the sum of all integers in the list weighted by their depth.

Each element is either an integer, or a list -- whose elements may also be integers or other lists.

**Example 1:**

    Given the list [[1,1],2,[1,1]], return 10. 
    (four 1's at depth 2, one 2 at depth 1)

**Example 2:**

    Given the list [1,[4,[6]]], return 27. 
    (one 1 at depth 1, one 4 at depth 2, and one 6 at depth 3; 1 + 4*2 + 6*3 = 27)
    
<br>

## Basic Idea:
最简单的思路就是DFS，写一个helper function，传入一个depth，从1开始每层累加。

* #### Java Code:
```java
    class Solution {
        public int depthSum(List<NestedInteger> nestedList) {
            // DFS
            return dfs(nestedList, 1);
        }
        
        private int dfs(List<NestedInteger> list, int depth) {
            int ret = 0;
            for (NestedInteger ni : list) {
                if (ni.isInteger()) {
                    ret += depth * ni.getInteger();
                } else {
                    ret += dfs(ni.getList(), depth + 1);
                }
            }
            return ret;
        }
    }
```