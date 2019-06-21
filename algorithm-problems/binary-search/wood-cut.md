# Wood Cut

_update Aug 18,2017 13:05_

[LintCode](http://www.lintcode.com/en/problem/wood-cut/)

Given n pieces of wood with length L\[i\] \(integer array\). Cut them into small pieces to guarantee you could have equal or more than k pieces with the same length. What is the longest length you can get from the n pieces of wood? Given `L & k`, return the maximum length of the small pieces.

**Notice**

You couldn't cut wood into float length.

If you couldn't get &gt;= k pieces, return 0.

**Example**

```text
For L=[232, 124, 456], k=7, return 114.
```

**Challenge**  O\(n log Len\), where Len is the longest length of the wood.

## Basic Idea:

根据`O(nloglen)`的时间复杂度，我们可以猜测需要使用二分法。我们可以采用二分答案，然后验证答案是否valid的思路，使用二分法测试从 1 开始一直到 max\(L\) 的值，每次检查该值能否切除不少于 k 段。

## Java Code:

```java
    public class Solution {
        /*
         * @param L: Given n pieces of wood with length L[i]
         * @param k: An integer
         * @return: The maximum length of the small pieces
         */
        public int woodCut(int[] L, int k) {
            long sum = 0; // 用int可能会溢出
            int max = Integer.MIN_VALUE;
            for (int l : L) {
                sum += l;
                max = Math.max(max, l);
            }
            if (k > sum) return 0; // 此时即使每段长度都是1也无法切出k段，所以返回0
            int p = 1, r = max;
            while (p + 1 < r) {
                int q = p + (r - p) / 2;
                if (isValid(L, k, q)) p = q;
                else r = q;
            }
            if (isValid(L, k, r)) return r;
            else return p;
        }
        private boolean isValid(int[] L, int k, int target) {
            int num = 0;
            for (int l : L) {
                num += l / target;
            }
            return num >= k;
        }
    };
```

_update Dec 16, 2017_

## Python Code:

```python
    class Solution:
        """
        @param: L: Given n pieces of wood with length L[i]
        @param: k: An integer
        @return: The maximum length of the small pieces
        """
        def woodCut(self, L, k):
            def isValid(L, k, minLen):
                for wood in L:
                    k -= wood // minLen
                return k <= 0


            if not L:
                return 0
            minLen, maxLen = 1, max(L) # 注意：此处从1开始，而不是min
            p, r = minLen, maxLen
            while p + 1 < r:
                q = p + (r - p) // 2
                if isValid(L, k, q):
                    p = q
                else:
                    r = q
            if isValid(L, k, r):
                return r
            elif isValid(L, k, p):
                return p
            else:
                return 0
```

