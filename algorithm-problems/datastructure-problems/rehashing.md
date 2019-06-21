# Rehashing

_update Aug 22, 2017_

[LintCode](http://www.lintcode.com/en/problem/rehashing/)

The size of the hash table is not determinate at the very beginning. If the total size of keys is too large \(e.g. size &gt;= capacity / 10\), we should double the size of the hash table and rehash every keys. Say you have a hash table looks like below:

```text
    size=3, capacity=4

    [null, 21, 14, null]
           ↓    ↓
           9   null
           ↓
          null
    The hash function is:

    int hashcode(int key, int capacity) {
        return key % capacity;
    }
```

here we have three numbers, 9, 14 and 21, where 21 and 9 share the same position as they all have the same hashcode 1 \(21 % 4 = 9 % 4 = 1\). We store them in the hash table by linked list.

rehashing this hash table, double the capacity, you will get:

```text
    size=3, capacity=8

    index:   0    1    2    3     4    5    6   7
    hash : [null, 9, null, null, null, 21, 14, null]
```

Given the original hash table, return the new hash table after rehashing .

**Notice**

For negative integer in hash table, the position can be calculated as follow:

C++/Java: if you directly calculate -4 % 3 you will get -1. You can use function: a % b = \(a % b + b\) % b to make it is a non negative integer. Python: you can directly use -1 % 3, you will get 2 automatically.

**Example**

```text
    Given [null, 21->9->null, 14->null, null],

    return [null, 9->null, null, null, null, 21->null, 14->null, null]
```

## Basic Idea:

其实就是rehashing的基本操作，用linkedList解决collision；

## Java Code:

```java
    /**
     * Definition for ListNode
     * public class ListNode {
     *     int val;
     *     ListNode next;
     *     ListNode(int x) {
     *         val = x;
     *         next = null;
     *     }
     * }
     */
    public class Solution {
        /**
         * @param hashTable: A list of The first node of linked list
         * @return: A list of The first node of linked list which have twice size
         */    
        public ListNode[] rehashing(ListNode[] hashTable) {
            int capacity = 2 * hashTable.length;
            ListNode[] newTable = new ListNode[capacity];
            for (int i = 0; i < hashTable.length; ++i) {
                ListNode node = hashTable[i];
                while (node != null) {
                    int index = hashcode(node.val, capacity);
                    put(newTable, node, index);
                    node = node.next;
                }
            }
            return newTable;
        }
        private void put(ListNode[] table, ListNode node, int index) {
            ListNode copy = new ListNode(node.val);
            if (table[index] == null) table[index] = copy;
            else {
                ListNode root = table[index];
                while (root.next != null) root = root.next;
                root.next = copy;
            }
        }
        private int hashcode(int key, int capacity) {
            if (key >= 0) return key % capacity;
            else return ((key % capacity + capacity) % capacity);
        }
    }
```

