## Reverse Linked List in Pairs
_update Jan 17,2018 1:15_

---
Reverse pairs of elements in a singly-linked list.

**Examples**

    L = null, after reverse is null
    L = 1 -> null, after reverse is 1 -> null
    L = 1 -> 2 -> null, after reverse is 2 -> 1 -> null
    L = 1 -> 2 -> 3 -> null, after reverse is 2 -> 1 -> 3 -> null
    
### Basic Idea:
利用递归的思路可以比较轻易的完成，如下图所示：
```c
    input：
      1 -> 2 -> 3 -> 4 -> 5 -> 6 -> ... -> null
    将第三个node及其之后看做子问题：
               |----------------------------|
      1-> 2 -> | 3 -> 4 -> 5 -> ... -> null |
               |----------------------------|
```

### Java Code:
```java
public class Solution {
  public ListNode reverseInPairs(ListNode head) {
    if (head == null || head.next == null) return head;
    ListNode newHead = head.next;
    head.next = reverseInPairs(newHead.next);
    newHead.next = head;
    return newHead;
  }
}
```