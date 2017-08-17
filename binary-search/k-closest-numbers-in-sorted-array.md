## K Closest Numbers In Sorted Array
_update Aug 17, 2017 14:11_

---
[LintCode](http://www.lintcode.com/en/problem/k-closest-numbers-in-sorted-array/)

Given a target number, a non-negative integer k and an integer array A sorted in ascending order, find the k closest numbers to target in A, sorted in ascending order by the difference between the number and target. Otherwise, sorted in ascending order by number if the difference is same.

**Example**

    Given A = [1, 2, 3], target = 2 and k = 3, return [2, 1, 3].
    
    Given A = [1, 4, 6, 8], target = 3 and k = 3, return [4, 1, 6].

**Challenge **
O(logn + k) time complexity.

#### Basic Idea:
先用二分法找到target，或者距离target最近的元素，然后向两侧扩展找k-1个最接近target的。

#### Java Code：
```java
    public class Solution {
        /**
         * @param A an integer array
         * @param target an integer
         * @param k a non-negative integer
         * @return an integer array
         */
        public int[] kClosestNumbers(int[] A, int target, int k) {
            // Write your code here
            // find target first
            if (k == 0) return new int[] {};
            int p = 0, r = A.length - 1;
            while (p + 1 < r) {
                int q = (r - p) / 2 + p;
                if (A[q] < target) p = q;
                else r = q;
            }
            int pos = -1;
            if (A[p] == target) pos = p;
            else if (A[r] == target) pos = r;
            else pos = Math.abs(target - A[p]) <= Math.abs(target - A[r]) ? p : r;
    
            
            // find k closest numbers
            List<Integer> res = new ArrayList<>();
            res.add(A[pos]);
            int count = 1;
            int start = pos, end = pos;
            while (count < k) {
                if (end == A.length - 1 || start > 0 &&
            Math.abs(target - A[start - 1]) <= Math.abs(target - A[end + 1])) {
                    start--;
                    res.add(A[start]);
                }
                else {
                    end++;
                    res.add(A[end]);
                }
                count++;
            }
            int[] ret = new int[res.size()];
            for (int i = 0; i < res.size(); ++i) {
                ret[i] = res.get(i);
            }
            return ret;
        }
    }
```

#### Python Code:
```python
    class Solution:
        # @param {int[]} A an integer array
        # @param {int} target an integer
        # @param {int} k a non-negative integer
        # @return {int[]} an integer array
        def kClosestNumbers(self, A, target, k):
            if k == 0:
                return []
            # find target first
            p, r = 0, len(A) - 1
            while p + 1 < r:
                q = (r - p) / 2 + p
                if A[q] < target:
                    p = q
                else:
                    r = q
            pos = p if abs(A[p] - target) <= abs(A[r] - target) else r # 注意这里是<=
            
            # 向左右扩展，找k个最近的
            left, right = pos, pos
            count = 1
            ret = [A[pos]]
            while count < k:
                if right == len(A) - 1 or abs(A[left - 1] - target) <= abs(A[right + 1] - target): # 注意这里也是<=
                    left -= 1
                    count += 1
                    ret.append(A[left])
                else:
                    right += 1
                    count += 1
                    ret.append(A[right])
            
            return ret
```