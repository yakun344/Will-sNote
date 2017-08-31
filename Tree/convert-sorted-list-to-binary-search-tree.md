## Convert Sorted List to Binary Search Tree
_update Aug 31, 2017  0:44_

---
[LeetCode](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/description/)

Given a singly linked list where elements are sorted in ascending order, convert it to a height balanced BST.

#### Basic Idea:
利用找中点的方式，每次在链表中找到mid作为当前root，递归进行。但是要注意细节，在找到mid之后，必须进一步断开 mid 两边相连的部分。

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
            
            