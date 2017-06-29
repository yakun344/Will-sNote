## Palindrome Linked List
_update Jun 29, 2017_

---
[leetcode](https://leetcode.com/problems/palindrome-linked-list/#/description)
Given a singly linked list, determine if it is a palindrome.

**Follow up**:
Could you do it in O(n) time and O(1) space?

#### 思路：
要检查链表是否是回文链表有很多方法，最简单的就是traverse一遍，把链表的val存入list，然后检查是否回文。但是题目要求用O(1)的空间，所以我们只能采取特殊方法，即先用快慢指针找到中点和尾节点，然后reverse后半部分，再比较。

#### Java code：
```java
    public class Solution {
        public boolean isPalindrome(ListNode head) {
            if (head == null) return true;
            if (head.next == null) return true;
            if (head.next.next == null) return head.val == head.next.val;
            ListNode slow = head;
            ListNode fast = head;
            int halfLength = 0;
            while (fast.next != null && fast.next.next != null) {
                slow = slow.next;
                fast = fast.next.next;
                halfLength++;
            }
            // 检查node个数奇偶，如果恰好fast在最右边，则是奇数，此时slow在正中间，则
            // halfLength --，否则的话需要手动把fast右移一位。
            if (fast.next == null) {
                halfLength--;
            } else {
                fast = fast.next;
            }
            ListNode right = reverse(slow.next); // reverse之后从两头比较。
            for (int i = 0; i < halfLength + 1; ++i) {
                if (right.val != head.val) return false;
                right = right.next;
                head = head.next;
            }
            return true;
        }
        private ListNode reverse(ListNode node) {
            if (node == null || node.next == null) return node;
            ListNode prev = null;
            ListNode curr = node;
            ListNode next = null;
            while (curr != null) {
                next = curr.next;
                curr.next = prev;
                prev = curr;
                curr = next;
            }
            return prev;
        }
    }
```