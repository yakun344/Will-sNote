## LRU Cache
_update Aug 22, 2017  22:30_

---
[LintCode](http://www.lintcode.com/en/problem/lru-cache/#)
[LeetCode](https://leetcode.com/problems/lru-cache/description/)

Design and implement a data structure for Least Recently Used (LRU) cache. It should support the following operations: get and put.

- get(key) - Get the value (will always be positive) of the key if the key exists in the cache, otherwise return -1.
- put(key, value) - Set or insert the value if the key is not already present. When the cache reached its capacity, it should invalidate the least recently used item before inserting a new item.

**Follow up:**
Could you do both operations in O(1) time complexity?

**Example:**

        LRUCache cache = new LRUCache( 2 /* capacity */ );

        cache.put(1, 1);
        cache.put(2, 2);
        cache.get(1);       // returns 1
        cache.put(3, 3);    // evicts key 2
        cache.get(2);       // returns -1 (not found)
        cache.put(4, 4);    // evicts key 1
        cache.get(1);       // returns -1 (not found)
        cache.get(3);       // returns 3
        cache.get(4);       // returns 4

#### Basic Idea:
要实现一个O(1)时间完成get和set操作的 LRU cache 数据结构。以下分析摘自[这里](https://aaronice.gitbooks.io/lintcode/content/data_structure/lru_cache.html)。

- LRU，也就是least recently used，最近使用最少的；这样一个数据结构，能够保持一定的顺序，使得最近使用过的时间或者顺序被记录，实际上，具体每一个item最近一次何时被使用的，并不重要，重要的是在这样的一个结构中，item的相对位置代表了最近使用的顺序；满足这样考虑的结构可以是链表list或者数组array，不过前者更有利于insert和delete的操纵，此外，需要记录这个链表的head和tail，方便进行移动到tail或者删除head的操作，即：head.next作为最近最少使用的item，tail.prev为最近使用过的item，在set时，如果超出capacity，则删除head.next，同时将要插入的item放入tail.prev, 而get时，如果存在，只需把item更新到tail.prev即可。

- 这样set与get均为O(1)时间的操作 （HashMap Get/Set + LinkedList Insert/Delete)，空间复杂度为O(n), n为capacity。

实现过程中要注意使用模块化的思路，写removeNode和insertAtTail两个helper function使代码看上去更易读。

#### Python Code：
这段代码曾在LintCode的环境下ac；
```python
    class LinkedNode:
        def __init__(self, key=-1, val=-1):
            self.val = val
            self.key = key
            self.prev = None
            self.next = None

    class LRUCache:

        # @param capacity, an integer
        def __init__(self, capacity):
            self.CAPACITY = capacity
            self.head = LinkedNode()
            self.tail = LinkedNode()
            self.head.next = self.tail
            self.tail.prev = self.head
            self.table = {}


        # @return an integer
        def get(self, key):
            if key not in self.table:
                return -1
            else:
                node = self.table[key]
                self.removeNode(node)
                self.insertAtTail(node)
                return node.val

        # @param key, an integer
        # @param value, an integer
        # @return nothing
        def set(self, key, value):
            if key not in self.table:
                if len(self.table) == self.CAPACITY:
                    del self.table[self.head.next.key]
                    self.removeNode(self.head.next)
                node = LinkedNode(key, value)
                self.insertAtTail(node)
                self.table[key] = node
            else:
                node = self.table[key]
                node.val = value
                self.removeNode(node)
                self.insertAtTail(node)


        def insertAtTail(self, node):
            node.prev = self.tail.prev
            node.next = self.tail
            node.prev.next = node
            self.tail.prev = node

        def removeNode(self, node):
            node.prev.next = node.next
            node.next.prev = node.prev
```

#### Java Code:
This code got AC in LeetCode;

```java
    class LRUCache {
        private class LinkedNode {
            int key;
            int val;
            LinkedNode prev = null;
            LinkedNode next = null;
            public LinkedNode() {
                key = -1;
                val = -1;
            }
            public LinkedNode(int key, int val) {
                this.key = key;
                this.val = val;
            }
        }

        private LinkedNode head;
        private LinkedNode tail;
        private Map<Integer, LinkedNode> map = null;
        private int CAPACITY;
        public LRUCache(int capacity) {
            this.head = new LinkedNode();
            this.tail = new LinkedNode();
            head.next = tail;
            tail.prev = head;
            this.map = new HashMap<>();
            this.CAPACITY = capacity;
        }

        public int get(int key) {
            if (! map.containsKey(key)) return -1;
            LinkedNode node = map.get(key);
            removeNode(node);
            insertAtTail(node);
            return node.val;
        }

        public void put(int key, int value) {
            if (! map.containsKey(key)) {
                if (map.size() == CAPACITY) {
                    // remove least recent used element
                    LinkedNode node = head.next;
                    map.remove(node.key);
                    removeNode(node);
                }
                // 建一个新的node，放入tail前
                LinkedNode node = new LinkedNode(key, value);
                map.put(key, node);
                insertAtTail(node);
            } else {
                // 原本已经有key，则找到node，改val，移到tail前
                LinkedNode node = map.get(key);
                node.val = value;
                removeNode(node);
                insertAtTail(node);
            }
        }

        private void removeNode(LinkedNode node) {
            node.prev.next = node.next;
            node.next.prev = node.prev;
        }

        private void insertAtTail(LinkedNode node) {
            node.next = tail;
            node.prev = tail.prev;
            node.prev.next = node;
            node.next.prev = node;
        }
    }
```

---
_update Sep 4, 2018 15:29_

#### Upadte: Java Code
这次写好的Java Code 感觉逻辑更加易于理解

```java
class LRUCache {
    private class ListNode {
        int key, val;
        ListNode prev, next;
        public ListNode(int key, int val) {
            this.key = key;
            this.val = val;
        }
    }

    private ListNode head;
    private ListNode tail;
    private final Map<Integer, ListNode> map;
    private final int CAPACITY;
    private int size;

    private void removeLast() {
        ListNode node = tail.prev;
        node.prev.next = tail;
        tail.prev = node.prev;
        map.remove(node.key);
    }

    private void addFirst(int key, int val) {
        ListNode node = new ListNode(key, val);
        map.put(key, node);
        node.next = head.next;
        node.prev = head;
        head.next = node;
        node.next.prev = node;
    }

    private void moveToFirst(ListNode node) {
        if (head.next == node) return;
        node.prev.next = node.next;
        node.next.prev = node.prev;
        node.next = head.next;
        node.prev = head;
        head.next = node;
        node.next.prev = node;
    }

    public LRUCache(int capacity) {
        CAPACITY = capacity;
        head = new ListNode(0, 0);
        tail = new ListNode(0, 0);
        head.next = tail;
        tail.prev = head;
        size = 0;
        map = new HashMap<Integer, ListNode>();
    }

    public int get(int key) {
        ListNode node = map.get(key);
        if (node == null) return -1;
        moveToFirst(node);
        return node.val;
    }

    public void put(int key, int value) {
        ListNode node = map.get(key);
        if (node != null) {
            // already exist key
            node.val = value;
            moveToFirst(node);
        } else {
            // need to add new node
            if (size == CAPACITY) {
                removeLast();
                size--;
            }
            addFirst(key, value);
            size++;
        }
    }
}
```
