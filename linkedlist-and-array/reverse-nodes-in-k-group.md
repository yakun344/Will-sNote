## Reverse Nodes in k-Group
_update Jul 29, 2017 14:56_

---
[LintCode](http://www.lintcode.com/en/problem/reverse-nodes-in-k-group/)

Given a linked list, reverse the nodes of a linked list k at a time and return its modified list.

If the number of nodes is not a multiple of k then left-out nodes in the end should remain as it is.

You may not alter the values in the nodes, only nodes itself may be changed.

Only constant memory is allowed.

**Example**
    
    Given this linked list: 1->2->3->4->5
    
    For k = 2, you should return: 2->1->4->3->5
    
    For k = 3, you should return: 3->2->1->4->5
    
#### Basic Idea:
这道题的难度应该远够不上 hard。

用到两个技术点，一是reverse linked list，二是reverse linked list中间的一段。后者是前者的扩展。

**具体地**，解法分成两部分：

*  ListNode getKth(ListNode head, int k);

这个函数对每个输入的head节点，找到从它开始第k个节点，若不存在，则返回null。

*  ListNode reverse(ListNode pre_head, ListNode tail);

这个函数 reverse 从pre_head.next 到 tail (include) 间的节点。之所以要传入要reverse的第一个节点之前的节点是为了整个list在reverse之后依然是连接的。

#### Python Code:
``` python
    # Definition for singly-linked list.
    # class ListNode:
    #     def __init__(self, x):
    #         self.val = x
    #         self.next = None
    
    class Solution:
        # @param head, a ListNode
        # @param k, an integer
        # @return a ListNode
        def reverseKGroup(self, head, k):
            dummy = ListNode(0)
            dummy.next = head
            kth = self.getKth(dummy.next, k)
            right_most = dummy
            while kth:
                right_most = self.reverse(right_most, kth)
                kth = self.getKth(right_most.next, k)
            return dummy.next
            
            
        def reverse(self, pre_head, tail):
            # reverse 从 pre_head.next 到 tail 的部分，并和 tail.next 链接
            
                # store left and right most nodes first
            left = pre_head.next
            right = tail
            right_next = tail.next
            
                # reverse nodes between them
            prev = None
            curr = pre_head.next
            next = None
            while curr != right_next and curr is not None:
                next = curr.next
                curr.next = prev
                prev = curr
                curr = next
            
                # concatenate middle part with right and left part
            pre_head.next = right
            left.next = right_next
                
                # return the tail of middle part
            return left
            
            
        def getKth(self, head, k):
            if not head: return None
            count = 1
            while count < k:
                head = head.next
                count += 1
                if not head:
                    return None
            return head
        
```

#### Java Code:
```java
    /**
     * Definition for singly-linked list.
     * public class ListNode {
     *     int val;
     *     ListNode next;
     *     ListNode(int x) { val = x; }
     * }
     */
    public class Solution {
        /**
         * @param head a ListNode
         * @param k an integer
         * @return a ListNode
         */
        public ListNode reverseKGroup(ListNode head, int k) {
            if (head == null || head.next == null || k == 1) return head;
            ListNode dummy = new ListNode(0);
            dummy.next = head;
            ListNode pre_head = dummy;
            ListNode kth = getKth(head, k);
            while (kth != null) {
                pre_head = reverse(pre_head, kth);
                kth = getKth(pre_head.next, k);
            }
            return dummy.next;
        }
        private ListNode getKth(ListNode head, int k) {
            if (head == null) return null;
            int count = 1;
            while (count < k) {
                head = head.next;
                if (head == null) return null;
                count++;
            }
            return head;
        }
        private ListNode reverse(ListNode pre_head, ListNode tail) {
            // store left and right most node first
            ListNode left = pre_head.next;
            ListNode right_next = tail.next;
            
            // reverse nodes between them
            ListNode prev = null;
            ListNode curr = left;
            ListNode next = null;
            while (curr != right_next) {
                next = curr.next;
                curr.next = prev;
                prev = curr;
                curr = next;
            }
            
            // concatenate left and right part
            pre_head.next = tail;
            left.next = right_next;
            
            // return the last node of middle part
            return left;
        }
    }
```