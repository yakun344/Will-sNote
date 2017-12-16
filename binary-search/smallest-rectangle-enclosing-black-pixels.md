## Smallest Rectangle Enclosing Black Pixels
_update Aug 17,2017  17:20_

---
[LeetCode](https://leetcode.com/problems/smallest-rectangle-enclosing-black-pixels/description/)


An image is represented by a binary matrix with 0 as a white pixel and 1 as a black pixel. The black pixels are connected, i.e., there is only one black region. Pixels are connected horizontally and vertically. Given the location (x, y) of one of the black pixels, return the area of the smallest (axis-aligned) rectangle that encloses all black pixels.

    For example, given the following image:
    
    [
      "0010",
      "0110",
      "0100"
    ]
and x = 0, y = 2,
Return 6.

#### Basic Idea:
**思路 1：二分法**
上下左右分别搜索，时间复杂度应该是 O(mlogn + nlogm).

**思路 2：BFS 或者 DFS**
时间复杂度为O(Number of black pixels) == O(mn).

#### Python Code:
二分法：
```python
    class Solution(object):
        def minArea(self, image, x, y):
            """
            :type image: List[List[str]]
            :type x: int
            :type y: int
            :rtype: int
            """
            up = self.searchUp(image, x)
            down = self.searchDown(image, x)
            left = self.searchLeft(image, y)
            right = self.searchRight(image, y)
            
            print (up, down, left, right)
            
            return (down - up + 1) * (right - left + 1)
            
            
        def searchCol(self, image, col):
            for i in range(len(image)):
                if image[i][col] == '1':
                    return True
            return False
        
        def searchRow(self, image, row):
            for i in range(len(image[0])):
                if image[row][i] == '1':
                    return True
            return False
        
        def searchUp(self, image, row):
            p, r = 0, row
            while p + 1 < r:
                q = (p + r) / 2
                if self.searchRow(image, q):
                    r = q
                else:
                    p = q
            if self.searchRow(image, p):
                return p
            else:
                return r
        
        def searchDown(self, image, row):
            p, r = row, len(image) - 1
            while p + 1 < r:
                q = (p + r) / 2
                if self.searchRow(image, q):
                    p = q
                else:
                    r = q
            if self.searchRow(image, r):
                return r
            else:
                return p
            
        def searchLeft(self, image, col):
            p, r = 0, col
            while p + 1 < r:
                q = (p + r) / 2
                if self.searchCol(image, q):
                    r = q
                else:
                    p = q
            if self.searchCol(image, p):
                return p
            else:
                return r
            
        def searchRight(self, image, col):
            p, r = col, len(image[0]) - 1
            while p + 1 < r:
                q = (p + r) / 2
                if self.searchCol(image, q):
                    p = q
                else:
                    r = q
            if self.searchCol(image, r):
                return r
            else:
                return p
```

#### Java Code：
DFS：
```java
    // dfs solution
    public class Solution {
        private int[] dr = null;
        private int[] dc = null;
        private int up;
        private int down;
        private int left;
        private int right;
        public int minArea(char[][] image, int x, int y) {
            dr = new int[] {0, 1, 0, -1};
            dc = new int[] {1, 0, -1, 0};
            up = down = x;
            left = right = y;
            dfs(image, x, y);
            return (right - left + 1) * (down - up + 1);
        }
        
        private void dfs(char[][] image, int r, int c) {
            if (! isValid(image, r, c) || image[r][c] == '0') return;
            image[r][c] = '0';
            if (r < up) up = r;
            if (r > down) down = r;
            if (c < left) left = c;
            if (c > right) right = c;
            for (int i = 0; i < dr.length; ++i) {
                dfs(image, r + dr[i], c + dc[i]);
            }
        }
        
        private boolean isValid(char[][] image, int r, int c) {
            if (r < 0 || c < 0 || r >= image.length || c >= image[0].length) 
                return false;
            return true;
        }
    }
```

BFS:
```java
    // bfs solution
    public class Solution {
        private class Coord {
            int r;
            int c;
            public Coord(int r, int c) {
                this.r = r;
                this.c = c;
            }
        }
        
        private int[] dr = null;
        private int[] dc = null;
        private int up;
        private int down;
        private int left;
        private int right;
        public int minArea(char[][] image, int x, int y) {
            dr = new int[] {0, 1, 0, -1};
            dc = new int[] {1, 0, -1, 0};
            up = down = x;
            left = right = y;
            bfs(image, x, y);
            return (right - left + 1) * (down - up + 1);
        }
        private void bfs(char[][] image, int r, int c) {
            Deque<Coord> queue = new LinkedList<>();
            queue.addFirst(new Coord(r, c));
            while (! queue.isEmpty()) {
                Coord coord = queue.removeLast();
                r = coord.r;
                c = coord.c;
                image[r][c] = '0';
                if (r < up) up = r;
                if (r > down) down = r;
                if (c < left) left = c;
                if (c > right) right = c;
                for (int i = 0; i < dc.length; ++i) {
                    int nr = r + dr[i];
                    int nc = c + dc[i];
                    if (isValid(image, nr, nc) && image[nr][nc] == '1') {
                        queue.addFirst(new Coord(nr, nc));
                    }
                    
                }
            }
        }
        private boolean isValid(char[][] image, int r, int c) {
            if (r < 0 || c < 0 || r >= image.length || c >= image[0].length) 
                return false;
            return true;
        }
    }
```
---
_update Dec 16, 2017_
#### Python BFS solution
```python
    # bfs solution
    class Solution:
        def minArea(self, image, x, y):
            """
            :type image: List[List[str]]
            :type x: int
            :type y: int
            :rtype: int
            """
            def isValid(x, y):
                if x < 0 or x >= len(image) or y < 0 or y >= len(image[0]):
                    return False
                return True
            
            dx = (0, -1, 0, 1)
            dy = (1, 0, -1, 0)
            up, down = x, x
            left, right = y, y
            queue = collections.deque()
            queue.appendleft((x, y))
            while queue:
                r, c = queue.pop()
                if not isValid(r, c) or image[r][c] == '0': 
                    continue
                image[r][c] = '0'
                up = min(up, r)
                down = max(down, r)
                left = min(left, c)
                right = max(right, c)
                for i in range(4):
                    queue.appendleft((r + dx[i], c + dy[i]));
            return (down - up + 1) * (right - left + 1)
```