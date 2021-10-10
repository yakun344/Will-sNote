# Reverse Linked List II

_update Sep 9, 2017 16:56_

[LeetCode](https://leetcode.com/problems/reverse-linked-list-ii/description/)

Reverse a linked list from position m to n. Do it in-place and in one-pass.

**For example:**

```
Given 1->2->3->4->5->NULL, m = 2 and n = 4,

return 1->4->3->2->5->NULL.
```

**Note:**

Given m, n satisfy the following condition:\
`1 ≤ m ≤ n ≤ length of list.`

## Basic Idea:

1. 先找到 M.prev；
2. reverse M 到 N 之间的指针，并且得到 N.next；
3. 拼接： `M.prev.next = N; M.next = N.next;`

## Java Code:

```java
    class Solution {
        public ListNode reverseBetween(ListNode head, int m, int n) {
            ListNode dummy = new ListNode(0);
            dummy.next = head;

            // find mth first
            ListNode preHead = dummy;
            for (int i = 0; i < m - 1; ++i) preHead = preHead.next;
            ListNode mth = preHead.next;

            // reverse mth - nth
            ListNode curr = mth;
            ListNode prev = null;
            ListNode next = null;
            for (int i = 0; i < n - m + 1; ++i) {
                next = curr.next;
                curr.next = prev;
                prev = curr;
                curr = next;
            }
            ListNode nth = prev;
            ListNode tailNext = curr;

            // 连接
            preHead.next = nth;
            mth.next = tailNext;

            return dummy.next;
        }
    }
```

## Python Code

```python
    class Solution:
        def reverseBetween(self, head, m, n):
            """
            :type head: ListNode
            :type m: int
            :type n: int
            :rtype: ListNode
            """
            dummy = ListNode(0)
            dummy.next = head
            preHead = dummy

            # 先找到 m-th
            for i in range(m - 1):
                preHead = preHead.next    
            mth = preHead.next

            # reverse m-n
            prev = None
            curr = mth
            next = None
            for i in range(n - m + 1):
                next = curr.next
                curr.next = prev
                prev = curr
                curr = next
            tailNext = curr
            nth = prev

            # 连接
            preHead.next = nth
            mth.next = tailNext

            return dummy.next
```

\__\__\__\__\__\__\__\_

_update Jun 23, 2021_

### _Java Code:_

新的写法，发现画个图会更容易理解

```java
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        if (left == right) return head;
        ListNode dummy = new ListNode(Integer.MAX_VALUE, head);
        ListNode prev = dummy, curr = head, next = head.next;
        for (int i = 1; i < left; ++i) {
            prev = prev.next;
            curr = curr.next;
            next = next.next;
        }
        
        // 1. 4. 3. 2. 5. 6. 7. 8 , 反转 256
        //       |  |     |
        //      p1  p2    p3      p2 将会是反转后的右边, p3是左边
        ListNode p1 = prev;
        ListNode p2 = curr;
        // 开始反转
        for (int i = 0; i < right - left; ++i) {
            prev = curr;
            curr = next;
            next = curr.next;
            curr.next = prev;
        }
        // 拼接
        ListNode p3 = curr;
        p1.next = p3;
        p2.next = next;
        return dummy.next;
    }
}
```
