## Remove Duplicates from Sorted List
_update May 5,2018  0:52_

---
[LeetCode](https://leetcode.com/problems/remove-duplicates-from-sorted-list/description/)


Given a sorted linked list, delete all duplicates such that each element appear only once.

**Example 1:**
    
    Input: 1->1->2
    Output: 1->2

**Example 2:**

    Input: 1->1->2->3->3
    Output: 1->2->3
    
### Basic Idea:
两种思路，recursion 和 iteration。

### C++ Code
#### Recursion
```cpp
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (head == nullptr || head->next == nullptr) return head;
        head->next = deleteDuplicates(head->next);
        return head->val == head->next->val ? head->next : head;
    }
};
```

#### Iteration
```cpp
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (head == nullptr || head->next == nullptr) return head;
        ListNode* prev = head;
        ListNode* curr = head->next;
        while (curr != nullptr) {
            if (prev->val == curr->val) {
                curr = curr->next;
            } else {
                prev->next = curr;
                prev = curr;
                curr = curr->next;
            }
        }
        prev->next = nullptr;
        return head;
    }
};
```