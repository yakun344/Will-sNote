# Reverse Linked List
_update Nov 29,2017  2:35_

---
[LeetCode](https://leetcode.com/problems/reverse-linked-list/description/)

Reverse a singly linked list.


**Hint:**
A linked list can be reversed either iteratively or recursively. Could you implement both?

#### Basic Idea:
两种思路，一是 iteration，一是recursion。  
i. Iteration 需要注意 prev，curr，next 三个变量的使用, 还要注意最后要让 `head.next = null`；
ii. Recursion 要注意当`reverse(head.next)`之后，head仍然指向之前指向的node，即reverse之后的tail，于是我们可以直接利用 `head.next.next = head; head.next = null` 来将之前的head链接好。

### Java Code:
**Iterative:**
```java
