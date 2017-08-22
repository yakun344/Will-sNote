## K Closest Points
_update Aug 22,2017  14:40_

---
[LintCode](http://www.lintcode.com/en/problem/k-closest-points/)

Given some points and a point origin in two dimensional space, find k points out of the some points which are nearest to origin.

Return these points sorted by distance, if they are same with distance, sorted by x-axis, otherwise sorted by y-axis.

**Example**

    Given points = [[4,6],[4,7],[4,4],[2,5],[1,1]], origin = [0, 0], k = 3
    return [[1,1],[2,5],[4,4]]
    
#### Basic Idea:
思路自然是使用Priority Queue：

1.  只要维持一个以 distance 为 key 的 max heap，保持 heap 的 size==k，最终 heap 中就是距离origin最短的k个元素，时间复杂度为O(n + logk)；也可以用minheap，把所有点都加入heap，然后pop最小的k个元素，但是这样的话时间复杂度最坏情况会变为O(nlogn + logk), 因为相当于用一个一个push的方法建堆消耗了O(nlogn)。
2.  另外要注意的就是当 distance 相等时先后顺序的处理。按照题目要求，三个key分别为（dist，x，y），而这里的处理方法在**JAVA和PYTHON中是不同**的：
  *  在java中，我们通过在定义PriorityQueue的时候定义Comparator匿名类来解决；
  *  在python中，如果我们存入heapq的是一个tuple，则会自动把tuple中的attributes从左至右识别为主次key；
  
#### Java Code：
```java
    /**
     * Definition for a point.
     * class Point {
     *     int x;
     *     int y;
     *     Point() { x = 0; y = 0; }
     *     Point(int a, int b) { x = a; y = b; }
     * }
     */
    public class Solution {
        /**
         * @param points a list of points
         * @param origin a point
         * @param k an integer
         * @return the k closest points
         */
        private class PointWithDist {
            int[] coord;
            int distance;
            public PointWithDist(int x, int y, int dist) {
                this.coord = new int[2];
                this.coord[0] = x;
                this.coord[1] = y;
                this.distance = dist;
            }
        }
        public Point[] kClosest(Point[] points, Point origin, int k) {
            // 维持一个最大堆，大小为k，保存距离最小的k个点
            PriorityQueue<PointWithDist> pq = new PriorityQueue<>(k, new Comparator<PointWithDist>() {
                @Override
                // 重写compare方法解决比较的主要次要key的问题
                public int compare(PointWithDist p1, PointWithDist p2) {
                    if (p1.distance != p2.distance) {
                        return p2.distance - p1.distance;
                    } else if (p1.coord[0] != p2.coord[0]) {
                        return p2.coord[0] - p1.coord[0];
                    } else {
                        return p2.coord[1] - p1.coord[1];
                    }
                }
            });
            for (Point p : points) {
                PointWithDist pwd = new PointWithDist(p.x, p.y, (int)(Math.pow((p.x - origin.x), 2) + Math.pow((p.y - origin.y), 2)));
                
        // 重点！！！ 这里的写法很简洁，限制了pq的size，即如果size大于k，则poll掉当前最大（保留k个最小的）
                pq.offer(pwd);
                if (pq.size() > k) pq.poll();
            }
            Point[] res = new Point[k];
            for (int i = 0; i< k; ++i) {
                PointWithDist pwd = pq.poll();
                res[k - i - 1] = new Point(pwd.coord[0], pwd.coord[1]);
            }
            return res;
        }
    }
```

#### Python Code:
这一次，我们又一次体会到了python与生俱来的那种concise，的确令人印象深刻；
```python
    # Definition for a point.
    # class Point:
    #     def __init__(self, a=0, b=0):
    #         self.x = a
    #         self.y = b
    
    class Solution:
        # @param {Pint[]} points a list of points
        # @param {Point} origin a point
        # @param {int} k an integer
        # @return {Pint[]} the k closest points
        def kClosest(self, points, origin, k):
            def getDist(point):
                return (point.x - origin.x) **2 + (point.y - origin.y) **2
            
            import heapq
            pq = []
            for point in points:
                dist = getDist(point)
                key = (-dist, -point.x, -point.y, point) # max heap，主次key和element
                heapq.heappush(pq, key)
                if len(pq) > k:
                    heapq.heappop(pq)
            
            res = []
            for i in range(len(pq)):
                res.append(heapq.heappop(pq)[3])
            return res[::-1]
```
    