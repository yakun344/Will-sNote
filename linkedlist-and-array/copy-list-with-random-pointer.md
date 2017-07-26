## Copy List with Random Pointer
_update Jul,26 2017 15:14_

---
[LintCode](http://www.lintcode.com/en/problem/copy-list-with-random-pointer/)

A linked list is given such that each node contains an additional random pointer which could point to any node in the list or null.

Return a deep copy of the list.

**Challenge **
Could you solve it with O(1) space?

#### Basic Idea:
这道题就是说这个链表除了next指针还有一个指针叫做random，可以指向其他任何node，求这个链表的deep copy。

**两种思路：**
1.  利用图的deep copy的做法，先用一个hashmap复制所有的node，再利用map中的新旧node一一对应关系复制random指针（就相当于图里面的edge），很简单，O(n)空间。
2.  利用链表next一路向后的性质，我们可以构造一个新旧node交替的大链表（old1->new1->old2->new2->old3->new3...），构造成功之后再traverse一遍搞定newnode们的random指针，然后把他们分开即可。

#### Java Code:
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