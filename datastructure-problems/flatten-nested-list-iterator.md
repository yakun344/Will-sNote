## Flatten Nested List Iterator
_update Aug 24, 22:50_

---
[LeetCode](https://leetcode.com/problems/flatten-nested-list-iterator/description/)

Given a nested list of integers, implement an iterator to flatten it.

Each element is either an integer, or a list -- whose elements may also be integers or other lists.

**Example 1:**

    Given the list [[1,1],2,[1,1]],
    
    By calling next repeatedly until hasNext returns false, 
    the order of elements returned by next should be: [1,1,2,1,1].

**Example 2:**

    Given the list [1,[4,[6]]],
    
    By calling next repeatedly until hasNext returns false, 
    the order of elements returned by next should be: [1,4,6].
    
#### Basic Idea:
基本思想就是利用stack，反向压栈，这样先取出来的就是左边的元素，如果该元素不是Integer，则解析出list之后把新的list反向压栈；

#### Code
Java
```java
    public class NestedIterator implements Iterator<Integer> {
        Deque<NestedInteger> stack = null;
        public NestedIterator(List<NestedInteger> nestedList) {
            stack = new LinkedList<NestedInteger>();
            for (int i = nestedList.size() - 1; i >= 0; --i) {
                stack.addLast(nestedList.get(i));
            }
        }
    
        @Override
        public Integer next() {
            return stack.removeLast().getInteger();
        }
    
        @Override
        public boolean hasNext() {
            for (;;) {
                if (stack.isEmpty()) return false;
                if (stack.peekLast().isInteger()) return true;
                List<NestedInteger> lst = stack.removeLast().getList();
                for (int i = lst.size() - 1; i >= 0; --i) {
                    stack.addLast(lst.get(i));
                }
            }
        }
    }
```

Python
```python
    class NestedIterator(object):
    
        def __init__(self, nestedList):
            self.stack = []
            for i in range(len(nestedList) - 1, -1, -1):
                self.stack.append(nestedList[i])
            
        # @return {int} the next element in the iteration
        def next(self):
            return self.stack.pop().getInteger()
            
        # @return {boolean} true if the iteration has more element or false
        def hasNext(self):
            if not self.stack:
                return False
            if self.stack[-1].isInteger():
                return True
            ni = self.stack.pop().getList()
            for i in range(len(ni) - 1, -1, -1):
                self.stack.append(ni[i])
            return self.hasNext()
```
            