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
&emsp; stack中存放当前待push元素和当前min value。每次push的时候比较当前最小和待插入元素，得出新min之后和元素一起插入。  

**优化：**  
&emsp; 如何优化空间呢，因为minStack可能会有很多重复情况。我们可以对每个minVal只存它本身一次，以及一个当前的 stack size，例如当push了 `3, 2, 1, 3, 4, 5` 之后，minStack 中会是:
```
  stack
    5    
    4
    3    minStack
    1      1,6
    2      2,2
    3      3,1
```
当pop时候检查当前minStack中peek对应的size，每次将size -1，如果当前 `stack size < minStack peek size`, 则将 minStack peek 删除。

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

<br>

---
_update May 5,2018 20:03_

#### C++ Code:
```cpp
    class MinStack {
        deque<pair<int, int>> _stack;
    public:
        /** initialize your data structure here. */
        MinStack() {
            
        }
        
        void push(int x) {
            int curr_min;
            if (_stack.empty() || x < _stack.back().second) {
                curr_min = x;
            } else {
                curr_min = _stack.back().second;
            }
            _stack.emplace_back(x, curr_min);
        }
        
        void pop() {
            if (! _stack.empty()) {
                _stack.pop_back();
            }
        }
        
        int top() {
            return _stack.back().first;
        }
        
        int getMin() {
            return _stack.back().second;
        }
    };
    
    /**
     * Your MinStack object will be instantiated and called as such:
     * MinStack obj = new MinStack();
     * obj.push(x);
     * obj.pop();
     * int param_3 = obj.top();
     * int param_4 = obj.getMin();
     */
 ```













