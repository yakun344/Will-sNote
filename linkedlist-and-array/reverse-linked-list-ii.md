## Reverse Linked List II
_update Sep 9, 2017 16:56_

---
[LeetCode](https://leetcode.com/problems/reverse-linked-list-ii/description/)

Reverse a linked list from position m to n. Do it in-place and in one-pass.

**For example:**

    Given 1->2->3->4->5->NULL, m = 2 and n = 4,
    
    return 1->4->3->2->5->NULL.

**Note:**

Given m, n satisfy the following condition:  
`1 ≤ m ≤ n ≤ length of list.`

#### Basic Idea:
1. 先找到 M.prev；
2. reverse M 到 N 之间的指针，并且得到 N.next；
3. 拼接： `M.prev.next = N; M.next = N.next;`

#### Java Code:
```java
    class Solution {
        public ListNode reverseBetween(ListNode head, int m, int n) {
            ListNode dummy = new ListNode(0);
            dummy.next = head;
            ListNode curr = dummy;
            // find prev of m
            for (int i = 0; i < m - 1; ++i) {
                curr = curr.next;
            }
            ListNode before = curr;
            ListNode M = curr.next;
            // reverse m到n之间的指针
            curr = M;
            ListNode prev = null;
            ListNode next = curr.next;
            for (int i = 0; i < n - m; ++i) {
                prev = curr;
                curr = next;
                next = curr.next;
                curr.next = prev;
            }
            // 拼接两端，此时next为 N.next
            before.next = curr;
            M.next = next;
            return dummy.next;
        }
    }
```


