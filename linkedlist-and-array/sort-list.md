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
对链表进行 Merge Sort 的精髓在于 **分** 的方法：getMid，然后断开mid之后的链。然后使用merge two sorted list 将两端merge。在合并的时候可以先新建dummy node指向head，然后从dummy开始，可以让code更concise。

**Quick Sort**
如果交换 node 本身会让问题变得复杂，所以精髓是只交换值而不交换 node。注判断递归出口的细节，要加上当`head == tail.next` 时，直接return。

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

#### Python Code:
Merge Sort：
```python
    class Solution:
        """
        @param head: The first node of the linked list.
        @return: You should return the head of the sorted linked list,
                      using constant space complexity.
        """
        def sortList(self, head):
            if not head or not head.next: return head
            mid = self.getMid(head)
            right_head = mid.next
            mid.next = None
            left = self.sortList(head)
            right = self.sortList(right_head)
            return self.merge(left, right)
            
        
        def getMid(self, head):
            slow = head
            fast = head.next
            while fast and fast.next:
                slow = slow.next
                fast = fast.next.next
            return slow
        
        
        def merge(self, left, right):
            dummy = ListNode(0)
            curr = dummy
            while left or right:
                if not left:
                    curr.next = right
                    break
                if not right:
                    curr.next = left
                    break
                if left.val <= right.val:
                    curr.next = left
                    curr = curr.next
                    left = left.next
                else:
                    curr.next = right
                    curr = curr.next
                    right = right.next
            return dummy.next    
```

Quick Sort:
```python
    # Quick Sort solution
    class Solution:
        # 仍然是交换值
        def sortList(self, head):
            def quickSort(head, tail):
                if not head or not tail or tail.next == head:
                    return head
                    
                # do partition first
                pivot = head.val
                dummy = ListNode(0)
                dummy.next = head
                left = dummy
                right = head
                leftTail = dummy
                while right != tail.next:
                    if right.val <= pivot:
                        leftTail = left
                        left = left.next
                        swap(left, right)
                    right = right.next
                swap(left, head)
                # 此时的left就是pivot所在位置, leftTail 为前半段的tail
                
                head1 = quickSort(head, leftTail)
                head2 = quickSort(left.next, tail)
                return head1
                
            
            def swap(node1, node2):
                t = node1.val
                node1.val = node2.val
                node2.val = t
                
                
            def getTail(head):
                if not head:
                    return None
                while head.next:
                    head = head.next
                return head
            
            # main part
            tail = getTail(head)
            return quickSort(head, tail)
```
---
_update Nov 26, 2017_

#### 重写此题
时隔数月，重写此题果然有了新的感悟。

1. 首先，对于 merge sort：
    > 对于mergesort来说，最重要的是实现先分开再merge。之前的写法是先用一个getMid函数找到中间node，再将其与后部断开，如此得到两段list。这次我把断开两段list的代码放在了 getMid 函数中（split函数），使得code更加简洁。  
      另外，这次的merge函数也比上次更简洁了。
2. 对于 quick sort：
    > 之前的版本感觉已经不错，还没想出真正交换node的解法，更新已经在上面quickSort的python code中了。


#### Java Code:
**Merge Sort**
```java
    public class Solution {
        /*
         * @param head: The head of linked list.
         * @return: You should return the head of the sorted linked list, using constant space complexity.
         */
         
        // merge sort solution
        public ListNode sortList(ListNode head) {
            if (head == null || head.next == null) return head;
            ListNode mid = split(head);
            ListNode l1 = sortList(head);
            ListNode l2 = sortList(mid);
            return merge(l1, l2);
        }
        
        private ListNode merge(ListNode head1, ListNode head2) {
            if (head1 == null) return head2;
            else if (head2 == null) return head1;
            
            ListNode dummy = new ListNode(0);
            ListNode curr = dummy;
            while (head1 != null && head2 != null) {
                if (head1.val < head2.val) {
                    curr.next = head1;
                    head1 = head1.next;
                } else {
                    curr.next = head2;
                    head2 = head2.next;
                }
                curr = curr.next;
            }
            if (head1 == null) curr.next = head2;
            else curr.next = head1;
            
            return dummy.next;
        }
        
        // 找到中间偏右的node作为mid，例如 1->2->null，should return 2, 同时断开 1->2
        // 精髓就是令fast的起始位置为 slow->next, 这样停止时的 slow 偏左，其右是应该
        // 返回的 mid，保存mid.next, 先断开当前mid，然后返回其右即可。
        private ListNode split(ListNode head) {
            if (head == null || head.next == null) return null;
            ListNode slow = head;
            ListNode fast = slow.next;
            while (fast != null && fast.next != null) {
                slow = slow.next;
                fast = fast.next.next;
            }
            ListNode ret = slow.next;
            slow.next = null;
            return ret;
        }
    }
```

#### Python Code
**Merge Sort**
```python
    class Solution:
        """
        @param: head: The head of linked list.
        @return: You should return the head of the sorted linked list, using constant space complexity.
        """
        def sortList(self, head):
            # ret: <ListNode>
            def split(head):
                if not head:
                    return None
                slow = head
                fast = head.next
                while fast and fast.next:
                    slow = slow.next
                    fast = fast.next.next
                ret = slow.next
                slow.next = None
                return ret
        
            # ret: <ListNode>
            def mergeSort(head):
                if not head or not head.next:
                    return head
                mid = split(head)
                head1 = mergeSort(head)
                head2 = mergeSort(mid)
                return merge(head1, head2)
            
            # ret: <ListNode>
            def merge(head1, head2):
                dummy = ListNode(0)
                curr = dummy
                while head1 and head2:
                    if head1.val < head2.val:
                        curr.next = head1
                        head1 = head1.next
                    else:
                        curr.next = head2
                        head2 = head2.next
                    curr = curr.next
                if not head1:
                    curr.next = head2
                else:
                    curr.next = head1
                return dummy.next
            
            # Main part:
            return mergeSort(head)
```







