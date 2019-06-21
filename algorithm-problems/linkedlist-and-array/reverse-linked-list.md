# Reverse Linked List

_update Nov 29,2017 2:35_

[LeetCode](https://leetcode.com/problems/reverse-linked-list/description/)

Reverse a singly linked list.

**Hint:** A linked list can be reversed either iteratively or recursively. Could you implement both?

### Basic Idea:

两种思路，一是 iteration，一是recursion。

1. Iteration 需要注意 prev，curr，next 三个变量的使用, 还要注意最后要让 `head.next = null`；
2. Recursion 要注意当`reverse(head.next)`之后，head仍然指向之前指向的node，即reverse之后的tail，于是我们可以直接利用 `head.next.next = head; head.next = null` 来将之前的head链接好。

## Java Code:

**Iterative:** **一定要特别注意：** 在reverse完成之后，要断开之前的head.next，否则返回的链表会有环；

```java
    class Solution {
        public ListNode reverseList(ListNode head) {
            if (head == null || head.next == null) return head;
            ListNode curr = head.next;
            ListNode prev = head;
            ListNode next = null;
            while (curr != null) {
                next = curr.next;
                curr.next = prev;
                prev = curr;
                curr = next;
            }
            head.next = null; // 这里很重要
            return prev;
        }
    }
```

**Recursive:**

```java
    class Solution {
        public ListNode reverseList(ListNode head) {
            if (head == null || head.next == null) return head;
            ListNode newHead = reverseList(head.next);
            head.next.next = head; // 记录 head.next 实际上是 reverse(head.next) 的尾
            head.next = null;
            return newHead;
        }
    }
```

_update May 6,2018 15:45_

## C++ Code:

令 prev 从 nullptr 开始，可以使code更简单，因为可以一开始就令 `head->next = nullptr`；

```cpp
    class Solution {
    public:
        ListNode* reverseList(ListNode* head) {
            if (head == nullptr || head->next == nullptr) return head;
            ListNode* prev = nullptr;
            ListNode* curr = head;
            ListNode* next = nullptr;
            while (curr != nullptr) {
                next = curr->next;
                curr->next = prev;
                prev = curr;
                curr = next;
            }
            return prev;
        }
    };
```

