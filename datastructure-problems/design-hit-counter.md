## Design Hit Counter
_update Sep 11,2017  12:58_

---
[LeetCode](https://leetcode.com/problems/design-hit-counter/description/)

Design a hit counter which counts the number of hits received in the past 5 minutes.

Each function accepts a timestamp parameter (in seconds granularity) and you may assume that calls are being made to the system in chronological order (ie, the timestamp is monotonically increasing). You may assume that the earliest timestamp starts at 1.

It is possible that several hits arrive roughly at the same time.

**Example:**

    HitCounter counter = new HitCounter();
    
    // hit at timestamp 1.
    counter.hit(1);
    
    // hit at timestamp 2.
    counter.hit(2);
    
    // hit at timestamp 3.
    counter.hit(3);
    
    // get hits at timestamp 4, should return 3.
    counter.getHits(4);
    
    // hit at timestamp 300.
    counter.hit(300);
    
    // get hits at timestamp 300, should return 4.
    counter.getHits(300);
    
    // get hits at timestamp 301, should return 3.
    counter.getHits(301); 
    
**Follow up:**  

What if the number of hits per second could be very large? Does your design scale?

#### Basic Idea:
因为是保持最近300秒内的hit个数，而且所给定的各种指令的 timestamp 又都是单调增顺序的，可以自然想到要使用 queue。

1. 比较简单的思路是每当新的hit来到，就把其 timestamp offer，待 query 时用一连串的 poll 将超时的 timestamp 都移出去，同时保持一个 sum 变量；
2. 但是考虑到 follow up，如果有很多hit在同一时间，在remove的时候就需要很多的操作。于是我们改变策略，在 queue 中保持两个变量，timestamp 以及 该时刻的 hit 的 count，如此就可以做到 O(1)时间 remove 一个 timestamp 的所有 hit；

#### Java Code:
```java
    class HitCounter {
        private int count;
        private Deque<Integer> timeQ;
        private Deque<Integer> countQ;
        /** Initialize your data structure here. */
        public HitCounter() {
            this.count = 0;
            this.timeQ = new LinkedList<>();
            this.countQ = new LinkedList<>();
        }
        
        /** Record a hit.
            @param timestamp - The current timestamp (in seconds granularity). */
        public void hit(int timestamp) {
            if (timeQ.isEmpty() || timeQ.peekFirst() != timestamp) {
                timeQ.addFirst(timestamp);
                countQ.addFirst(0);
            }
            countQ.addFirst(countQ.removeFirst() + 1);
            count += 1;
        }
        
        /** Return the number of hits in the past 5 minutes.
            @param timestamp - The current timestamp (in seconds granularity). */
        public int getHits(int timestamp) {
            while (! timeQ.isEmpty() && timestamp - timeQ.peekLast() >= 300) {
                timeQ.removeLast();
                count -= countQ.removeLast();
            }
            return count;
        }
    }
```




























