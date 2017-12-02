## Insertion Sort List
_update Sep 10, 2017  14:24_

---
[LeetCode](https://leetcode.com/problems/insertion-sort-list/description/)

Sort a linked list using insertion sort.

#### Basic Idea:
传统的 Insertion Sort 是对每个位置 j，从 `j-1` 到 0 进行扫描，但是题中所给的是 Singly Linked List, 无法向前遍历，所以我们采取对每个元素，从head向后找插入位置的办法。

还是维持一个指针 curr 持续向后，同时维持它前面一个node prev为了删除curr方便。然后保持curr左边都是已经排序的。每次从左到右找要插入curr的位置，把curr从原位置删除，插入新位置即可。

#### Java Code:
```java
    class Solution {
        public ListNode insertionSortList(ListNode head) {
            if (head == null) return null;
            ListNode dummy = new ListNode(Integer.MIN_VALUE);
            dummy.next = head;
            ListNode prev = null, curr = dummy;
            while (curr != null && curr.next != null) {
                prev = curr;
                curr = curr.next;
                // 如果curr需要插入，则进入循环
                if (prev.val > curr.val) {
                    ListNode prob = dummy;
                    // 从左到右找curr的插入位置
                    while (prob != curr && prob.val < curr.val && prob.next.val < curr.val) {
                        prob = prob.next;
                    }
                    // 把curr插入到prob.next
                    prev.next = curr.next;
                    curr.next = prob.next;
                    prob.next = curr;
                }
            }
            return dummy.next;
        }
    }
```
--- 
_update Dec 1, 2017  19:21_

#### Update
更新一个时隔多月后写的Code，和当时的思路有些不同，整个考虑上，逻辑判断比之前的要简单：

Java
```java
    class Solution {
        public ListNode insertionSortList(ListNode head) {
            ListNode dummy = new ListNode(Integer.MIN_VALUE);
            dummy.next = head;
            ListNode i = dummy; // i 为左边已经排序部分的右边界
            ListNode j = dummy.next; // j 为向右探测的指针
            ListNode prevJ = dummy; // j 之前的node，记录它方便移除 j
            while (j != null) {
                if (j.val < i.val) { // 需要插入
                    ListNode k = dummy; // 从左端点开始扫描，找合适插入位置的指针
                    while (k.next.val < j.val) k = k.next;
                    // 找到位置之后，把j插入到k之后
                    prevJ.next = j.next;
                    j.next = k.next;
                    k.next = j;
                    j = prevJ.next;
                } else {
                    prevJ = j;
                    j = j.next;
                    i = i.next;
                }
            }
            return dummy.next;
        }
    }
```










