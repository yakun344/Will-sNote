## Swap Nodes in Pairs
_update Jan 17,2018 1:15_

---
Given a linked list, swap every two adjacent nodes and return its head.

**For example,**

    Given 1->2->3->4, you should return the list as 2->1->4->3.

Your algorithm should use only constant space. You may not modify the values in the list, only nodes itself can be changed.
    
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
但是这种写法会消耗`O(n)`space，因为 call stack 的问题，执行顺序最先被reverse的其实是最右端的两个，然后逐层返回。

### Java Code:
```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode nextHead = head.next.next;
        ListNode newHead = head.next;
        newHead.next = head;
        head.next = swapPairs(nextHead);
        return newHead;
    }
}
```