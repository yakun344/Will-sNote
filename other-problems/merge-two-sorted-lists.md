## Merge Two Sorted Lists
_update Jun 27, 2017_

---
[leetcode](https://leetcode.com/problems/merge-two-sorted-lists/#/description)
Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

**思路：**
这道题目使用recursion比较简单，因为recursion可以很简单的做到先处理后面的nodes再链接前面的nodes，即可以不让前面的nodes丢失后面的部分，而使用iteration就会复杂一些。

Iteration 的方法可以先使用一个无意义的 dummy node 作为头指针，然后用一个curr跟踪当前新链表的最后一个node

#### Recursion，Python code：

```python
    class Solution(object):
        def mergeTwoLists(self, l1, l2):
            """
            :type l1: ListNode
            :type l2: ListNode
            :rtype: ListNode
            """
            return self.merge(l1, l2)
            
        def merge(self, l1, l2):
            if not l1:
                return l2
            if not l2:
                return l1
            if l1.val <= l2.val:
            # 先处理后面的，再拼接前面的
                l1.next = self.merge(l1.next, l2)
                return l1
            else:
                l2.next = self.merge(l1, l2.next)
                return l2
```

#### Non Recursion， Java code：
```java
    // non-recursive solution
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) return l2;
        if (l2 == null) return l1;
        ListNode dummy = new ListNode(0);
        ListNode curr = null;
        if (l1.val < l2.val) {
            curr = l1;
            l1 = l1.next;
        } else {
            curr = l2;
            l2 = l2.next;
        }
        dummy.next = curr;
        while (l1 != null || l2 != null) {
            if (l1 == null) {
                curr.next = l2;
                break;
            } else if (l2 == null) {
                curr.next = l1;
                break;
            } else if (l2.val < l1.val) {
                curr.next = l2;
                curr = curr.next;
                l2 = l2.next;
            } else {
                curr.next = l1;
                curr = curr.next;
                l1 = l1.next;
            }
        }
        return dummy.next;
    }
```