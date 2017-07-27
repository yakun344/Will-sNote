## Sort List
_update Jul,26 2017 21:57_

---
[LintCode](http://www.lintcode.com/en/problem/sort-list/)

Sort a linked list in O(n log n) time using constant space complexity.

**Example**

    Given 1->3->2->null, sort it to 1->2->3->null.

**Challenge**
Solve it by merge sort & quick sort separately.

#### Basic Idea:
**Merge Sort:**
对链表进行 Merge Sort 的精髓在于 **分** 的方法：getMid，然后断开mid之后的链。然后使用merge two sorted list 将两端merge。

**Quick Sort**
如果交换 node 本身会让问题变得复杂，所以精髓是只交换值而不交换 node。

#### Java Code:
**Merge sort:**
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
    
     merge sort solution
     public class Solution {
         /**
          * @param head: The head of linked list.
          * @return: You should return the head of the sorted linked list,
                         using constant space complexity.
          */
         
         // merge sort
        public ListNode sortList(ListNode head) {  
            return mergeSort(head);
        }
        private ListNode mergeSort(ListNode start) {
            if (start == null) return null;
            if (start.next == null) return start;
            ListNode mid = getMid(start);
            ListNode next = mid.next;
            mid.next = null;
            ListNode left_start = mergeSort(start);
            ListNode right_start = mergeSort(next);
            return merge(left_start, right_start);
        }
        private ListNode getMid(ListNode start) {
            ListNode slow = start;
            ListNode fast = start.next;
            while (fast != null && fast.next != null) {
                slow = slow.next;
                fast = fast.next.next;
            }
            return slow;
        }
        private ListNode merge(ListNode left, ListNode right) {
            if (left == null) return right;
            if (right == null) return left;
            ListNode dummy = new ListNode(0);
            ListNode curr = dummy;
            while (left != null || right != null) {
                if (left == null) {
                    curr.next = right;
                    break;
                } 
                if (right == null) {
                    curr.next = left;
                    break;
                }
                if (left.val < right.val) {
                    curr.next = left;
                    curr = curr.next;
                    left = left.next;
                } else {
                    curr.next = right;
                    curr = curr.next;
                    right = right.next;
                }
            }
            return dummy.next;
        }
    }
```

**Quick Sort:**
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
    // quick sort solution
    public class Solution {
        /**
         * @param head: The head of linked list.
         * @return: You should return the head of the sorted linked list,
                        using constant space complexity.
         */
        
        public ListNode sortList(ListNode head) {
            ListNode tail = getTail(head);
            quickSort(head, tail);
            return head;
        }
        private void quickSort(ListNode head, ListNode tail) {
            if (head == null || tail == null || head == tail) return;
            if (head == tail.next) return;
    
        // 因为partition后需要不止一个返回值（pivot node, end），我们直接在主函数里实现
        // partition
            int pivot = head.val;
            ListNode left = head;
            ListNode left_tail = null;
            ListNode right = head.next;
            while (right != tail.next) {
                if (right.val <= pivot) {
                    left_tail = left;
                    left = left.next;
                    swapValue(left, right);
                }
                right = right.next;
            }
            swapValue(left, head);
           
        // recursion
            ListNode mid = left;
            quickSort(head, left_tail);
            quickSort(mid.next, tail);
        }
        private void swapValue(ListNode a, ListNode b) {
            if (a == b) return;
            int t = a.val;
            a.val = b.val;
            b.val = t;
        }
        private ListNode getTail(ListNode head) {
            if (head == null) return null;
            while (head.next != null) {
                head = head.next;
            }
            return head;
        }
    }
```