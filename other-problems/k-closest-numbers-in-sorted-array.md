## K Closest Numbers In Sorted Array 
_update Jul 22, 2017 21:31_

---
[LintCode](http://www.lintcode.com/en/problem/k-closest-numbers-in-sorted-array/)

Given a target number, a non-negative integer k and an integer array A sorted in ascending order, find the k closest numbers to target in A, sorted in ascending order by the difference between the number and target. Otherwise, sorted in ascending order by number if the difference is same.

**Example**
    
    Given A = [1, 2, 3], target = 2 and k = 3, return [2, 1, 3].
    
    Given A = [1, 4, 6, 8], target = 3 and k = 3, return [4, 1, 6].
    
#### Basic Idea:
基本思路分为两步：
1.  用 binary search 找到target的位置。
2.  用 2 pointers 的算法向两边扩展，找到 k closest numbers。

#### Python Code：
```python
    class Solution:
        # @param {int[]} A an integer array
        # @param {int} target an integer
        # @param {int} k a non-negative integer
        # @return {int[]} an integer array
        def kClosestNumbers(self, A, target, k):
            if k == 0 or not A: return []
            
            # find the position of target first
            pos = self.biSearch(A, target)
            
            # find K closest elements
            i = j = pos
            res = [A[pos]]
            while j - i + 1 < k:
                if i == 0 :   
                    j += 1
                    res.append(A[j])
                elif j == len(A) - 1: 
                    i -= 1
                    res.append(A[i])
                else:
                    if abs(A[i - 1] - target) <= abs(A[j + 1] - target): #这里的<=
                        i -= 1
                        res.append(A[i])
                    else:
                        j += 1
                        res.append(A[j])
            return res
            
            
            
        def biSearch(self, A, target):
            p, r = 0, len(A) - 1
            while p + 1 < r:
                q = p + (r - p) // 2
                if A[q] < target:
                    p = q
                else:
                    r = q
            pos = p if abs(A[p] - target) <= abs(A[r] - target) else r # 这里的<=
            return pos
```