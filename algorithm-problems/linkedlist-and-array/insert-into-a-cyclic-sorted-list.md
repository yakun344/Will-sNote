# Insert into a Cyclic Sorted List

_update Jul 24, 2017 15:40_

[LintCode](http://www.lintcode.com/en/problem/insert-into-a-cyclic-sorted-list/)

Given a node from a cyclic linked list which has been sorted, write a function to insert a value into the list such that it remains a cyclic sorted list. The given node can be any single node in the list. Return the inserted new node.

**Notice**

* 3-&gt;5-&gt;1 is a cyclic list, so 3 is next node of 1.
* 3-&gt;5-&gt;1 is same with 5-&gt;1-&gt;3

**Example**

```text
Given a list, and insert a value 4:
3->5->1
Return 5->1->3->4
```

## Basic Idea:

1. 考虑当输入是null时的情况
2. 当可以找到 `curr <= target <= next` 时的情况
3. target 比所有node值都大或者都小的情况

_update 2017-11-21 01:59:08_

这道题其实没有想象中的那么简单，而奇怪的是几个月前第一次刷这道题的时候还用一个错误解骗过了LintCode 的 OJ，导致一直没有发现问题。

## 问题在于：

首先我们考虑共有哪些情况 ：

1. node is None；
2. 我们找到了明确可以插入的位置：`curr.val <= x <= curr.next.val`;
3. x 比当前 linked list 的所有元素都大，或者都小；

看到这里感觉还是没有问题的，和之前的分析也一样，但是关键就在于如何判断 x 比所有当前元素都大或者都小，此时需要分两种情况讨论：

1. 给定 list 的元素不全都相同，例如 3,1,5；

   > 这种情况我们可以通过判断 `curr.val > curr.next.val` 来确定是否到达排序链表的 tail，然后分别判断 x 是否比 curr 和 curr.next 都大或者都小；

2. 给定 list 所有元素都相同，这种情况下就很容易出现死循环；

   > 这种情况下，我们就需要记录一开始给定的 node，当出现 `curr is node` 的时候，我们就知道我们一定已经循环了一整圈，并且不符合以上其他情况，那么一定是因为给定list所有元素都相同，无法判断出tail导致，此时可以直接插入 x；

## 综上所述

正确的做法其实是使用多个判断语句分情况讨论：

1. 如果 `curr is node`，说明已经一圈结束，且所有元素都相同，可以插入；
2. `curr.val <= x <= curr.next.val`，即恰好有位置插入；
3. 如果已经到 tail： `curr.val > curr.next.val`，继续判断 首位两元素是否都大于或者都小于 x，如果是，则可以插入；

## Java Code:

```java
    public class Solution {
        /*
         * @param node: a list node in the list
         * @param x: An integer
         * @return: the inserted new list node
         */
        public ListNode insert(ListNode node, int x) {
            ListNode target = new ListNode(x);
            if (node == null) {
                target.next = target;
                return target;
            }

            ListNode curr = node;
            while (true) {
                if (curr.next == node ||
                    (curr.val <= x && curr.next.val >= x) ||
                    (curr.val > curr.next.val && (curr.val < x && curr.next.val < x ||
                     curr.val > x && curr.next.val > x))) {
                    target.next = curr.next;
                    curr.next = target;
                    return target;
                }
                curr = curr.next;
            }
        }
    }
```

## Python Code:

```python
    """
    Definition of ListNode
    class ListNode(object):

        def __init__(self, val, next=None):
            self.val = val
            self.next = next
    """
    class Solution:
        # @param {ListNode} node a list node in the list
        # @param {int} x an integer
        # @return {ListNode} the inserted new list node
        def insert(self, node, x):
            target = ListNode(x)
            if node is None:
                target.next = target
                return target

            curr = node

            while True:
            # *****************************************************

                if (curr.next is node) or (curr.val <= x and curr.next.val >=x) or \
                   (curr.val > curr.next.val and \
                    (curr.val > x and curr.next.val > x or curr.val < x and curr.next.val < x)):
            # *****************************************************
                    target.next = curr.next
                    curr.next = target
                    return target
                curr = curr.next
            return None
```

_update 2017-11-21 14:18:45_

## 总结

面对一道题的时候，应该在动手coding之前先想好所有的可能情况，有了通盘考虑之后再开始动手。

_update 2018-6-30 22:27_

## Update

一种新的想法：

其实如果给定list的元素种类不是唯一的，就一定可以在一次循环中搞定，那么如果在循环结束（回到给定node）后没有搞定，则说明list中元素是唯一的，此时就可以直接插入；

所以在实现的时候，先用一层while循环处理list中元素不唯一的情况，如果在这次循环中没有解决，则直接插入target；

```java
public class Solution {
    public ListNode insert(ListNode node, int x) {
        ListNode target = new ListNode(x);
        target.next = target;
        if (node == null) return target;
        ListNode curr = node;
        while (curr.next != node) {
            if (curr.val <= x && curr.next.val >= x) {
                target.next = curr.next;
                curr.next = target;
                return target;
            } else if (curr.val > curr.next.val) {
                // curr is min
                if (x >= curr.val && x >= curr.next.val || x <= curr.val && x <= curr.next.val) {
                    target.next = curr.next;
                    curr.next = target;
                    return target;
                }
            }
            curr = curr.next;
        }
        // 此时 curr.next == node, 循环一周后退出，说明所有元素都一样
        target.next = curr.next;
        curr.next = target;
        return target;
    }
}
```

