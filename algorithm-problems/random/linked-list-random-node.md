# Linked List Random Node

_udpate Jul,29 2017 17:02_

[LeetCode](https://leetcode.com/problems/linked-list-random-node/discuss/)

Given a singly linked list, return a random node's value from the linked list. Each node must have the same probability of being chosen.

**Follow up:** What if the linked list is extremely large and its length is unknown to you? Could you solve this efficiently without using extra space?

**Example:**

```java
    // Init a singly linked list [1,2,3].
    ListNode head = new ListNode(1);
    head.next = new ListNode(2);
    head.next.next = new ListNode(3);
    Solution solution = new Solution(head);

    // getRandom() should return either 1, 2, or 3 randomly.
    // Each element should have equal probability of returning.
    solution.getRandom();
```

## Basic Idea：

这道题目涉及到了新的内容，和随机概率有关，这里需要说一下 **Reservoir Sampling** 的 strategy。 [**这里是leetcode discussion 中对于这个内容的说明**](https://leetcode.com/problems/linked-list-random-node/discuss/)

```text
Problem: Select K elements from n.

Choose 1, 2, 3, ..., k first and put them into the reservoir.
For k+1, pick it with a probability of k/(k+1), and randomly replace a number in the reservoir.
For k+i, pick it with a probability of k/(k+i), and randomly replace a number in the reservoir.
Repeat until k+i reaches n

解释：当我们要随机选择 k 个元素的时候，例如当前所visit的元素是第n个，我们需要保证它被选择的概率是 `k / n`，
  这也就是之前公式中 `k / (k + i)` 的来源。至于正确性，可以递推找规律，归纳证明。
```

具体地，在这里，k = 1. 所以每次我们只需要以 `1/(i)` 的概率选择是否将当前element替换已选择的元素即可。如果要在n个元素中选k个，则对于前k个元素直接加入res，之后对于第i个元素，以 `k / i` 的概率将其加入res，并随机选择一个res中的元素弹出即可。

* 另：当然也可以遍历一遍，存好所有元素，然后用随机数生成index选择，只是说如果不可以使用extra space 这种方法就不可以了。

## Java Code:

```java
    /**
     * Definition for singly-linked list.
     * public class ListNode {
     *     int val;
     *     ListNode next;
     *     ListNode(int x) { val = x; }
     * }
     */

    // O(1) space and O(n) time solution, without buffer
    public class Solution {

        /** @param head The linked list's head.
            Note that the head is guaranteed to be not null, so it contains at least one node. */

        public ListNode HEAD = null;

        public Solution(ListNode head) {
            HEAD = head;
        }

        /** Returns a random node's value. */
        public int getRandom() {
            ListNode curr = HEAD;
            ListNode res = null;
            int count = 0;
            while (curr != null) {
                count++;
                if ((int)(Math.random() * count) == 0) {
                    res = curr;
                }
                curr = curr.next;
            }
            return res.val;
        }
    }

    /**
     * Your Solution object will be instantiated and called as such:
     * Solution obj = new Solution(head);
     * int param_1 = obj.getRandom();
     */
```

## Python Code:

Using buffer, O\(n\) time for create buffer, O\(1\) for query. O\(n\) space.

```python
    # Definition for singly-linked list.
    # class ListNode(object):
    #     def __init__(self, x):
    #         self.val = x
    #         self.next = None

    class Solution(object):

        def __init__(self, head):
            """
            @param head The linked list's head.
            Note that the head is guaranteed to be not null, so it contains at least one node.
            :type head: ListNode
            """
            self.buff = []
            while head:
                self.buff.append(head.val)
                head = head.next
            self.LENGTH = len(self.buff)

        def getRandom(self):
            """
            Returns a random node's value.
            :rtype: int
            """
            return self.buff[random.randint(0, self.LENGTH - 1)]



    # Your Solution object will be instantiated and called as such:
    # obj = Solution(head)
    # param_1 = obj.getRandom()
```

