# Exam Room
_update Nov 23, 2018_

---
[LeetCode](https://leetcode.com/problems/exam-room/)

In an exam room, there are N seats in a single row, numbered `0, 1, 2, ..., N-1`.

When a student enters the room, they must sit in the seat that maximizes the distance to the closest person.  If there are multiple such seats, they sit in the seat with the lowest number.  (Also, if no one is in the room, then the student sits at seat number 0.)

Return a class ExamRoom(int N) that exposes two functions: ExamRoom.seat() returning an int representing what seat the student sat in, and ExamRoom.leave(int p) representing that the student in seat number p now leaves the room.  It is guaranteed that any calls to ExamRoom.leave(p) have a student sitting in seat p.

 

**Example 1:**

    Input: ["ExamRoom","seat","seat","seat","seat","leave","seat"], [[10],[],[],[],[],[4],[]]
    Output: [null,0,9,4,2,null,5]

**Explanation:**

    ExamRoom(10) -> null
    seat() -> 0, no one is in the room, then the student sits at seat number 0.
    seat() -> 9, the student sits at the last seat number 9.
    seat() -> 4, the student sits at the last seat number 4.
    seat() -> 2, the student sits at the last seat number 2.
    leave(4) -> null
    seat() -> 5, the student sits at the last seat number 5.
​​​​​​​

**Note:**

1. `1 <= N <= 10^9`
2. ExamRoom.seat() and ExamRoom.leave() will be called at most `10^4` times across all test cases.
3. Calls to ExamRoom.leave(p) are guaranteed to have a student currently sitting in seat number p.

<br/>

### Basic Idea:
这道题就是说一共有N个位置，两个function，一个add，一个leave。add的时候一定要保证add的位置距离最近的已有点最远，leave的时候就删除传入的点。
#### Solution 1, O(N) time
我们可以直接用一个sorted list来存所有有人的位置，然后删除的时候可以直接删除，加人的时候扫描一遍，找距离两边最远的点加人。具体地，扫描的时候考虑每个interval，另外还需要考虑首尾两个位置。
* #### Java Code:
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
#### Solution 2: Log(N) time
因为在刚刚的解法中我们需要 O(N) 的时间才能找到最大的interval，如果要优化时间，就要想办法优化这一步的时间复杂度。我们首先可以想到用TreeSet来存所有的interval。这样每次需要add的时候，我们只需要检查能产生最大dist的interval和左右两边界就可以了。在完成add或者remove操作之后，更新intervals treeSet，整个时间复杂度为 `log(N)`.

  ```java
  class ExamRoom {
    private class Interval implements Comparable<Interval> {
        public int left, right, dist;
        public Interval(int left, int right) {
            this.left = left;
            this.right = right;
            dist = (right - left) / 2; // 关键，要提前/2，否则左右距离为3和4会被认为不同，但其实是相同的
        }

        public boolean equals(Interval that) {
            return this.left == that.left && this.right == that.right;
        }
        
        @Override
        public int compareTo(Interval that) {
            if (this.dist == that.dist) {
                return this.left - that.left;
            } else {
                return that.dist - this.dist;
            }
        }
    }
    
    private int MIN, MAX;
    private TreeSet<Integer> seats;
    private TreeSet<Interval> intervals;
        
    public ExamRoom(int N) {
        seats = new TreeSet<>();
        intervals = new TreeSet<>();
        MIN = 0;
        MAX = N - 1;
    }
    
    public int seat() {
        int pos = MIN, maxDist = -1; // 默认pos位置为0
        if (! seats.isEmpty()) {
            maxDist = seats.first() - MIN;
            if (! intervals.isEmpty()) {
                // 先考虑dist最大的interval
                Interval itv = intervals.first();
                if (itv.dist > maxDist) {
                    maxDist = itv.dist;
                    pos = (itv.right + itv.left) / 2;
                }
            }
            // consider right most position
            if (MAX - seats.last() > maxDist) {
                pos = MAX;
            }
        }
        // update intervals
        if (seats.isEmpty()) {
            ; //pass
        } else if (pos == MIN) {
            intervals.add(new Interval(MIN, seats.first()));
        } else if (pos == MAX) {
            intervals.add(new Interval(seats.last(), MAX));
        } else {
            int left = seats.lower(pos);
            int right = seats.higher(pos);
            intervals.remove(new Interval(left, right));
            intervals.add(new Interval(left, pos));
            intervals.add(new Interval(pos, right));
        }
        seats.add(pos);
        return pos;
    }
    
    public void leave(int p) {
        Integer left = seats.lower(p);
        Integer right = seats.higher(p);
        if (left != null && right != null) {
            intervals.remove(new Interval(left, p));
            intervals.remove(new Interval(p, right));
            intervals.add(new Interval(left, right));
        } else if (left == null && right == null) {
            ; // pass
        } else if (left == null) {
            intervals.remove(new Interval(p, right));
        } else {
            intervals.remove(new Interval(left, p));
        }
        seats.remove(p);
    }
  }
  ```
