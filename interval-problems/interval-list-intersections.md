# Interval List Intersections
_update Feb 9 2019, 16:35_

---
[LeetCode](https://leetcode.com/problems/interval-list-intersections/)

Given two lists of closed intervals, each list of intervals is pairwise disjoint and in sorted order.

Return the intersection of these two interval lists.

(Formally, a closed interval [a, b] (with a <= b) denotes the set of real numbers x with a <= x <= b.  The intersection of two closed intervals is a set of real numbers that is either empty, or can be represented as a closed interval.  For example, the intersection of [1, 3] and [2, 4] is [2, 3].)

![](https://assets.leetcode.com/uploads/2019/01/30/interval1.png)

**Example 1:**

    Input: A = [[0,2],[5,10],[13,23],[24,25]], 
           B = [[1,5],[8,12],[15,24],[25,26]]
    Output: [[1,2],[5,5],[8,10],[15,23],[24,24],[25,25]]

Reminder: The inputs and the desired output are lists of Interval objects, and not arrays or lists.
 
**Note:**

1. `0 <= A.length < 1000`
2. `0 <= B.length < 1000`
3. `0 <= A[i].start, A[i].end, B[i].start, B[i].end < 10^9`

<br/>

### Basic Idea:
基本思路就是使用双指针从左到右一次比较A和B中的两个interval。需要注意对于条件的判断方法：

```java
    （a.start <= b.end && a.end >= b.start） 就可以说明两个interval a,b 是有交集的
      之后谁的end位置比较靠前，就让谁的指针右移一位
```

#### Java Code:
```java
class Solution {
    public Interval[] intervalIntersection(Interval[] A, Interval[] B) {
        List<Interval> res = new ArrayList<>();
        int i = 0, j = 0;
        while (i < A.length && j < B.length) {
            Interval a = A[i], b = B[j];
            if (a.start <= b.end && a.end >= b.start) {
                // 有交集
                res.add(new Interval(Math.max(a.start, b.start), Math.min(a.end, b.end)));
            }
            if (a.end > b.end) j++;
            else i++;
        }
        return res.stream().toArray(Interval[]::new);
    }
}
```
