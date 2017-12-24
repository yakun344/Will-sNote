## Convert Sorted List to Binary Search Tree
_update Aug 31, 2017  0:44_

---
[LeetCode](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/description/)

Given a singly linked list where elements are sorted in ascending order, convert it to a height balanced BST.

#### Basic Idea:
利用找中点的方式，每次在链表中找到mid作为当前root，递归进行。但是要注意细节，在找到mid之后，必须进一步断开 mid 两边相连的部分。

#### Java Code:
将slow和fast初始化为head，则退出的时候mid为偏右mid；
```java
    class Solution {
        public TreeNode sortedListToBST(ListNode head) {
            if (head == null) return null;
            if (head.next == null) return new TreeNode(head.val);
            ListNode mid = getMid(head);
            TreeNode root = new TreeNode(mid.val);
            ListNode rightHead = mid.next;
            mid.next = null; // 断开root和右边
            root.left = sortedListToBST(head);
            root.right = sortedListToBST(rightHead);
            return root;
        }
        
        private ListNode getMid(ListNode head) {
            if (head == null) return null;
            ListNode slow = head, fast = head;
            ListNode prev = null;
            while (fast != null && fast.next != null) {
                prev = slow;
                slow = slow.next;
                fast = fast.next.next;
            }
            prev.next = null; // 断开mid之前，mid 偏右
            return slow;
        }
    }
```

#### Python Code:
```python
    class Solution:
        def sortedListToBST(self, head):
            """
            :type head: ListNode
            :rtype: TreeNode
            """
            def getMid(head):
                if not head or not head.next: return None
                fast, slow, prev = head, head, None
                while fast and fast.next:
                    prev = slow
                    slow = slow.next
                    fast = fast.next.next
                prev.next = None
                return slow
            
            
            if not head: return None
            if not head.next: return TreeNode(head.val)
            mid = getMid(head)
            rightHead = mid.next
            mid.next = None
            root = TreeNode(mid.val)
            root.left = self.sortedListToBST(head)
            root.right = self.sortedListToBST(rightHead)
            return root
```            
            
            