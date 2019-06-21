# Split Linked List in Parts

_update Nov 17,2018_

[LeetCode](https://leetcode.com/problems/split-linked-list-in-parts/)

Given a \(singly\) linked list with head node root, write a function to split the linked list into k consecutive linked list "parts".

The length of each part should be as equal as possible: no two parts should have a size differing by more than 1. This may lead to some parts being null.

The parts should be in order of occurrence in the input list, and parts occurring earlier should always have a size greater than or equal parts occurring later.

Return a List of ListNode's representing the linked list parts that are formed.

Examples `1->2->3->4, k = 5 // 5` equal parts `[ [1], [2], [3], [4], null ]`

**Example 1:**

```text
Input: 
root = [1, 2, 3], k = 5
Output: [[1],[2],[3],[],[]]

Explanation:
The input and each element of the output are ListNodes, not arrays.
For example, the input root has root.val = 1, root.next.val = 2, 
  root.next.next.val = 3, and root.next.next.next = null.
The first element output[0] has output[0].val = 1, output[0].next = null.
The last element output[4] is null, but it's string representation as a ListNode is [].
```

**Example 2:**

```text
Input: 
root = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10], k = 3
Output: [[1, 2, 3, 4], [5, 6, 7], [8, 9, 10]]

Explanation:
The input has been split into consecutive parts with size difference at most 1, and earlier parts are a larger size than the later parts.
```

**Note:**

1. The length of root will be in the range `[0, 1000]`.
2. Each value of a node in the input will be an integer in the range `[0, 999]`.
3. k will be an integer in the range `[1, 50]`.

## Basic Idea:

这道题是要把input list分成k个尽可能长度接近的小段，每段之间长度差不超过1，并且较长的放在前面。基本思路是先求input list的长度，然后用 `len/k` 求得每个part的长度，而 `len%k` 就可以算出前几个part的长度需要加上1. 例如input：`[1,2,3,4,5,6,7,8,9,10,11], k = 3`, output: `[[1,2,3,4], [5,6,7,8], [9,10,11]]`。

### Java Code:

```java
class Solution {
    public ListNode[] splitListToParts(ListNode root, int k) {
        ListNode[] res = new ListNode[k];
        if (root == null) return res;
        int len = 0;
        ListNode curr = root;
        while (curr != null) {
            len++;
            curr = curr.next;
        }
        int partLen = len / k;
        int longerPartNum = len % k;
        curr = root;
        for (int i = 0; i < k && curr != null; ++i) {
            ListNode nextHead = partition(curr, partLen + (longerPartNum > 0 ? 1 : 0));
            longerPartNum--;
            res[i] = curr;
            curr = nextHead;
        }
        return res;
    }

    // return next head
    private ListNode partition(ListNode head, int len) {
        ListNode nextHead = head, prev = null;
        for (int i = 0; i < len; ++i) {
            prev = nextHead;
            nextHead = nextHead.next;
        }
        prev.next = null;
        return nextHead;
    }
}
```

