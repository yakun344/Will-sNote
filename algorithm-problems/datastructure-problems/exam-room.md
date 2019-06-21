# Exam Room

_update Nov 23, 2018_

[LeetCode](https://leetcode.com/problems/exam-room/)

In an exam room, there are N seats in a single row, numbered `0, 1, 2, ..., N-1`.

When a student enters the room, they must sit in the seat that maximizes the distance to the closest person. If there are multiple such seats, they sit in the seat with the lowest number. \(Also, if no one is in the room, then the student sits at seat number 0.\)

Return a class ExamRoom\(int N\) that exposes two functions: ExamRoom.seat\(\) returning an int representing what seat the student sat in, and ExamRoom.leave\(int p\) representing that the student in seat number p now leaves the room. It is guaranteed that any calls to ExamRoom.leave\(p\) have a student sitting in seat p.

**Example 1:**

```text
Input: ["ExamRoom","seat","seat","seat","seat","leave","seat"], [[10],[],[],[],[],[4],[]]
Output: [null,0,9,4,2,null,5]
```

**Explanation:**

```text
ExamRoom(10) -> null
seat() -> 0, no one is in the room, then the student sits at seat number 0.
seat() -> 9, the student sits at the last seat number 9.
seat() -> 4, the student sits at the last seat number 4.
seat() -> 2, the student sits at the last seat number 2.
leave(4) -> null
seat() -> 5, the student sits at the last seat number 5.
```

​​​​​​​

**Note:**

1. `1 <= N <= 10^9`
2. ExamRoom.seat\(\) and ExamRoom.leave\(\) will be called at most `10^4` times across all test cases.
3. Calls to ExamRoom.leave\(p\) are guaranteed to have a student currently sitting in seat number p.

## Basic Idea:

这道题就是说一共有N个位置，两个function，一个add，一个leave。add的时候一定要保证add的位置距离最近的已有点最远，leave的时候就删除传入的点。

### Solution 1, O\(N\) time

我们可以直接用一个sorted list来存所有有人的位置，然后删除的时候可以直接删除，加人的时候扫描一遍，找距离两边最远的点加人。具体地，扫描的时候考虑每个interval，另外还需要考虑首尾两个位置。

* **Java Code:**

  ```java
  class ExamRoom {
    TreeSet<Integer> seats;
    int MIN, MAX;
    public ExamRoom(int N) {
        seats = new TreeSet<>();
        MIN = 0;
        MAX = N - 1;
    }

    public int seat() {
        int pos = 0, maxDist = 0;
        Iterator<Integer> it = seats.iterator();
        if (it.hasNext()) {
            pos = 0;
            maxDist = seats.first() - MIN;
            int prev = it.next();
            while (it.hasNext()) {
                int curr = it.next();
                int currDist = (curr - prev) / 2;
                if (currDist > maxDist) {
                    maxDist = currDist;
                    pos = (curr + prev) / 2;
                }
                prev = curr;
            }
            // consider the last position
            if (! seats.contains(MAX) && MAX - seats.last() > maxDist) {
                pos = MAX;
            }
        }
        seats.add(pos);
        return pos;
    }

    public void leave(int p) {
        seats.remove(p);
    }
  }
  ```

  **Solution 2: Log\(N\) time**

  因为在刚刚的解法中我们需要 O\(N\) 的时间才能找到最大的interval，如果要优化时间，就要想办法优化这一步的时间复杂度。我们首先可以想到用TreeSet来存所有的interval。这样每次需要add的时候，我们只需要检查能产生最大dist的interval和左右两边界就可以了。在完成add或者remove操作之后，更新intervals treeSet，整个时间复杂度为 `log(N)`.

  ```java
    class ExamRoom {
        private class Interval implements Comparable<Interval> {
            int left, right, dist;
            public Interval(int left, int right) {
                this.left = left;
                this.right = right;
                this.dist = (right - left) / 2;
            }

            @Override 
            public int compareTo(Interval that) {
                if (this.dist == that.dist) {
                    return this.left - that.left;
                } else {
                    return that.dist - this.dist;
                }
            }

            @Override
            public boolean equals(Object obj) {
                if (! (obj instanceof Interval)) return false;
                else {
                    Interval that = (Interval)obj;
                    return this.left == that.left && this.right == that.right;
                }
            }
        }

        private TreeSet<Integer> seats;
        private TreeSet<Interval> intervals;
        private int MIN, MAX;

        public ExamRoom(int N) {
            seats = new TreeSet<>();
            intervals = new TreeSet<>();
            MIN = 0;
            MAX = N - 1;
        }

        public int seat() {
            int pos = MIN, maxDist = 0;
            if (! seats.isEmpty()) {
                maxDist = seats.first() - MIN;
                // check intervals
                if (! intervals.isEmpty()) {
                    Interval itv = intervals.first();
                    if (itv.dist > maxDist) {
                        maxDist = itv.dist;
                        pos = (itv.left + itv.right) / 2; 
                    }
                }
                // check last position
                if (MAX - seats.last() > maxDist) {
                    pos = MAX;
                }
            }
            // update intervals
            Integer prev = seats.lower(pos);
            Integer next = seats.higher(pos);
            if (prev != null && next != null) {
                intervals.remove(new Interval(prev, next));
                intervals.add(new Interval(prev, pos));
                intervals.add(new Interval(pos, next));
            } else if (prev == null && next == null) {
                ; // pass
            } else if (prev == null) {
                intervals.add(new Interval(pos, next));
            } else {
                intervals.add(new Interval(prev, pos));
            }
            seats.add(pos);
            return pos;
        }

        public void leave(int p) {
            Integer prev = seats.lower(p);
            Integer next = seats.higher(p);
            seats.remove(p);
            // update interval
            if (prev != null && next != null) {
                intervals.remove(new Interval(prev, p));
                intervals.remove(new Interval(p, next));
                intervals.add(new Interval(prev, next));
            } else if (prev == null && next == null) {
                ; // pass
            } else if (prev == null) {
                intervals.remove(new Interval(p, next));
            } else {
                intervals.remove(new Interval(prev, p));
            }
        }
    }
  ```

