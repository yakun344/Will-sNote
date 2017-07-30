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
3.  把数字存在字符串中；
**follow up** 所说的不可以reverse the list，所以我们使用第二个方法。
**具体地：**还有一个细节，就是在生成结果list的时候，只要一直在dummy.next添加新节点就可以保证正确顺序。

#### Java Code:
    ```java
    /**
     * Definition for singly-linked list.
     * public class ListNode {
     *     int val;
     *     ListNode next;
     *     ListNode(int x) { val = x; }
     * }
     */
    public class Solution {
        public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
            Deque<Integer> stack1 = new LinkedList<>();
            Deque<Integer> stack2 = new LinkedList<>();
            
            while (l1 != null || l2 != null) {
                if (l1 != null) {
                    stack1.push(l1.val);
                    l1 = l1.next;
                }
                if (l2 != null) {
                    stack2.push(l2.val);
                    l2 =  l2.next;
                }
            }
            int sum = 0;
            int carrier = 0;
            ListNode dummy = new ListNode(0);
            while (! stack1.isEmpty() || ! stack2.isEmpty() || carrier != 0) {
                if (! stack1.isEmpty()) sum += stack1.pop();
                if (! stack2.isEmpty()) sum += stack2.pop();
                sum += carrier;
                carrier = sum / 10;
                ListNode this_dig = new ListNode(sum % 10);
                this_dig.next = dummy.next;
                dummy.next = this_dig;
                sum = 0;
            }
            return dummy.next;
        }
    }
```