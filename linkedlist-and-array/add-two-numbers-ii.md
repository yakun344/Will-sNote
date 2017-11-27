## Add Two Numbers II
_update Jul,30 2017 9:10_

---
[LeetCode](https://leetcode.com/problems/add-two-numbers-ii/description/)

You are given two non-empty linked lists representing two non-negative integers. The most significant digit comes first and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

**Follow up:**
What if you cannot modify the input lists? In other words, reversing the lists is not allowed.

**Example:**

    Input: (7 -> 2 -> 4 -> 3) + (5 -> 6 -> 4)
    Output: 7 -> 8 -> 0 -> 7
    
#### Basic Idea:
这道题的唯一难点是链表从高位连向低位，所以我们可以有三种思路：
1.  reverse the linked list；
2.  use stack to store numbers，so that we can reverse the sequence；
3.  把数字存在字符串中，和方法 2 类似；
**follow up** 所说的不可以reverse the list，所以我们使用第二个方法。
**具体地：**还有一个细节，就是在生成结果list的时候，只要一直在dummy.next添加新节点就可以保证正确顺序。

#### Java Code:
```java
    class Solution {
        public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
            // 把两个list的数字放入stack，这样pop的顺序就是低位到高位
            Deque<Integer> stack1 = new LinkedList<>();
            Deque<Integer> stack2 = new LinkedList<>();
            while (l1 != null) {
                stack1.addFirst(l1.val);
                l1 = l1.next;
            }
            while (l2 != null) {
                stack2.addFirst(l2.val);
                l2 = l2.next;
            }
            
            // 一次按位相加，存carrier，结果直接append到return list的左边
            ListNode dummy = new ListNode(0);
            int carrier = 0;
            while (! stack1.isEmpty() || ! stack2.isEmpty() || carrier != 0) {
                int adder1 = stack1.isEmpty() ? 0 : stack1.removeFirst();
                int adder2 = stack2.isEmpty() ? 0 : stack2.removeFirst();
                int res = adder1 + adder2 + carrier;
                int num = res % 10;
                carrier = res / 10;
                ListNode curr = new ListNode(num);
                curr.next = dummy.next;
                dummy.next = curr;
            }
            return dummy.next;
        }
    }
```

---
_update Nov 27, 2017 1:08_

#### Python Code
```python
    class Solution:
        def addTwoNumbers(self, l1, l2):
            """
            :type l1: ListNode
            :type l2: ListNode
            :rtype: ListNode
            """
            stack1 = []
            stack2 = []
            while l1:
                stack1.append(l1.val)
                l1 = l1.next
            while l2:
                stack2.append(l2.val)
                l2 = l2.next
            
            dummy = ListNode(0)
            carrier = 0
            while stack1 or stack2 or carrier:
                adder1 = 0 if not stack1 else stack1.pop()
                adder2 = 0 if not stack2 else stack2.pop()
                res = adder1 + adder2 + carrier
                carrier = res // 10
                num = res % 10
                curr = ListNode(num)
                curr.next = dummy.next
                dummy.next = curr
            
            return dummy.next
```



