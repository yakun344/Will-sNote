# Copy List with Random Pointer

_update Jul,26 2017 15:14_

[LintCode](http://www.lintcode.com/en/problem/copy-list-with-random-pointer/)

A linked list is given such that each node contains an additional random pointer which could point to any node in the list or null.

Return a deep copy of the list.

**Challenge**  Could you solve it with O\(1\) space?

## Basic Idea:

这道题就是说这个链表除了next指针还有一个指针叫做random，可以指向其他任何node，求这个链表的deep copy。

**两种思路：** 1. 利用图的deep copy的做法，先用一个hashmap复制所有的node，再利用map中的新旧node一一对应关系复制random指针（就相当于图里面的edge），很简单，O\(n\)空间。 2. 利用链表next一路向后的性质，我们可以构造一个新旧node交替的大链表（old1-&gt;new1-&gt;old2-&gt;new2-&gt;old3-&gt;new3...），构造成功之后再traverse一遍搞定newnode们的random指针，然后把他们分开即可。

## Java Code:

方法1：

```java
    /**
     * Definition for singly-linked list with a random pointer.
     * class RandomListNode {
     *     int label;
     *     RandomListNode next, random;
     *     RandomListNode(int x) { this.label = x; }
     * };
     */
    public class Solution {
        /**
         * @param head: The head of linked list with a random pointer.
         * @return: A new head of a deep copy of the list.
         */

        // HashMap solution
        public RandomListNode copyRandomList(RandomListNode head) {
            if (head == null) return null;
            Map<RandomListNode, RandomListNode> map = new HashMap<>();
            RandomListNode temp = head;
            while (temp != null) {
                map.put(temp, new RandomListNode(temp.label));
                temp = temp.next;
            }
            for (RandomListNode node : map.keySet()) {
                RandomListNode curr = map.get(node);
                curr.next = map.get(node.next);
                curr.random = map.get(node.random);
            }
            return map.get(head);
        }
    }
```

方法2：

```java
    /**
     * Definition for singly-linked list with a random pointer.
     * class RandomListNode {
     *     int label;
     *     RandomListNode next, random;
     *     RandomListNode(int x) { this.label = x; }
     * };
     */
    public class Solution {
        /**
         * @param head: The head of linked list with a random pointer.
         * @return: A new head of a deep copy of the list.
         */
        // no extra space solution, (old->new->old->new->... solution)
        public RandomListNode copyRandomList(RandomListNode head) {
            if (head == null) return null;
            RandomListNode old_curr = head;
            RandomListNode new_curr = null;
            // 第一遍遍历在每个oldnode后面新建newnode，第二遍给所有newnode写入random
            // 然后把所有newnode拆出来组成新list
            while (old_curr != null) {
                new_curr = new RandomListNode(old_curr.label);
                new_curr.next = old_curr.next;
                old_curr.next = new_curr;
                old_curr = new_curr.next;
            }
            old_curr = head;
            new_curr = head.next;
            while (new_curr != null) {
                if (old_curr.random != null) {
                    new_curr.random = old_curr.random.next;
                }
                if (new_curr.next == null) break;
                new_curr = new_curr.next.next;
                old_curr = old_curr.next.next;
            }
            return split(head);
        }
        private RandomListNode split(RandomListNode head) {
            RandomListNode dummy = head;
            RandomListNode curr = head.next;
            while (curr.next != null) {
                curr.next = curr.next.next;
                curr = curr.next;
            }
            return dummy.next;
        }
    }
```

_update Nov 26, 2017 0:14_ 对于 O\(1\) space 的解法，有一点需要更正。以上解法中的 split 函数没有恢复原链表，实际使用中肯定是需要恢复的，所以在新的解法中更正了这一部分。

```java
    public class Solution {
        /**
         * @param head: The head of linked list with a random pointer.
         * @return: A new head of a deep copy of the list.
         */

        // no extra space solution, (old->new->old->new->... solution)
        public RandomListNode copyRandomList(RandomListNode head) {
            if (head == null) return null;

            RandomListNode oldCurr = head;
            RandomListNode newCurr = null;

            // 第一步，复制每个node插入其后
            while (oldCurr != null) {
                newCurr = new RandomListNode(oldCurr.label);
                newCurr.next = oldCurr.next;
                oldCurr.next = newCurr;
                oldCurr = oldCurr.next.next;
                newCurr = newCurr.next;
            }

            // 第二步，复制random pointer
            oldCurr = head;
            newCurr = head.next;
            while (oldCurr != null) {
                if (oldCurr.random != null) {
                    newCurr.random = oldCurr.random.next;
                }
                oldCurr = oldCurr.next.next;
                if (oldCurr == null) break;
                newCurr = newCurr.next.next;
            }

            // 第三部，分离新旧 list, 特别注意要复原原本的list
            return splitList(head);
        }

        private RandomListNode splitList(RandomListNode head) {
            RandomListNode oldCurr = head;
            RandomListNode newCurr = head.next;
            RandomListNode dummy = new RandomListNode(0);
            dummy.next = newCurr;
            while (oldCurr != null) {
                oldCurr.next = oldCurr.next.next;
                oldCurr = oldCurr.next;
                if (oldCurr == null) break;
                newCurr.next = newCurr.next.next;
                newCurr = newCurr.next;
            }
            return dummy.next;
        }
    }
```

