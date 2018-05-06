##  Implement Stack using Queues
_update May 6,2018  16:37_

---
[LeetCode](https://leetcode.com/problems/implement-stack-using-queues/description/)

Implement the following operations of a stack using queues.

    push(x) -- Push element x onto stack.
    pop() -- Removes the element on top of the stack.
    top() -- Get the top element.
    empty() -- Return whether the stack is empty.

**Notes:**

You must use only standard operations of a queue -- which means only push to back, peek/pop from front, size, and is empty operations are valid.

Depending on your language, queue may not be supported natively. You may simulate a queue by using a list or deque (double-ended queue), as long as you use only standard operations of a queue.

You may assume that all operations are valid (for example, no pop or top operations will be called on an empty stack).

### Basic Idea:
正常的做法是无法做到 O(1) push&pop 的，只能对其一做优化。有两种思路，也可能就会是面试时候的两种follow up，一是优化push 的时间，二就是优化 pop() 的时间。

* #### 优化push：
只需要一个queue，每次push的时候直接offer进queue，而在pop的时候，将 size-1 个元素poll后再offer，露出需要pop的元素，然后将其poll后再return。top的操作时可以先pop，然后再将其offer入queue。
#### C++Code
```cpp


* #### 优化pop：
只需要一个queue，每次push的时候都将之前的所有元素依次poll，然后offer到后端，令刚插入的元素在peek的位置。例如如果之前的queue中有 `->[1,2,3]->`, 插入 4 之后，变为 `->[4,1,2,3]->`, 然后依次poll后offer，得到 `->[1,2,3,4]->`. 这样在 pop 操作的时候可以直接poll， O(1) 时间。
##### C++ Code
```cpp
class MyStack {
    deque<int> _queue;
public:
    /** Initialize your data structure here. */
    MyStack() {
        
    }
    
    /** Push element x onto stack. */
    void push(int x) {
        _queue.push_back(x);
        int size = _queue.size();
        for (int i = 0; i < size - 1; ++i) {
            _queue.push_back(_queue.front());
            _queue.pop_front();
        }
    }
    
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        int ret = _queue.front();
        _queue.pop_front();
        return ret;
    }
    
    /** Get the top element. */
    int top() {
        return _queue.front();
    }
    
    /** Returns whether the stack is empty. */
    bool empty() {
        return _queue.empty();
    }
};
```