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

  1.  If the two linked lists have no intersection at all, return null.
  2.  The linked lists must retain their original structure after the function returns.
  3.  You may assume there are no cycles anywhere in the entire linked structure.
  4.  Your code should preferably run in O(n) time and use only O(1) memory.

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

<br>

---
_update May 5,2018  20:49_

#### C++ Code:
```cpp
    class Solution {
        int getLen(ListNode* head) {
            if (head == nullptr) return 0;
            int len = 0;
            while (head != nullptr) {
                head = head->next;
                ++len;
            }
            return len;
        }
    public:
        ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
            int len1 = getLen(headA);
            int len2 = getLen(headB);
            
            // make headA be the head of the longer list
            if (len1 < len2) {
                ListNode* t = headA;
                headA = headB;
                headB = t;
            }
            for (int i = 0; i < abs(len1 - len2); ++i) {
                headA = headA->next;
            }
            while (headA != headB) {
                headA = headA->next;
                headB = headB->next;
            }
            if (headA == nullptr || headB == nullptr) return nullptr;
            else return headA;
        }
    };
```