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

---
_update Nov 29, 2017_

#### 更新
1. 代码更加模块化，将findMid和split合并起来，写成一个split函数，在这个函数中，直接完成了将list从中间一分为二，并且对奇偶的不同情况进行处理。如果是奇数个，返回时候已经删去了正中间的node。
2. 这次的 fast 起点为 slow.next，这样可以使得循环退出的时候 slow 指向中点之前，避免需要跟踪 slow 之前一个 node 的指针。

**Java Code update：**
```java
    class Solution {
        public boolean isPalindrome(ListNode head) {
            if (head == null || head.next == null) return true;
            ListNode mid = reverse(split(head));
            while (head != null || mid != null) {
                if (head == null || mid == null) return false;
                if (head.val != mid.val) return false;
                head = head.next;
                mid = mid.next;
            }
            return true;
        }
        
        private ListNode reverse(ListNode head) {
            if (head == null || head.next == null) return head;
            ListNode node = reverse(head.next);
            head.next.next = head;
            head.next = null;
            return node;
        }
        
        // 以左边中点将list分成两组，如果结束时候fast在最右边，则说明list中有偶数个node
        // 此时slow指向左中点。否则的话，slow指向中点的左边，需要手动右移。
        private ListNode split(ListNode head) {
            ListNode preSlow = null;
            ListNode slow = head;
            ListNode fast = head.next;
            while (fast != null && fast.next != null && fast.next.next != null) {
                preSlow = slow;
                slow = slow.next;
                fast = fast.next.next;
            }
            if (fast.next != null) { // odd number, 删掉slow右边的真中点
                slow.next = slow.next.next;
            }
            ListNode ret = slow.next;
            slow.next = null;
            
            return ret;
        }
    }
```











