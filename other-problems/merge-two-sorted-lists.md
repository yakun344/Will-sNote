## Merge Two Sorted Lists
_update Jun 27, 2017_

---
[leetcode](https://leetcode.com/problems/merge-two-sorted-lists/#/description)
Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

**思路：**
这道题目使用recursion比较简单，因为recursion可以很简单的做到先处理后面的nodes再链接前面的nodes，即可以不让前面的nodes丢失后面的部分，而使用iteration就会复杂一些。

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
    public class Solution {
        public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
            if (l1 == null) return l2;
            if (l2 == null) return l1;
            ListNode head, node1, node2;
            if (l1.val <= l2.val) {
                head = l1;
                node1 = l1.next;
                node2 = l2;
            } else {
                head = l2;
                node1 = l1;
                node2 = l2.next;
            }
            ListNode prev = head;
        // 使用 node1 和 node2 跟踪需要比较的两个node，prev 表示当前构建的链表的最后一个节点。 
            while (node1 != null && node2 != null) {
                if (node1.val <= node2.val) {
                    prev.next = node1;
                    node1 = node1.next;
                    prev = prev.next;
                } else {
                    prev.next = node2;
                    node2 = node2.next;
                    prev = prev.next;
                }
            }
            prev.next = node1 == null ? node2 : node1;
            return head;
        }
    }
```