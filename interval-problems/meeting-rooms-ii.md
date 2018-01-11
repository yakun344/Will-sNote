## Meeting Rooms II
_update Aug 12, 2017  10:39_

---
[LeetCode](https://leetcode.com/problems/meeting-rooms-ii/description/)


Given an array of meeting time intervals consisting of start and end times [[s1,e1],[s2,e2],...] (si < ei), find the minimum number of conference rooms required.

**For example,**

    Given [[0, 30],[5, 10],[15, 20]],
    return 2.

#### Basic Idea:
[这里](https://discuss.leetcode.com/topic/35253/explanation-of-super-easy-java-solution-beats-98-8-from-pinkfloyda/2) 是非常好的分析。

基本思路就是对start时间和end时间分别排序，然后维持两个指针开始遍历两个数组。具体思路如下图所示（摘自leetcode discussion）

![](/assets/Untitled-1.jpg)

这种思路可以算作是：**基于事件的分析**

#### Java Code:
```java
    /**
     * Definition for an interval.
     * public class Interval {
     *     int start;
     *     int end;
     *     Interval() { start = 0; end = 0; }
     *     Interval(int s, int e) { start = s; end = e; }
     * }
     */
    public class Solution {
        public int minMeetingRooms(Interval[] intervals) {
            if (intervals == null || intervals.length == 0) return 0;
            int[] starts = new int[intervals.length];
            int[] ends = new int[intervals.length];
            for (int i = 0; i < intervals.length; ++i) {
                starts[i] = intervals[i].start;
                ends[i] = intervals[i].end;
            }
            Arrays.sort(starts);
            Arrays.sort(ends);
            int count = 0;
            int i = 0, j = 0;
            while (i < starts.length) {
                if (starts[i] < ends[j]) {
                    // 说明当前情况下没有新的会议结束，即无空房，要开新房
                    count++;
                    i++;
                } else {
                    // 说明有之前开始的某场会议已经结束，可以使用之前的房间
                    i++;
                    j++;
                }
            }
            return count;
        }
    }
```