## Min Stack
_update Jun30, 2017 22:36_

---
[leetcode](https://leetcode.com/problems/min-stack/#/description)
Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

*  push(x) -- Push element x onto stack.
*  pop() -- Removes the element on top of the stack.
*  top() -- Get the top element.
*  getMin() -- Retrieve the minimum element in the stack.
    
        Example:
        MinStack minStack = new MinStack();
        minStack.push(-2);
        minStack.push(0);
        minStack.push(-3);
        minStack.getMin();   --> Returns -3.
        minStack.pop();
        minStack.top();      --> Returns 0.
        minStack.getMin();   --> Returns -2.
        
#### 思路：
stack中存放当前待push元素和当前min value。每次push的时候比较当前最小和待插入元素，得出新min之后和元素一起插入。

#### Python code：
```python
    class MinStack(object):
    
        def __init__(self):
            """
            initialize your data structure here.
            """
            self.stack = []
            
        def push(self, x):
            """
            :type x: int
            :rtype: void
            """
            if not self.stack or x < self.getMin():
                self.stack.append((x, x))
            else:
                self.stack.append((x, self.getMin()))
    
        def pop(self):
            """
            :rtype: void
            """
            self.stack.pop()
    
        def top(self):
            """
            :rtype: int
            """
            return self.stack[-1][0]
    
        def getMin(self):
            """
            :rtype: int
            """
            return self.stack[-1][1]
    
    
    # Your MinStack object will be instantiated and called as such:
    # obj = MinStack()
    # obj.push(x)
    # obj.pop()
    # param_3 = obj.top()
    # param_4 = obj.getMin()
```