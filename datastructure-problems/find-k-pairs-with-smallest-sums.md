## Find K Pairs with Smallest Sums
_update Jan 15,2018  9:21_

---
You are given two integer arrays `nums1` and `nums2` sorted in ascending order and an integer `k`.

Define a pair `(u,v)` which consists of one element from the first array and one element from the second array.

Find the `k` pairs `(u1,v1),(u2,v2) ...(uk,vk)` with the smallest sums.

**Example 1:**

    Given nums1 = [1,7,11], nums2 = [2,4,6],  k = 3

    Return: [1,2],[1,4],[1,6]

    The first 3 pairs are returned from the sequence:
    [1,2],[1,4],[1,6],[7,2],[7,4],[11,2],[7,6],[11,4],[11,6]
    
**Example 2:**

    Given nums1 = [1,1,2], nums2 = [1,2,3],  k = 2

    Return: [1,1],[1,1]

    The first 2 pairs are returned from the sequence:
    [1,1],[1,1],[1,2],[2,1],[1,2],[2,2],[1,3],[1,3],[2,3]

**Example 3:**

    Given nums1 = [1,2], nums2 = [3],  k = 3 

    Return: [1,3],[2,3]

    All possible pairs are returned from the sequence:
    [1,3],[2,3]
    
<br>

### Basic Idea （BFS2）
基本思想就是用两个指针分别从两数组的头部开始，对于每一对当前最小sum的元素 (ni, nj)，将其后大于它的pair加入 pq，即 (ni+1, nj) 以及 (ni, nj+1)。维持一个 pq，每次 poll 出当前最小，将其加入res，再根据该最小pair 继续 generate 新的pair，直到 pq 为空或者 `res.size() == k`。


#### Java Code:
```java
class Solution {
    private class Pair {
        int x, y;
        Pair(int x, int y) {
            this.x = x;
            this.y = y;
        }
    }
    public List<int[]> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<int[]> res = new ArrayList<>();
        if (nums1 == null || nums2 == null || nums1.length == 0 || nums2.length == 0) return res;
        PriorityQueue<Pair> pq = new PriorityQueue<>((p1, p2) -> 
                                                  Integer.compare(nums1[p1.x] + nums2[p1.y], nums1[p2.x] + nums2[p2.y]));
        boolean[][] visited = new boolean[nums1.length][nums2.length];
        pq.offer(new Pair(0, 0));
        while (res.size() < k && ! pq.isEmpty()) {
            Pair currMin = pq.poll();
            int x = currMin.x, y = currMin.y;
            res.add(new int[] {nums1[x], nums2[y]});
            if (x + 1 < nums1.length && ! visited[x + 1][y]) {
                pq.offer(new Pair(x + 1, y));
                visited[x + 1][y] = true;
            }
            if (y + 1 < nums2.length && ! visited[x][y + 1]) {
                pq.offer(new Pair(x, y + 1));
                visited[x][y + 1] = true;
            }
        }
        return res;
    }
}
```









