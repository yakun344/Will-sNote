## Kth Smallest Element in a Sorted Matrix
_update Aug 4,2017 17:00_

---
[LeetCode](https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/description/)

Given a n x n matrix where each of the rows and columns are sorted in ascending order, find the kth smallest element in the matrix.

Note that it is the kth smallest element in the sorted order, not the kth distinct element.

**Example:**

      matrix = [
         [ 1,  5,  9],
         [10, 11, 13],
         [12, 13, 15]
      ],
      k = 8,
      
      return 13.

**Note: **

You may assume k is always valid, 1 ? k ? n2.

#### Basic Idea:
**思路1：**
利用 priority queue 的性质，我们先把第一行的数字 offer，然后做如下事情 k-1 次：

1. 将当前最小 poll，然后将它下面的元素 offer。
2. 之后，queue.peek 就是第 k 小的数了。

这个方法的原理是我们每次都保证不在 queue 中的数字比 queue 中的都大，那么我们经过 k-1 次的poll之后，一定会得到第 k 小的元素。

时间复杂度：O(klogn)

**代码如下：**
**java：**
```java
    // 为了保存坐标，新建了 wrapper，注意override compareTo的细节
    class Node implements Comparable<Node> {
        int val, x, y;
        public Node(int val, int x, int y) {
            this.val = val;
            this.x = x;
            this.y = y;
        }
        @Override
        public int compareTo(Node that) {
            return this.val - that.val;
        }
    } 
    
    public class Solution {
    
        public int kthSmallest(int[][] matrix, int k) {
            PriorityQueue<Node> heapq = new PriorityQueue();
            for (int i = 0; i < matrix[0].length; ++i) {
                heapq.offer(new Node(matrix[0][i], 0, i));
            }
            for (int i = 0; i < k - 1; ++i) {
                Node peek = heapq.poll();
                int x = peek.x;
                int y = peek.y;
                if (x == matrix.length - 1) {
                    continue;
                }
                heapq.offer(new Node(matrix[x + 1][y], x + 1, y));
            }
            return heapq.peek().val;
        }
    }
```

**python:**
```python
    class Solution:
        # heapq solution
        def kthSmallest(self, matrix, k):
            """
            :type matrix: List[List[int]]
            :type k: int
            :rtype: int
            """
            pq = []
            # 先把第一row的元素都push进pq
            for i in range(len(matrix[0])):
                heapq.heappush(pq, (matrix[0][i], (0, i)))
            minVal = None
            while k > 0:
                minVal, (r, c) = heapq.heappop(pq)
                if r + 1 < len(matrix): 
                    heapq.heappush(pq, (matrix[r + 1][c], (r + 1, c)))
                k -= 1
            return minVal
```

**思路2：**
使用二分法对答案进行二分，然后判断估计的答案在matrix中的排名，判断时可以对每行进行二分。
```java
    // binary search
    // 采用二分答案的方法，初始 p = min(matrix), r = max(matrix)，每次对于猜测的答案mid
    // 在matrix中找mid的排名，
    public class Solution {
        public int kthSmallest(int[][] matrix, int k) {
    		int n = matrix.length;
    		int L = matrix[0][0], R = matrix[n - 1][n - 1];
    		while (L < R) {
    			int mid = L + ((R - L) >> 1);
    			int temp = 0;
    			for (int i = 0; i < n; i++) temp += binary_search(matrix[i], n, mid);
    			if (temp < k) L = mid + 1;
    			else R = mid;
    		}
    		return L;
    	}
    	
    	private int binary_search(int[] row,int R,int x){
    	    int L = 0;
    	    while (L < R){
    	        int mid = (L + R) / 2;
    	        if(row[mid] <= x) L = mid + 1;
    	        else R = mid;
    	    }
    	    return L;
    	}
    }
```

---
_update Dec 2, 2017  22:58_
#### O(n) 时间得到这种 sorted matrix 中某元素的 rank 的方法：
类似 [这道题](https://will-gxz.gitbooks.io/xiaozheng_algo/content/other-problems/search-a-2d-matrix-ii.html), 我们从 matrix 的做下角出发，如果当前元素比 target 大，说明右边都比它大，则往上移动一行。如果当前元素比target小，说明这个元素及其上面一列都比 target 小，则右移一列。因为我们目的不是找到 target，而是确定target的rank，所以找到target之后要 keep going，沿途统计小于它元素的个数。

#### Update Binary Search Solution
之前写的 binary search 手法太过稚嫩，一定是经过了很多次尝试才最终把 base case 设定好。事实上写 binary search 一定要注意 base case： `while p + 1 < r:` 

原本用两个binary search的做法时间复杂度为 `O(nlogn * log(max-min))`，前面一项表示每次确定target在matrix中的rank需要`O(nlogn)`的时间。这次更新的解法，在求rank的时候使用前面的线性时间的方法，将时间复杂度优化到 `O(nlog(max-min))`，在 `max-min` 比较小的情况下比之前使用 priority queue 的 `O(klogn)` 要快。（_这里的 n 指的是 matrix 的边长_）.

**Python Code:**
```python
    class Solution:
        def kthSmallest(self, matrix, k):
            """
            :type matrix: List[List[int]]
            :type k: int
            :rtype: int
            """
            p = matrix[0][0]
            r = matrix[-1][-1]
            while p + 1 < r:
                q = p + (r - p) // 2
                rank = self.getRank(matrix, q)
                # 为了避免找到一个不存在的数（这个数一定比存在的 k-th 数要小），
                # 我们找最后一个在 k 位置的数
                if rank <= k:
                    p = q
                else:
                    r = q
            if self.getRank(matrix, r) <= k: return r # 当 k-th 有重复的时候，rank 可能会小于 k
            else: return p
            
            
        # 求第一个 target 出现时的 rank，one base
        # 使用从左下角开始扫描的 O(n) 解法
        def getRank(self, matrix, target):
            N = len(matrix) # matrix 是正方形
            rank = 0
            r, c = N - 1, 0 # 定位左下角
            while r >= 0 and c < N:
                if matrix[r][c] == target: # 往右上移动
                    rank += r
                    r -= 1
                    c += 1
                elif matrix[r][c] < target: # 往右移
                    rank += r + 1
                    c += 1
                else: # 往上移
                    r -= 1
            print(str(target) + " : " + str(rank + 1))
            return rank + 1 # 此时的rank为小于target数的个数
```
这种解法中存在许多细节：
1. 为了避免返回一个不存在的数（如果要找[1,4,7,9]中的第三大的数，有可能返回5或者6),在写二分法条件判断的时候，要按照找最后一个满足条件的数的情况去写，因为不存在的kth数一定比存在的kth数要小；
2. 为了处理有重复元素的问题，如果二分结束后 r 对应的 rank 小于 k，则说明 kth 有不止一个数字，我们可以认为 r 就是解。

















