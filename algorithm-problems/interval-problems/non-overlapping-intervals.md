# Non-overlapping Intervals

_update Aug 11,2017 21:40_

[LeetCode](https://leetcode.com/problems/non-overlapping-intervals/description/)

Given a collection of intervals, find the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping.

**Note:** You may assume the interval's end point is always bigger than its start point. Intervals like `[1,2]` and `[2,3]` have borders "touching" but they don't overlap each other.

**Example 1:**

```text
Input: [ [1,2], [2,3], [3,4], [1,3] ]

Output: 1
```

Explanation: \[1,3\] can be removed and the rest of intervals are non-overlapping.

**Example 2:**

```text
Input: [ [1,2], [1,2], [1,2] ]

Output: 2
```

Explanation: You need to remove two \[1,2\] to make the rest of intervals non-overlapping.

**Example 3:**

```text
Input: [ [1,2], [2,3] ]

Output: 0
```

Explanation: You don't need to remove any of the intervals since they're already non-overlapping.

### Basic Idea:

我们可以尝试基于end的贪心算法。

* 首先，我们对 intervals 排序，key = interval.end；
* 然后，扫描每个interval，维持一个currEnd。如果当前interval的 `start < currEnd`，说明当前interval和之前某个或者某几个发生了重叠，我们此时**贪婪地**抛弃当前interval \(count++\)，然后查看下一个。如果当前interval的 `start >= currEnd`，我们令 `currEnd = interval.end`，然后继续。

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
        public int eraseOverlapIntervals(Interval[] intervals) {
            if (intervals.length <= 1) return 0;
            Arrays.sort(intervals, (a, b) -> a.end - b.end);
            int count = 0;
            int currEnd = Integer.MIN_VALUE;
            for (int i = 0; i < intervals.length; ++i) {
                if (intervals[i].start >= currEnd) currEnd = intervals[i].end;
                else count++;
            }
            return count;
        }
    }
```

### Python Code:

```python
    # Definition for an interval.
    # class Interval(object):
    #     def __init__(self, s=0, e=0):
    #         self.start = s
    #         self.end = e

    class Solution(object):
        def eraseOverlapIntervals(self, intervals):
            """
            :type intervals: List[Interval]
            :rtype: int
            """
            intervals.sort(key = lambda a : a.end)
            count = 0
            currEnd = float('-INF')
            for interval in intervals:
                if interval.start >= currEnd:
                    currEnd = interval.end
                else:
                    count += 1
            return count
```

_update Nov 18, 2018_

## Update: 对start排序的解法

如果不使用贪心算法，我们仍然有办法。首先仍然是对intervals按照start排序，然后从左到右扫描，每次比较 `prev.end` 和 `curr.start`，看是否有overlap，如果有，则移除end更加靠后的那个。

```java
class Solution {
    public int eraseOverlapIntervals(Interval[] intervals) {
        if (intervals.length == 0) return 0;
        Arrays.sort(intervals, (a, b)->Integer.compare(a.start, b.start));
        Interval prev = intervals[0];
        int ret = 0;
        for (int i = 1; i < intervals.length; ++i) {
            if (intervals[i].start < prev.end) {
                ret++;
                if (intervals[i].end < prev.end) prev = intervals[i];
            } else {
                prev = intervals[i];
            }
        }
        return ret;
    }
}
```

