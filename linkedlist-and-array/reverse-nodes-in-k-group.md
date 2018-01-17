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
class Solution:
    """
    @param: head: a ListNode
    @param: k: An integer
    @return: a ListNode
    """
    def reverseKGroup(self, head, k):
        def getKth(head):
            for i in range(k - 1):
                if not head:
                    return None
                head = head.next
            return head


        def reverse(preHead, tail):
            if preHead.next == tail:
                return
            head = preHead.next
            tailNext = tail.next # 这里很重要，要提前保存好 tail.next, 否则的话tail会丢失next
            # reverse head 和 tail 之间的部分
            prev = head
            curr = head.next
            next = None
            while curr != tailNext:
                next = curr.next
                curr.next = prev
                prev = curr
                curr = next
            # 重新连接左右部分和中间部分
            head.next = tailNext
            preHead.next = tail
            
            
        # main part
        dummy = ListNode(0)
        dummy.next = head
        preHead = dummy
        while True:
            kth = getKth(preHead.next)
            nextPreHead = preHead.next
            if not kth:
                break
            reverse(preHead, kth)
            preHead = nextPreHead
        return dummy.next
```

#### Java Code:
这种写法和之前的python写法不同。这里每次先将当前段的 k 个nodes与之后部分断开，然后调用reverse，再将其和前后重新连接，逻辑更加清晰；
```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode start = head;
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode prevStart = dummy;

        while (true) {
            ListNode kth = getKth(start, k);
            if (kth == null) break;
            // 记录下一段的head，断开kth.next
            ListNode nextStart = kth.next;
            kth.next = null;
            // 链接上一段和这一段reverse之后的新head，新head是kth
            prevStart.next = kth;
            reverse(start);
            // reverse 之后，kth为新head，之前的start为tail
            prevStart = start;
            start.next = nextStart;
            start = nextStart;
        }
        return dummy.next;
    }
    
    // get the k-th node, include head
    private ListNode getKth(ListNode head, int k) {
        int count = 1;
        ListNode curr = head;
        while (count < k && curr != null) {
            curr = curr.next;
            count++;
        }
        return curr;
    }
    
    private ListNode reverse(ListNode head) {
        ListNode prev = null;
        ListNode curr = head;
        ListNode next = null;
        while (curr != null) {
            next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
}
```

<br>

---
_udpate Jan 17,2018  11:54_

### Update (Recursion Solution)
严格上讲，这种 recursion 的解法是不符合 constant space 的要求的，但是这种写法更为简单，所以也提供一下。基本思路就是每次reverse完一组 k 个 nodes 之后，将其后的部分视作一个子问题，然后递归求解。
<br>
#### Java Code:
```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode kth = getKth(head, k);
        if (kth == null) return head;
        ListNode nextHead = kth.next;
        kth.next = null;
        reverse(head); 
        // kth is the new head, head is now the tail
        head.next = reverseKGroup(nextHead, k);
        return kth;
    }
    
    // get the k-th node, include head
    private ListNode getKth(ListNode head, int k) {
        int count = 1;
        ListNode curr = head;
        while (count < k && curr != null) {
            curr = curr.next;
            count++;
        }
        return curr;
    }
    
    private ListNode reverse(ListNode head) {
        ListNode prev = null;
        ListNode curr = head;
        ListNode next = null;
        while (curr != null) {
            next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
}
```








