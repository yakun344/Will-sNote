## Remove Duplicates from Sorted List II
_update Sep 2, 2017  22:12_

---
[LeetCode](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/description/)

Given a sorted linked list, delete all nodes that have duplicate numbers, leaving only distinct numbers from the original list.

**For example,**

Given 1->2->3->3->4->4->5, return 1->2->5.
Given 1->1->1->2->3, return 2->3.

#### Basic Idea:
此题是之前一道题目的Follow up，这道题目不是要去重，而是把重复过的nodes都去掉，只剩下单独出现过的nodes；

这道题目的逻辑其实有些晦涩，必须抓住一个重点来展开，就是这道题目与之前题目的不同点：
-  之前题目是跳过之前重复的node，只剩最后一个；
-  这道题目是连最后一个也跳过；

从这点出发，我们就想到了一个**思路**：

我们通过判断 curr.val==curr.next.val 来确定当前node是否要去除，那么一个重复序列结束后，第一次出现不同时，curr 事实上指向了之前重复序列的最后一个元素，我们只要设置一个flag，来指示当前出现的不同是否是刚刚一段重复序列的终结即可；
-  如果 flag==True ，则去掉当前node，将 flag 设为 False;
-  如果 flag==False，说明当前已经不是一个重复序列的终结，无需去掉当前node；

(另外，这道题目中 dummy node 的应用也很重要)；

#### Python Code:
```python
    class Solution(object):
        def deleteDuplicates(self, head):
            """
            :type head: ListNode
            :rtype: ListNode
            """
            if not head:
                return None
            dup = False
            dummy = ListNode(0)
            dummy.next = head
            prev = dummy
            curr = head
            while curr and curr.next:
                if curr.val == curr.next.val:
                    prev.next = curr.next
                    curr = curr.next
                    dup = True
                elif curr.val != curr.next.val and dup:
                    prev.next = curr.next
                    curr = curr.next
                    dup = False
                else:
                    prev = curr
                    curr = curr.next
            if dup:
                prev.next = prev.next.next
            return dummy.next
```
#### Java Code:
```java
    class Solution {
        public ListNode deleteDuplicates(ListNode head) {
            ListNode dummy = new ListNode(0);
            dummy.next = head;
            ListNode prev = dummy;
            ListNode curr = head;
            boolean dup = false;
            while (curr != null && curr.next != null) {
                if (curr.val == curr.next.val) {
                    // 如果相等，则去掉curr，设dup为true
                    prev.next = curr.next;
                    curr = curr.next;
                    dup = true;
                } else if (curr.val != curr.next.val && dup) {
                    // dup为true，说明一段重复到这里结束，抛弃curr, 设dup为false
                    prev.next = curr.next;
                    curr = curr.next;
                    dup = false;
                } else {
                    curr = curr.next;
                    prev = prev.next;
                }
            }
            if (dup) prev.next = prev.next.next;
            return dummy.next;
        }
    }
```         

---
_update Nov 27, 2017 13:14_

### 更新，重新整理Code逻辑
#### Java Code 
之前的逻辑考虑的东西比较多，重新看的时候发现不好理解。现在的逻辑只需要注意curr指针的出口为 curr==null，然后结束之后记得把 prev.next 置为 null。
           
```java
    class Solution {
        public ListNode deleteDuplicates(ListNode head) {
            ListNode dummy = new ListNode(0);
            dummy.next = head;
            boolean dup = false;
            
            ListNode prev = dummy;
            ListNode curr = dummy.next;
            while (curr != null) {
                // 如果当前curr和next不同，或者curr为null，考虑是否dup
                if (curr.next == null || curr.val != curr.next.val) {
                    if (! dup) {
                        prev.next = curr;
                        prev = prev.next;
                    }
                    dup = false;
                } else {
                // 如果当前curr和next重复，设dup为true
                    dup = true;
                }
                curr = curr.next;
            }
            // 因为每次prev相当于是我们结果list的最后一个node，所以需要手动将prev最终的next设为null
            prev.next = null;
            
            return dummy.next;
        }
    }
```
            