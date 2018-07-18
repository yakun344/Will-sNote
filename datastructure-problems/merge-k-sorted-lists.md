## Merge k Sorted Lists
_update Aug 22,2017  15:56_

---
[LintCode](http://www.lintcode.com/en/problem/merge-k-sorted-lists/)
[LeetCode](https://leetcode.com/problems/merge-k-sorted-lists/description/)

Merge k sorted linked lists and return it as one sorted list.

Analyze and describe its complexity.

**Example**

      Given lists:

      [
         2->4->null,
         null,
         -1->null
      ],
      return -1->2->4->null.

#### Basic Idea:
**思路1：heap**
利用 min heap，先把每个 list 的 head 加入 heap，每次拿出当前 heap 的 peek 作为当前最小值，放入 res，再把该最小node 的 next 加入heap。这样一来，就可以保证当前 heap 的顶一定为剩余所有node中最小的。

时间复杂度为O(nklogk), 因为我们每个node都访问了一遍（nk），对于每个node，需要logk的heap操作，所以是 nklogk。空间复杂度为 O(k)，即 priority queue 的 size；

**思路2：merge sort 分治法**
类似于merge sort 的思路，我们先把 k 组 head 分为 2 组，再继续划分，直到每组尺度不大于2，开始merge。

时间复杂度同样为 `O(nklogk)`, 因为 `T(k) = 2T(k/2) + O(nk)`；即每次merge耗时 `O(nk)`, 共有 `log(k)` 层，所以总时间为 `O(nklogk)`.

#### Java Code：
    ```java
    /**
     * Definition for ListNode.
     * public class ListNode {
     *     int val;
     *     ListNode next;
     *     ListNode(int val) {
     *         this.val = val;
     *         this.next = null;
     *     }
     * }
     */
    public class Solution {
        /**
         * @param lists: a list of ListNode
         * @return: The head of one sorted list.
         */
        public ListNode mergeKLists(List<ListNode> lists) {  
            // using a min heap
            if (lists == null || lists.size() == 0) return null;
            PriorityQueue<ListNode> pq = new PriorityQueue<>(lists.size(), new Comparator<ListNode>(){
                @Override
                public int compare(ListNode node1, ListNode node2) {
                    return node1.val - node2.val;
                }
            });
            for (ListNode node : lists) {
                if (node != null) {
                    pq.offer(node);
                }
            }
            ListNode dummy = new ListNode(0);
            ListNode curr = dummy;
            while (! pq.isEmpty()) {
                ListNode currMin = pq.poll();
                if (currMin.next != null) {
                    pq.offer(currMin.next);
                }
                curr.next = new ListNode(currMin.val);
                curr = curr.next;
            }
            return dummy.next;
        }
    }
```

#### Python Code:
This code got AC in LeetCode;
    ```python
    # Definition for singly-linked list.
    # class ListNode(object):
    #     def __init__(self, x):
    #         self.val = x
    #         self.next = None

    class Solution(object):
        def mergeKLists(self, lists):
            """
            :type lists: List[ListNode]
            :rtype: ListNode
            """
            dummy = ListNode(0)
            curr = dummy
            # 维持min heap，就可以跟踪剩余node中的最小值
            pq = []
            for head in lists:
                if head:
                    heapq.heappush(pq, (head.val, head))

            while pq:
                node = heapq.heappop(pq)[1]
                if node.next:
                    heapq.heappush(pq, (node.next.val, node.next))
                curr.next = ListNode(node.val)
                curr = curr.next
            return dummy.next
```

---
_update 2018-07-17 20:47:59_

#### Update: Java 分治法 solution
```java
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists.length == 0) return null;
        return mergeSort(lists, 0, lists.length - 1);
    }

    private ListNode mergeSort(ListNode[] lists, int start, int end) {
        if (start == end) return lists[start];
        int mid = (start + end) / 2;
        ListNode head1 = mergeSort(lists, start, mid);
        ListNode head2 = mergeSort(lists, mid + 1, end);
        return merge(head1, head2);
    }

    private ListNode merge(ListNode head1, ListNode head2) {
        ListNode dummy = new ListNode(0);
        ListNode curr = dummy;
        while (head1 != null || head2 != null) {
            if (head1 == null) {
                curr.next = head2;
                return dummy.next;
            } else if (head2 == null) {
                curr.next = head1;
                return dummy.next;
            } else if (head1.val < head2.val) {
                curr.next = head1;
                head1 = head1.next;
                curr.next.next = null;
                curr = curr.next;
            } else {
                curr.next = head2;
                head2 = head2.next;
                curr.next.next = null;
                curr = curr.next;
            }
        }
        return dummy.next;
    }
}
```
