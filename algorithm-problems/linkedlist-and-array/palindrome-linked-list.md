# Palindrome Linked List

_update Jun 29, 2017_

[leetcode](https://leetcode.com/problems/palindrome-linked-list/#/description) Given a singly linked list, determine if it is a palindrome.

**Follow up**: Could you do it in O\(n\) time and O\(1\) space?

### 思路：

要检查链表是否是回文链表有很多方法，最简单的就是traverse一遍，把链表的val存入list，然后检查是否回文。但是题目要求用O\(1\)的空间，所以我们只能采取特殊方法，即先用快慢指针找到中点和尾节点，然后reverse后半部分，再比较。

### Java code：

```java
    public class Solution {
        public boolean isPalindrome(ListNode head) {
            if (head == null) return true;
            if (head.next == null) return true;
            if (head.next.next == null) return head.val == head.next.val;
            ListNode slow = head;
            ListNode fast = head;
            int halfLength = 0;
            while (fast.next != null && fast.next.next != null) {
                slow = slow.next;
                fast = fast.next.next;
                halfLength++;
            }
            // 检查node个数奇偶，如果恰好fast在最右边，则是奇数，此时slow在正中间，则
            // halfLength --，否则的话需要手动把fast右移一位。
            if (fast.next == null) {
                halfLength--;
            } else {
                fast = fast.next;
            }
            ListNode right = reverse(slow.next); // reverse之后从两头比较。
            for (int i = 0; i < halfLength + 1; ++i) {
                if (right.val != head.val) return false;
                right = right.next;
                head = head.next;
            }
            return true;
        }
        private ListNode reverse(ListNode node) {
            if (node == null || node.next == null) return node;
            ListNode prev = null;
            ListNode curr = node;
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

_update Nov 29, 2017_

### 更新

1. 代码更加模块化，将findMid和split合并起来，写成一个split函数，在这个函数中，直接完成了将list从中间一分为二，并且对奇偶的不同情况进行处理。如果是奇数个，返回时候已经删去了正中间的node。
2. 这次的 fast 起点为 slow.next，这样可以使得循环退出的时候 slow 指向中点之前，避免需要跟踪 slow 之前一个 node 的指针。

**Java Code update：**

```java
    class Solution {
        public boolean isPalindrome(ListNode head) {
            if (head == null || head.next == null) return true;
            ListNode mid = reverse(split(head));
            while (head != null || mid != null) {
                if (head == null || mid == null) return false;
                if (head.val != mid.val) return false;
                head = head.next;
                mid = mid.next;
            }
            return true;
        }

        private ListNode reverse(ListNode head) {
            if (head == null || head.next == null) return head;
            ListNode node = reverse(head.next);
            head.next.next = head;
            head.next = null;
            return node;
        }

        // 将list分成两组，中间断开，返回值为后半段起点。
        // 利用循环结束时 fast的位置来判断list中node个数的奇偶，如果fast在最右边则list中有偶数个node，
        // 否则的话，则需要手动删去中心的node。
        // 因为起始位置 fast 为 slow.next，这样循环结束的时候 slow 指向中点之前，更方便操作。
        private ListNode split(ListNode head) {
            ListNode preSlow = null;
            ListNode slow = head;
            ListNode fast = head.next;
            while (fast != null && fast.next != null && fast.next.next != null) {
                preSlow = slow;
                slow = slow.next;
                fast = fast.next.next;
            }
            if (fast.next != null) { // odd number, 删掉slow右边的真中点
                slow.next = slow.next.next;
            }
            ListNode ret = slow.next;
            slow.next = null;

            return ret;
        }
    }
```

**O\(n\) space, easy solution:**

```python
    class Solution:
        def isPalindrome(self, head):
            """
            :type head: ListNode
            :rtype: bool
            """
            if not head or not head.next:
                return True
            nums = []
            while head:
                nums.append(head.val)
                head = head.next

            mid = len(nums) // 2
            left, right = -1, -1
            if len(nums) % 2 == 0:
                left = mid - 1
                right = mid
            else:
                left = mid - 1
                right = mid + 1

            while left >= 0 or right < len(nums):
                if left < 0 or right == len(nums):
                    return False
                if nums[left] != nums[right]:
                    return False
                left -= 1
                right += 1
            return True
```

_update Sep 16 2018, 14:40_

## Update: 更简洁的写法

时隔大半年，写这道题目的code更加简洁而且一次bug free。需要注意的点如下：

1. fast的起点为head.next, 这样最终slow会指向中间偏左的位置;
2. reverse之后比较两个list是否相等的时候不需要考虑其长度不同的问题，因为可能多出来的node并不会影响，所以只有当对应node的val不同时返回false即可。

### Java Code:

```java
class Solution {
   public boolean isPalindrome(ListNode head) {
       if (head == null || head.next == null) return true;
       ListNode slow = head, fast = head.next;
       while (fast.next != null && fast.next.next != null) {
           slow = slow.next;
           fast = fast.next.next;
       }
       // slow 指向中间偏左
       ListNode mid = slow.next;
       slow.next = null;
       ListNode head2 = reverse(mid);
       while (head != null && head2 != null) {
           if (head.val != head2.val) return false;
           head = head.next;
           head2 = head2.next;
       }
       return true;
   }

   private ListNode reverse(ListNode head) {
       ListNode prev = null, curr = head, next = null;
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

