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
    class Solution(object):
        def sortedListToBST(self, head):
            """
            :type head: ListNode
            :rtype: TreeNode
            """
            def getMid(head):
                slow = head
                fast = head.next
                while fast and fast.next:
                    slow = slow.next
                    fast = fast.next.next
                return slow
            
            if not head:
                return None
            mid = getMid(head)
            root = TreeNode(mid.val)
            left_head = head
            right_head = mid.next
            
            # 断掉右半段开头相连部分，mid之后
            mid.next = None
            # 断掉mid之前
            temp = head
            while temp.next and temp.next != mid:
                temp = temp.next
            temp.next = None
            
            if head != mid:
                root.left = self.sortedListToBST(left_head)
            root.right = self.sortedListToBST(right_head)
            return root
```            
            
            