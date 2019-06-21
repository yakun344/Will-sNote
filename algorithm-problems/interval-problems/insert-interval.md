# Insert Interval

_update Aug 12,2017 12:37_

[LeetCode](https://leetcode.com/problems/insert-interval/description/)

Given a set of non-overlapping intervals, insert a new interval into the intervals \(merge if necessary\).

You may assume that the intervals were initially sorted according to their start times.

**Example 1:**

```text
Given intervals [1,3],[6,9], 
insert and merge [2,5] in as [1,5],[6,9].
```

**Example 2:**

```text
Given [1,2],[3,5],[6,7],[8,10],[12,16], 
insert and merge [4,9] in as [1,2],[3,10],[12,16].

This is because the new interval [4,9] overlaps with [3,5],[6,7],[8,10].
```

## Basic Idea:

[这里](https://syjohnson.gitbooks.io/leetcode/content/13_intervalyu_sao_miao_xian.html) 有一个很好的分析文章，关于这一系列题目。

首先的一个思路是直接将newInterval插入，然后执行之前 merge intervals 那道题目的算法。但是由于此题所给条件是intervals已经排序且没有overlapping，用merge的算法会浪费很多时间。由此我们可以想到一种**优化**的方法：

在遍历的时候，intervals会被分为三段：

* 在 newInterval 左边，无交集；
* 有交集；
* 在 newInterval 右边，无交集；

于是我们可以根据这个性质，用三个while循环分别处理。

## Java Code:

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
        public List<Interval> insert(List<Interval> intervals, Interval newInterval) {
            if (intervals == null || (intervals.size() == 0 && newInterval == null)) return new ArrayList<Interval>();
            List<Interval> res = new ArrayList<>();
            // 分为左中右三部分分别处理, 分别为左边无交集，有交集，右边无交集
            int i = 0;
            while (i < intervals.size() && intervals.get(i).end < newInterval.start) {
                res.add(new Interval(intervals.get(i).start, intervals.get(i).end));
                i++;
            }
            int currStart = newInterval.start;
            int currEnd = newInterval.end;
            while (i < intervals.size() && intervals.get(i).start <= newInterval.end && intervals.get(i).end >= newInterval.start) {
                currStart = Math.min(currStart, intervals.get(i).start);
                currEnd = Math.max(currEnd, intervals.get(i).end);
                i++;
            }
            res.add(new Interval(currStart, currEnd));
            while (i < intervals.size()) {
                res.add(new Interval(intervals.get(i).start, intervals.get(i).end));
                i++;
            }
            return res;
        }
    }
```

## Python Code:

```python
    # Definition for an interval.
    # class Interval(object):
    #     def __init__(self, s=0, e=0):
    #         self.start = s
    #         self.end = e

    class Solution(object):
        def insert(self, intervals, newInterval):
            """
            :type intervals: List[Interval]
            :type newInterval: Interval
            :rtype: List[Interval]
            """
            res = []
            if intervals is None or (not intervals and not newInterval): 
                return res
            i = 0
            # 左边无交集
            while i < len(intervals) and intervals[i].end < newInterval.start:
                res.append(Interval(intervals[i].start, intervals[i].end))
                i += 1
            # 有交集
            currStart = newInterval.start
            currEnd = newInterval.end
            while i < len(intervals) and intervals[i].start <= newInterval.end and intervals[i].end >= newInterval.start:
                currStart = min(currStart, intervals[i].start)
                currEnd = max(currEnd, intervals[i].end)
                i += 1
            res.append(Interval(currStart, currEnd))
            # 右边无交集
            while i < len(intervals):
                res.append(Interval(intervals[i].start, intervals[i].end))
                i += 1
            return res
```

