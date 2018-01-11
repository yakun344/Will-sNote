## Intersection of Two Linked Lists
_update Jun 30, 2017 09:41_

---
[leetcode](https://leetcode.com/problems/intersection-of-two-linked-lists/#/description)
Write a program to find the node at which the intersection of two singly linked lists begins.


For example, the following two linked lists:

      A:          a1 → a2
                         ↘
                           c1 → c2 → c3
                         ↗            
      B:     b1 → b2 → b3
begin to intersect at node c1.


**Notes:**

If the two linked lists have no intersection at all, return null.
The linked lists must retain their original structure after the function returns.
You may assume there are no cycles anywhere in the entire linked structure.
Your code should preferably run in O(n) time and use only O(1) memory.

#### 注意：
基本的思想是很简单的，就是先找到两个head node所对应的链表长度，然后取相同长度，开始同步向后走，检查是否相同。关键是code要漂亮。

#### Java：
```java
    // java
    public class Solution {
        public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
            if (headA == null || headB == null) return null;
            int len1 = lenMeasure(headA);
            int len2 = lenMeasure(headB);
            
            // 注意接下来的部分，非常concise but makes sense.
            
            while (len1 > len2) {
                headA = headA.next;
                len1--;
            }
            while (len1 < len2) {
                headB = headB.next;
                len2--;
            }
            while (headA != headB) {
                headA = headA.next;
                headB = headB.next;
            }
            return headA;
        }
        private int lenMeasure(ListNode head) {
            int len = 0;
            while (head != null) {
                len++;
                head = head.next;
            }
            return len;
        }
    }
```