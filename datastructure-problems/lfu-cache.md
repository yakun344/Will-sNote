## LFU Cache
_update Aug 24, 2017  23:14_

---
[LeetCode](https://leetcode.com/problems/lfu-cache/description/)

Design and implement a data structure for Least Frequently Used (LFU) cache. It should support the following operations: get and put.

get(key) - Get the value (will always be positive) of the key if the key exists in the cache, otherwise return -1.
put(key, value) - Set or insert the value if the key is not already present. When the cache reaches its capacity, it should invalidate the least frequently used item before inserting a new item. For the purpose of this problem, when there is a tie (i.e., two or more keys that have the same frequency), the least recently used key would be evicted.

**Follow up:**
Could you do both operations in O(1) time complexity?

**Example:**

    LFUCache cache = new LFUCache( 2 /* capacity */ );
    
    cache.put(1, 1);
    cache.put(2, 2);
    cache.get(1);       // returns 1
    cache.put(3, 3);    // evicts key 2
    cache.get(2);       // returns -1 (not found)
    cache.get(3);       // returns 3.
    cache.put(4, 4);    // evicts key 1.
    cache.get(1);       // returns -1 (not found)
    cache.get(3);       // returns 3
    cache.get(4);       // returns 4
    
#### Basic Idea:
[这里](http://bookshadow.com/weblog/2016/11/22/leetcode-lfu-cache/) 有一个非常不错的分析文章。

在我的实现中，整个数据结构示意图：
![](/assets/WechatIMG26.jpg)

基本思想就是：
-  外层： 维护FreqNode系统，保证O(1)时间找到任何freq的fnode（利用FreqMap），同时要做到产生新的freq时候要增加fnode，内部 dnode 为空时候要删除 fnode；
-  内层： 在每个 fnode 下面维护 DataNode 的链表。当某 dnode 的 freq 提升时（该key被set或者get），要将其移动到 freq+1 的 fnode 下。同时在每个 fnode 内部的链表中保持 LRU 原则，即从tail插入，从head删除；
-  另外，当cache满，需要删除现有dnode时候，选择最左边 fnode 下的 dnode 进行删除。

#### Java Code:
```java
    public class LFUCache {
        private class FreqNode {
            int freq;
            FreqNode prev = null;
            FreqNode next = null;
            DataNode head = null;
            DataNode tail = null;
            public FreqNode(int freq) {
                this.freq = freq;
                head = new DataNode(-1, -1);
                tail = new DataNode(-1, -1);
                head.next = tail;
                tail.prev = head;
            }
        }
        
        private class DataNode {
            int key;
            int val;
            int freq = 1;
            DataNode prev = null;
            DataNode next = null;
            public DataNode(int key, int val) {
                this.key = key;
                this.val = val;
            }
        }
        
        private Map<Integer, FreqNode> FreqMap;
        private Map<Integer, DataNode> DataMap;
        private FreqNode head;
        private FreqNode tail;
        private int capacity;
        
        public LFUCache(int capacity) {
            this.capacity = capacity;
            FreqMap = new HashMap<>();
            DataMap = new HashMap<>();
            head = new FreqNode(0);
            tail = new FreqNode(0);
            head.next = tail;
            tail.prev = head;
        }
    
        public void put(int key, int value) {
            if (capacity == 0) return;
            if (DataMap.containsKey(key)) {
                DataNode dnode = DataMap.get(key);
                dnode.val = value;
                updateFreq(dnode);
            } else {
                // 若cache已满, 删除head.next freqnode中的第一个datanode（最老的）
                // 如果移去之后，当前freqnode为空，则删去
                if (DataMap.size() == capacity) {
                    FreqNode fnode = head.next;
                    DataNode dnode = fnode.head.next;
                    DataMap.remove(dnode.key);
                    removeDataNode(dnode);
                    if (fnode.head.next == fnode.tail) {
                        FreqMap.remove(fnode.freq);
                        removeFreqNode(fnode);
                    }
                }
                // 新建新的datanode，插入freq为1的freqnode的tail前(如果没有freq=1，则要新建)
                ensureFirstFreqNode();
                FreqNode fnode = FreqMap.get(1);
                DataNode dnode = new DataNode(key, value);
                DataMap.put(key, dnode);
                insertDataNode(dnode, fnode);
            }
        }
    
        public int get(int key) {
            if (! DataMap.containsKey(key)) return -1;
            DataNode dnode = DataMap.get(key);
            updateFreq(dnode);
            return dnode.val;
        }
        
        // 确保当前结构中有freq=1的fnode
        private void ensureFirstFreqNode() {
            if (FreqMap.containsKey(1)) return;
            FreqNode fnode = new FreqNode(1);
            fnode.next = head.next;
            fnode.prev = head;
            fnode.next.prev = fnode;
            head.next = fnode;
            FreqMap.put(1, fnode);
        }
        
        // 令当前datanode的freq+=1，完成相关所有操作
        private void updateFreq(DataNode dnode) {
            // 先看freq+1是否存在，若不存在，则新建之后插入
            int new_freq = dnode.freq + 1;
            FreqNode new_fnode = null;
            FreqNode prev_fnode = FreqMap.get(dnode.freq);
            if (! FreqMap.containsKey(new_freq)) {
                new_fnode = new FreqNode(new_freq);
                FreqMap.put(new_freq, new_fnode);
                new_fnode.prev = prev_fnode;
                new_fnode.next = prev_fnode.next;
                prev_fnode.next = new_fnode;
                new_fnode.next.prev = new_fnode;
            }
            // 在原fnode中删去dnode，如果之后原fnode为空，则删去fnode
            removeDataNode(dnode);
            if (prev_fnode.head.next == prev_fnode.tail) {
                removeFreqNode(prev_fnode);
                FreqMap.remove(prev_fnode.freq);
            }
            // 更新dnode的freq，并插入新freq对应的fnode
            dnode.freq++;
            new_fnode = FreqMap.get(dnode.freq);
            insertDataNode(dnode, new_fnode);
        }
        
        // 把datanode插入当前freqnode的tail前
        private void insertDataNode(DataNode dnode, FreqNode fnode) {
            dnode.prev = fnode.tail.prev;
            dnode.next = fnode.tail;
            fnode.tail.prev = dnode;
            dnode.prev.next = dnode;
        }
        
        private void removeFreqNode(FreqNode fnode) {
            fnode.prev.next = fnode.next;
            fnode.next.prev = fnode.prev;
        }
        
        private void removeDataNode(DataNode dnode) {
            dnode.prev.next = dnode.next;
            dnode.next.prev = dnode.prev;
        }
    }
```
    