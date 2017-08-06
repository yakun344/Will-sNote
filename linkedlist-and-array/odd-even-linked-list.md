## Odd Even Linked List
_udpate Aug,6 2017 17:43_

---
[LeetCode](https://leetcode.com/problems/odd-even-linked-list/description/)

Given a singly linked list, group all odd nodes together followed by the even nodes. Please note here we are talking about the node number and not the value in the nodes.

You should try to do it in place. The program should run in O(1) space complexity and O(nodes) time complexity.

**Example:**

Given 1->2->3->4->5->NULL,
return 1->3->5->2->4->NULL.

**Note:**
The relative order inside both the even and odd groups should remain as it was in the input. 
The first node is considered odd, the second node even and so on ...

#### Basic Idea:
画示意图会很容易说明，记得使用dummy node。

#### Java Code:
```java
    /**
     * Definition for singly-linked list.
     * public class ListNode {
     *     int val;
     *     ListNode next;
     *     ListNode(int x) { val = x; }
     * }
     */
    public class Solution {
        public ListNode oddEvenList(ListNode head) {
            if (head == null || head.next == null) return head;
            ListNode dummy = new ListNode(0);
            dummy.next = head;
            ListNode odd = head;
            ListNode even = head.next;
            while (even != null && even.next != null) {
                ListNode temp = even.next;
                even.next = even.next.next;
                even = even.next;
                temp.next = odd.next;
                odd.next = temp;
                odd = odd.next;
            }
            return dummy.next;
        }
    }
```