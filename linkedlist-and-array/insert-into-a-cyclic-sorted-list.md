## Insert into a Cyclic Sorted List
_update Jul 24, 2017 15:40_

---
[LintCode](http://www.lintcode.com/en/problem/insert-into-a-cyclic-sorted-list/)

Given a node from a cyclic linked list which has been sorted, write a function to insert a value into the list such that it remains a cyclic sorted list. The given node can be any single node in the list. Return the inserted new node.

**Notice**
*  3->5->1 is a cyclic list, so 3 is next node of 1.
*  3->5->1 is same with 5->1->3

**Example**

    Given a list, and insert a value 4:
    3->5->1
    Return 5->1->3->4
    
#### Basic Idea:
1.  考虑当输入是null时的情况
2.  当可以找到 `curr <= target <= next` 时的情况
3.  target 比所有node值都大或者都小的情况

---
_update 2017-11-21 01:59:08_


---

#### Python Code:
```python
    """
    Definition of ListNode
    class ListNode(object):
    
        def __init__(self, val, next=None):
            self.val = val
            self.next = next
    """
    class Solution:
        # @param {ListNode} node a list node in the list
        # @param {int} x an integer
        # @return {ListNode} the inserted new list node
        def insert(self, node, x):
            target = ListNode(x)
            if node is None:
                target.next = target
                return target
            
            curr = node
            while True:
                if (curr.next is node) or (curr.val <= x and curr.next.val >=x) or \
                   (curr.val > curr.next.val and (curr.val > x and curr.next.val > x or \
                                                  curr.val < x and curr.next.val < x)):
                    target.next = curr.next
                    curr.next = target
                    return target
                curr = curr.next
            return None
```