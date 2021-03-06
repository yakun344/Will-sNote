# Meeting Rooms II

_update Aug 12, 2017 10:39_

[LeetCode](https://leetcode.com/problems/meeting-rooms-ii/description/)

Given an array of meeting time intervals consisting of start and end times \[\[s1,e1\],\[s2,e2\],...\] \(si &lt; ei\), find the minimum number of conference rooms required.

**For example,**

```text
Given [[0, 30],[5, 10],[15, 20]],
return 2.
```

### Basic Idea:

[这里](https://discuss.leetcode.com/topic/35253/explanation-of-super-easy-java-solution-beats-98-8-from-pinkfloyda/2) 是非常好的分析。

基本思路就是对start时间和end时间分别排序，然后维持两个指针开始遍历两个数组。具体思路如下图所示（摘自leetcode discussion）

![](../../.gitbook/assets/untitled-1%20%281%29%20%281%29%20%281%29.jpg)

这种思路可以算作是：**基于事件的分析**

### Java Code:

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

_update Sep 16 2018, 13:58_

## Update: 扫描线法

这个方法的本质和之前是一样的，也是将start和end平等对待，将其当做两种事件。先将每个时间点拿出来，标记为start或者end，然后将他们一起排序。之后从左往右扫描，遇到start就记为overlap+1，遇到end就记为overlap-1，返回值就是历史上最大的overlap数量。但是需要注意的是如果出现一个start和end在同一时间出现，我们需要保证end出现在start的前面，所以需要特别定制comparator的规则。

### Java Code:

```java
class Solution {
    private class Event {
        int time;
        boolean start;
        public Event(int time, boolean start) {
            this.time = time;
            this.start = start;
        }
    }
    public int minMeetingRooms(Interval[] intervals) {
        Event[] arr = new Event[intervals.length * 2];
        for (int i = 0; i < intervals.length; ++i) {
            arr[2 * i] = new Event(intervals[i].start, true);
            arr[2 * i + 1] = new Event(intervals[i].end, false);
        }
        Arrays.sort(arr, (a, b)->{
                if (a.time != b.time) {
                    return Integer.compare(a.time, b.time);
                } else if (a.start) {
                    return 1;
                } else return -1;
            });
        int maxOverlap = 0, overlap = 0;
        for (int i = 0; i < arr.length; ++i) {
            if (arr[i].start) {
                overlap++;
                maxOverlap = Math.max(maxOverlap, overlap);
            } else {
                overlap--;
            }
        }
        return maxOverlap;
    }
}
```

