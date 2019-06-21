# Linked List Cycle II

_update Sep 9,2017 14:58_

[LeetCode](https://leetcode.com/problems/linked-list-cycle-ii/description/)

Given a linked list, return the node where the cycle begins. If there is no cycle, return null.

**Note:** Do not modify the linked list.

**Follow up:**  
Can you solve it without using extra space?

## Basic Idea:

1. 两个指针，slow and fast，从起点出发，如果相遇，则说明有环；
2. 保持slow指针在相遇点，新建slow2指针在起点，两指针同时出发，相遇点就是入口；

## Java Code:

需要注意，初始的时候slow和fast必须同时从start出发；

```java
    public class Solution {
        public ListNode detectCycle(ListNode head) {
            if (head == null || head.next == null) return null;
            ListNode slow = head, fast = head;
            while (fast != null && fast.next != null) {
                slow = slow.next;
                fast = fast.next.next;
                if (slow == fast) break;
            }
            if (fast == null || fast.next == null) return null;

            ListNode slow2 = head;
            while (slow2 != slow) {
                slow2 = slow2.next;
                slow = slow.next;
            }
            return slow;
        }
    }
```

