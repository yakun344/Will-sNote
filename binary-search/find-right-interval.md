## Find Right Interval
_update Aug 10, 2017  17:50_

---
[LeetCode](https://leetcode.com/problems/find-right-interval/description/)

Given a set of intervals, for each of the interval i, check if there exists an interval j whose start point is bigger than or equal to the end point of the interval i, which can be called that j is on the "right" of i.

For any interval i, you need to store the minimum interval j's index, which means that the interval j has the minimum start point to build the "right" relationship for interval i. If the interval j doesn't exist, store -1 for the interval i. Finally, you need output the stored value of each interval as an array.

**Note:**
You may assume the interval's end point is always bigger than its start point.
You may assume none of these intervals have the same start point.

**Example 1:**
    
    Input: [ [1,2] ]
    
    Output: [-1]
    
    Explanation: There is only one interval in the collection, so it outputs -1.

**Example 2:**

    Input: [ [3,4], [2,3], [1,2] ]
    
    Output: [-1, 0, 1]
    
    Explanation: There is no satisfied "right" interval for [3,4].
    For [2,3], the interval [3,4] has minimum-"right" start point;
    For [1,2], the interval [2,3] has minimum-"right" start point.

**Example 3:**

    Input: [ [1,4], [2,3], [3,4] ]
    
    Output: [-1, 2, -1]
    
    Explanation: There is no satisfied "right" interval for [1,4] and [3,4].
    For [2,3], the interval [3,4] has minimum-"right" start point.
    
#### Basic Idea:
**思路1：binary search**
先把每个 interval 和原 index 存入 hashmap，然后 copy intervals数组，对其按照 start 排序。然后对原数组中每个interval 的 end，在 sorted array 中 binary search 找第一个大于等于该end 的 interval，它原来的index就对应是一个解。

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
    
    // binary search 
    public class Solution {
        public int[] findRightInterval(Interval[] intervals) {
            // 在map中存储index
            Map<Interval, Integer> map = new HashMap<>();
            for (int i = 0; i < intervals.length; ++i) {
                map.put(intervals[i], i);
            }
    
            // 生成排序的intervals
            Interval[] sorted = new Interval[intervals.length];
            for (int i = 0; i < sorted.length; ++i) {
                sorted[i] = intervals[i];
            }
            Arrays.sort(sorted, (a, b) -> a.start - b.start);
    
            // binary search 找答案
            int[] res = new int[intervals.length];
            for (int i = 0; i < res.length; ++i) {
                int next = binarySearch(sorted, map, intervals[i].end);
                res[i] = next;
            }
            return res;
        }
        // 在排序好的 arr 中找到第一个 start >= target 的interval，返回其在map中对应的的value （index）
        // 如果没找到，则返回 -1
        private int binarySearch(Interval[] arr, Map<Interval, Integer> map, int target) {
            int p = 0, r = arr.length - 1;
            while (p + 1 < r) {
                int q = (r - p) / 2 + p;
                if (arr[q].start < target) p = q;
                else r = q;
            }
            if (arr[p].start >= target) return map.get(arr[p]);
            else if (arr[r].start >= target) return map.get(arr[r]);
            else return -1;
        }
    }
```

**思路2：TreeMap**
利用treemap自带排序的性质，把每个start及其index存入treemap,然后对每个end在map中找大于等于它的最小的start对应的index。

```java
    // Using TreeMap
    // treemap 存储所有的start-index对，然后对每个原interval的end，找treemap中最小的大于等于它的start的index
    public class Solution {
        public int[] findRightInterval(Interval[] intervals) {
            TreeMap<Integer, Integer> map = new TreeMap<>();
            for (int i = 0; i < intervals.length; ++i) {
                map.put(intervals[i].start, i);
            }
            int[] res = new int[intervals.length];
            for (int i = 0; i < intervals.length; ++i) {
                Map.Entry<Integer, Integer> entry = map.ceilingEntry(intervals[i].end);
                res[i] = entry == null ? -1 : entry.getValue();
            }
            return res;
        }
    }
```




























