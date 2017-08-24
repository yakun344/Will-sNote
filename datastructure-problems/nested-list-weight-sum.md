## Nested List Weight Sum
_update Aug 24, 2017  15:10_

---
[LintCode](http://www.lintcode.com/en/problem/nested-list-weight-sum/)

Given a nested list of integers, return the sum of all integers in the list weighted by their depth. Each element is either an integer, or a list -- whose elements may also be integers or other lists.

**Example**    

    Given the list [[1,1],2,[1,1]], return 10. 
    (four 1's at depth 2, one 2 at depth 1, 4 * 1 * 2 + 1 * 2 * 1 = 10)
    
    Given the list [1,[4,[6]]], return 27. 
    (one 1 at depth 1, one 4 at depth 2, and one 6 at depth 3; 1 + 42 + 63 = 27)
    
#### Basic Idea:
**思路1，recursion dfs：**
最直观的方法就是用递归写一个dfs，每层传入一个depth变量；

java：
```java
    public class Solution {
        public int depthSum(List<NestedInteger> nestedList) {
            int ret = 0;
            for (NestedInteger ni : nestedList) {
                ret += helper(ni, 1);
            }
            return ret;
        }
        private int helper(NestedInteger ni, int depth) {
            if (ni.isInteger()) {
                return ni.getInteger() * depth;
            } else {
                List<NestedInteger> lst = ni.getList();
                int ret = 0;
                for (NestedInteger n : lst) {
                    ret += helper(n, depth + 1);
                }
                return ret;
            }
        }
    }
```

**思路2，iteration dfs**
因为需要记录depth，所以非递归dfs稍显麻烦。为了解决这个问题，在python中可以直接存入stack一个`tuple<NestedInteger, depth>`，而在Java中，可能新建一个inner class（可以叫做stackElement）比较合适。

python：
```python
    # iterative dfs solution
    class Solution(object):
        # @param {NestedInteger[]} nestedList a list of NestedInteger Object
        # @return {int} an integer
        def depthSum(self, nestedList):
            stack = []
            ret = 0
            for ni in nestedList:
                stack.append((ni, 1)) # stack 中存入tuple，标明深度
            while stack:
                temp = stack.pop()
                ni = temp[0]
                depth = temp[1]
                if ni.isInteger():
                    ret += ni.getInteger() * depth
                else:
                    lst = ni.getList()
                    for n in lst:
                        stack.append((n, depth + 1))
            return ret
```

**思路3，bfs**
bfs的思路也比较自然，对于depth的问题只需在bfs的过程中记录层数即可；

```python
    # BFS solution
    class Solution(object):
        # @param {NestedInteger[]} nestedList a list of NestedInteger Object
        # @return {int} an integer    
        def depthSum(self, nestedList):
            queue = collections.deque()
            ret = 0
            for ni in nestedList:
                queue.appendleft(ni)
            depth = 1
            while queue:
                size = len(queue)
                # 对每层，如果是integer，则计算，如果是list，则把list中的元素加入queue作为下一层
                for i in range(size):
                    ni = queue.pop()
                    if ni.isInteger():
                        ret += depth * ni.getInteger()
                    else:
                        lst = ni.getList()
                        for n in lst:
                            queue.appendleft(n)
                depth += 1
            return ret
```
