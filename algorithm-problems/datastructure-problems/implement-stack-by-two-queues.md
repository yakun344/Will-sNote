# Implement Stack by Two Queues

_update Aug 24, 2017 16:36_

[LintCode](http://www.lintcode.com/en/problem/implement-stack-by-two-queues/)

Implement a stack by two queues. The queue is first in first out (FIFO). That means you can not directly pop the last element in a queue.

**Example**

```
    push(1)
    pop()
    push(2)
    isEmpty() // return false
    top() // return 2
    pop()
    isEmpty() // return true
```

## Basic Idea:

[这里](https://stackoverflow.com/questions/688276/implement-stack-using-two-queues) 有一个很好的分析，以下内容均基于此；

用两个queue实现stack，无法同时满足O(1)的push和pop，所以有两种不同的实现方法分别对pop和push进行了优化：

* 对** push **优化：
  * push和pop结束后，始终保持 q2 为空；
  * push 时，直接offer进 q1；
  * pop 时，把 q1 中的元素依次放入 q2，只剩余最后一个，移除并返回这个元素，然后另 q1 和 q2 的名字交换；
* 对** pop **优化：
  * 在操作结束后，仍然保持 q2 为空；
  * push：先offer进 q2，然后把 q1 中所有元素依次offer 进 q2，再交换 q1 和 q2 的引用；
  * pop：直接 q1.poll();

具体地，这里因为有top和pop两个操作，使用对 pop 优化的实现方法更为合适。

## Java Code:

```java
    class Stack {
        Deque<Integer> q1;
        Deque<Integer> q2;
        public Stack() {
            this.q1 = new LinkedList<>();
            this.q2 = new LinkedList<>();
        }
        // Push a new item into the stack
        public void push(int x) {
            q2.addFirst(x);
            while (! q1.isEmpty()) {
                q2.addFirst(q1.removeLast());
            }
            Deque<Integer> temp = q1;
            q1 = q2;
            q2 = temp;
        }

        // Pop the top of the stack
        public void pop() {
            q1.removeLast();
        }

        // Return the top of the stack
        public int top() {
            return q1.peekLast();
        }

        // Check the stack is empty or not.
        public boolean isEmpty() {
            return q1.isEmpty();
        }    
    }
```
