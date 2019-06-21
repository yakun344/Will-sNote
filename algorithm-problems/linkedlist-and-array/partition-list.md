# Partition List

_update Nov 27, 2017 14:48_

[LeetCode](https://leetcode.com/problems/partition-list/description/)

Given a linked list and a value x, partition it such that all nodes less than x come before nodes greater than or equal to x.

You should preserve the original relative order of the nodes in each of the two partitions.

**For example,** Given 1-&gt;4-&gt;3-&gt;2-&gt;5-&gt;2 and x = 3, return 1-&gt;2-&gt;2-&gt;4-&gt;3-&gt;5.

## Basic Idea

这道题目是要求实现一个对 linked list 的 partition，特别的，它的左边不是 `<= x`, 而是 `< x`。

基本的思路是把 小于x 的部分和 大于等于x 的部分用两个list来表示，之后再把两个list连在一起。例如：dummy1 为head的list为小于x的nodes，dummy2的则为大于等于x的nodes.

## Python Code:

```python
    class Solution:
        def partition(self, head, x):
            """
            :type head: ListNode
            :type x: int
            :rtype: ListNode
            """
            dummy1 = ListNode(0) # smaller than x
            dummy2 = ListNode(0) # equal or larger than x
            curr1 = dummy1
            curr2 = dummy2

            curr = head
            while curr:
                if curr.val < x:
                    curr1.next = curr
                    curr = curr.next
                    curr1 = curr1.next
                    curr1.next = None
                else:
                    curr2.next = curr
                    curr = curr.next
                    curr2 = curr2.next
                    curr2.next = None

            curr1.next = dummy2.next
            return dummy1.next
```

## Java Code:

```java
    class Solution {
        public ListNode partition(ListNode head, int x) {
            ListNode dummy1 = new ListNode(0);
            ListNode dummy2 = new ListNode(0);
            ListNode curr1 = dummy1;
            ListNode curr2 = dummy2;

            ListNode curr = head;
            while (curr != null) {
                if (curr.val < x) {
                    curr1.next = curr;
                    curr = curr.next;
                    curr1 = curr1.next;
                    curr1.next = null;
                } else {
                    curr2.next = curr;
                    curr = curr.next;
                    curr2 = curr2.next;
                    curr2.next = null;
                }
            }

            curr1.next = dummy2.next;
            return dummy1.next;
        }
    }
```

