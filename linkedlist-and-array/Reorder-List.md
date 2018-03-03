## Reorder List
_update Jan 3,2018 1:28_

---
[LeetCode](https://leetcode.com/problems/reorder-list/description/)


Given a singly linked list `L: L0→L1→…→Ln-1→Ln`,   
reorder it to: `L0→Ln→L1→Ln-1→L2→Ln-2→…`  

You must do this in-place without altering the nodes' values.

**For example,**  
Given {1,2,3,4}, reorder it to {1,4,2,3}.

#### Basic  Idea:
这道题目非常典型地综合了许多 linked list 题目的知识点:

1.  get mid in a linked list;
2.  reverse a linked list;
3.  merge two linked list;

解法步骤：  

1.  先找到中点，将list一分为二；
2.  reverse 后半部分；
3.  merge 前半部分和reversed后半部分；

**注意：** reverse 不可以使用递归，因为如果input list 太长，会 StackOverFlow;

#### Java Code:
```java
class Solution {
    public void reorderList(ListNode head) {
        if (head == null || head.next == null) return;
        ListNode mid = getMid(head);
        ListNode head2 = reverse(mid);
        ListNode dummy = new ListNode(0);
        ListNode curr = dummy, curr1 = head, curr2 = head2;
        while (curr1 != null && curr2 != null) {
            curr.next = curr1;
            curr1 = curr1.next;
            curr.next.next = curr2;
            curr2 = curr2.next;
            curr = curr.next.next;
        }
        if (curr1 != null) curr.next = curr1;
        else if (curr2 != null) curr.next = curr2;
    }
    
    // split the linked list from mid, return the head of the 2nd part
    // 注意特殊处理，需要让slow停在中点之前
    private ListNode getMid(ListNode head) {
        ListNode slow = head, fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        ListNode ret = slow.next;
        slow.next = null;
        return ret;
    }
    
    // reverse the input linked list, return its new head
    private ListNode reverse(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode prev = head, next = null, curr = head.next;
        while (curr != null) {
            next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        head.next = null;
        return prev;
    }
}
```