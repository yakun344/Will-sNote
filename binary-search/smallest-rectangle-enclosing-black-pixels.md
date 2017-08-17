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
如果使用BFS的话时间复杂度会太高，可以考虑使用二分法。上下左右分别搜索，时间复杂度应该是 O(mlogn + nlogm).

#### Python Code:
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