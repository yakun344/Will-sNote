# Odd Even Linked List

_update Sep 17 2018, 17:11_

[LeetCode](https://leetcode.com/problems/odd-even-linked-list/description/)

Given a singly linked list, group all odd nodes together followed by the even nodes. Please note here we are talking about the node number and not the value in the nodes.

You should try to do it in place. The program should run in O\(1\) space complexity and O\(nodes\) time complexity.

**Example 1:**

```text
Input: 1->2->3->4->5->NULL
Output: 1->3->5->2->4->NULL
```

**Example 2:**

```text
Input: 2->1->3->5->6->4->7->NULL
Output: 2->3->6->7->1->5->4->NULL
```

**Note:**

* The relative order inside both the even and odd groups should remain as it was in the input.
* The first node is considered odd, the second node even and so on ...

## Basic Idea:

基本思路就是用两个dummy node，一个链接奇数node，一个链接原来的。然后把奇数node从原来的中取出来，并维持剩余偶数的顺序不变，保持连接，然后再把奇数和偶数两个list连起来。

### Java Code:

```java
class Solution {
    public ListNode oddEvenList(ListNode head) {
        ListNode dummy1 = new ListNode(0);
        ListNode dummy2 = new ListNode(0);
        dummy2.next = head;
        ListNode i = dummy1, j = dummy2;
        while (j != null && j.next != null) {
            i.next = j.next;
            i = i.next;
            j.next = j.next.next;
            j = j.next;
            i.next = null;
        }
        i.next = dummy2.next;
        return dummy1.next;
    }
}
```

