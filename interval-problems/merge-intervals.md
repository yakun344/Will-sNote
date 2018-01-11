## Merge Intervals
_update Aug 12, 2017  11:13_

---
[LeetCode](https://leetcode.com/problems/merge-intervals/description/)

Given a collection of intervals, merge all overlapping intervals.

**For example,**

    Given [1,3],[2,6],[8,10],[15,18],
    return [1,6],[8,10],[15,18].
    
#### Basic Idea:
先将input list 按照start排序，然后想象使用一个扫描线从左往右扫描，跟踪 currStart 和 currEnd 两个边界。

*  如果当前区间在之前区间（[currStart, currEnd]）之内，则不作操作；
*  如果当前区间扩展了之前区间，则更新 currEnd；
*  如果当前区间在之前区间之右（无交集），则把之前区间加入res，更新之前区间为此区间。

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
        public List<Interval> merge(List<Interval> intervals) {
            if (intervals == null || intervals.size() == 0) return new ArrayList<Interval>();
            Collections.sort(intervals, new Comparator<Interval>() {
                public int compare(Interval a, Interval b) {
                    return a.start - b.start;
                }
            });
            int currStart = intervals.get(0).start;
            int currEnd = intervals.get(0).end;
            List<Interval> res = new ArrayList<>();
            for (int i = 1; i < intervals.size(); ++i) {
                Interval interval = intervals.get(i);
                // 之前的区间包含了新区间
                if (interval.end < currEnd) continue;
                // 新区间和之前的有交集，并且扩展了currEnd
                if (interval.start <= currEnd) currEnd = interval.end;
                // 新区间在之前区间右边，无交集，则保存之前的区间
                else {
                    res.add(new Interval(currStart, currEnd));
                    currStart = interval.start;
                    currEnd = interval.end;
                }
            }
            // 把最后一个加入结果
            res.add(new Interval(currStart, currEnd));
            return res;
        }
    }
```