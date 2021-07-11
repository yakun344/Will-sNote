---
description: 'Jul 10, 2021'
---

# The Skyline Problem

[Leetcode](https://leetcode.com/problems/the-skyline-problem/)



A city's **skyline** is the outer contour of the silhouette formed by all the buildings in that city when viewed from a distance. Given the locations and heights of all the buildings, return _the **skyline** formed by these buildings collectively_.

The geometric information of each building is given in the array `buildings` where `buildings[i] = [lefti, righti, heighti]`:

* `lefti` is the x coordinate of the left edge of the `ith` building.
* `righti` is the x coordinate of the right edge of the `ith` building.
* `heighti` is the height of the `ith` building.

You may assume all buildings are perfect rectangles grounded on an absolutely flat surface at height `0`.

The **skyline** should be represented as a list of "key points" **sorted by their x-coordinate** in the form `[[x1,y1],[x2,y2],...]`. Each key point is the left endpoint of some horizontal segment in the skyline except the last point in the list, which always has a y-coordinate `0` and is used to mark the skyline's termination where the rightmost building ends. Any ground between the leftmost and rightmost buildings should be part of the skyline's contour.

**Note:** There must be no consecutive horizontal lines of equal height in the output skyline. For instance, `[...,[2 3],[4 5],[7 5],[11 5],[12 7],...]` is not acceptable; the three lines of height 5 should be merged into one in the final output as such: `[...,[2 3],[4 5],[12 7],...]`

**Example 1:**

![](../../.gitbook/assets/image%20%285%29.png)

```text
Input: buildings = [[2,9,10],[3,7,15],[5,12,12],[15,20,10],[19,24,8]]
Output: [[2,10],[3,15],[7,12],[12,0],[15,10],[20,8],[24,0]]
Explanation:
Figure A shows the buildings of the input.
Figure B shows the skyline formed by those buildings. The red points in figure B represent the key points in the output list.
```

**Example 2:**

```text
Input: buildings = [[0,2,3],[2,5,3]]
Output: [[0,3],[5,0]]
```

**Constraints:**

* `1 <= buildings.length <= 104`
* `0 <= lefti < righti <= 231 - 1`
* `1 <= heighti <= 231 - 1`
* `buildings` is sorted by `lefti` in non-decreasing order.

### **Basic Idea:**

基本思路还是使用扫描线算法，但我们在排序所有event之后还需要用一个BST来存当前仍未结束的高楼的高度。

#### Java Code:

```java
class Solution {
    private static class Event {
        boolean isStart;
        int h;
        int x;
        
        Event(boolean isStart, int x, int h) {
            this.isStart = isStart;
            this.x = x;
            this.h = h;
        }
    }
    
    public List<List<Integer>> getSkyline(int[][] buildings) {
        List<Event> eventList = new ArrayList<>();
        for (int[] building : buildings) {
            eventList.add(new Event(true, building[0], building[2]));
            eventList.add(new Event(false, building[1], building[2]));
        }
        Collections.sort(eventList, (e1, e2) -> {
            if (e1.x != e2.x) {
                return Integer.compare(e1.x, e2.x);
            } else if (e1.isStart != e2.isStart) {
                return Boolean.compare(e2.isStart, e1.isStart);
            } else if (e1.isStart) {
                return Integer.compare(e2.h, e1.h);
            } else {
                return Integer.compare(e1.h, e2.h);
            }
        });
        
        TreeMap<Integer, Integer> heights = new TreeMap<>();
        heights.put(0, 1);
        List<List<Integer>> res = new ArrayList<>();
        for (int i = 0; i < eventList.size(); ++i) {
            Event event = eventList.get(i);
            if (event.isStart) {
                heights.put(event.h, heights.getOrDefault(event.h, 0) + 1);
                if (heights.lastKey() == event.h && heights.get(event.h) == 1) {
                    res.add(Arrays.asList(event.x, event.h));
                }
            } else {
                if (event.h == heights.lastKey()) {
                    heights.put(event.h, heights.get(event.h) - 1);
                    if (heights.get(event.h) == 0) {
                        heights.remove(event.h);
                        res.add(Arrays.asList(event.x, heights.lastKey()));
                    }
                } else {
                    heights.put(event.h, heights.get(event.h) - 1);
                    if (heights.get(event.h) == 0) {
                        heights.remove(event.h);
                    }
                }
            }
        }
        return res;
    }
}
```

