## Median of two Sorted Arrays
_update Aug 15, 2017 22:41_

---
[LintCode](http://www.lintcode.com/en/problem/median-of-two-sorted-arrays/#)

There are two sorted arrays A and B of size m and n respectively. Find the median of the two sorted arrays.

**Example**

    Given A=[1,2,3,4,5,6] and B=[2,3,4,5], the median is 3.5.
    
    Given A=[1,2,3] and B=[4,5], the median is 3.

**Challenge **
The overall run time complexity should be O(log (m+n)).

#### Basic Idea:
思路的关键：

1.  把找median转换成求第 `(m+n)/2` 个数的问题；
2.  明确当要找第K个数时，移除两数组中较小的第 k/2 个数及其之前的数是安全的；

于是，我们就可以得到一个log(n)时间的解法。

实现的细节：当两个数组不一样长的时候，可能会出现 k/2 比短的那个数组长度还大，此时我们可以假设两个数组都是无限长，超过有效值部分都是 INF；

#### Java Code：
```java
    class Solution {
        /**
         * @param A: An integer array.
         * @param B: An integer array.
         * @return: a double whose format is *.5 or *.0
         */
        public double findMedianSortedArrays(int[] A, int[] B) {
            if ((A.length + B.length) % 2 != 0) {
                // 如果是奇数个，则中位数是第(m+n)/2+1个数
                return findKth(A, 0, B, 0, (A.length + B.length) / 2 + 1);
            } else {
                // 如果是偶数，则是(m+n)/2 和 (m+n)/2+1 的平均值
                return (findKth(A, 0, B, 0, (A.length + B.length) / 2) + 
                       findKth(A, 0, B, 0, (A.length + B.length) / 2 + 1)) / 2;
            }
        }
        private double findKth(int[] A, int indexA, int[] B,int indexB, int k) {
            if (indexA >= A.length) {  // A 为空
                return B[indexB + k - 1];
            }
            if (indexB >= B.length) {  // B 为空
                return A[indexA + k - 1];
            }
            if (k == 1) {  // 出口，k表示第k个，从1开始
                return Math.min(A[indexA], B[indexB]);
            }
            // 分别找出两数组中的第 k/2 个数
            double a = Integer.MAX_VALUE;
            double b = Integer.MAX_VALUE;
            if (indexA + k / 2 - 1 < A.length) {
                a = A[indexA + k/2 - 1];
            }
            if (indexB + k / 2 - 1 < B.length) {
                b = B[indexB + k / 2 - 1];
            }
            if (a < b) {
                return findKth(A, indexA + k / 2, B, indexB, k - k / 2);
            } else {
                return findKth(A, indexA, B, indexB + k / 2, k - k / 2);
            }
        }
    }
```

#### Python
```python
    class FreqNode(object):
        def __init__(self, freq):
            self.freq = freq
            self.prev = None
            self.next = None
            self.head = DataNode(-1, -1)
            self.tail = DataNode(-1, -1)
            self.head.next = self.tail
            self.tail.prev = self.head
            
    class DataNode(object):
        def __init__(self, key, value):
            self.key = key
            self.val = value
            self.freq = 1
            self.prev = None
            self.next = None
    
    class LFUCache(object):
    
        def __init__(self, capacity):
            """
            :type capacity: int
            """
            self.capacity = capacity
            self.head = FreqNode(0)
            self.tail = FreqNode(0)
            self.head.next = self.tail
            self.tail.prev = self.head
            self.FreqMap = {}
            self.DataMap = {}
    
        def get(self, key):
            """
            :type key: int
            :rtype: int
            """
            if key not in self.DataMap:
                return -1
            dnode = self.DataMap[key]
            self.updateFreq(dnode)
            return dnode.val
            
    
        def put(self, key, value):
            """
            :type key: int
            :type value: int
            :rtype: void
            """
            if self.capacity == 0:
                return
            if key in self.DataMap:
                dnode = self.DataMap[key]
                dnode.val = value
                self.updateFreq(dnode)
            else:
                # 若已满，则移除freq最小的fnode中靠近head的dnode
                if len(self.DataMap) == self.capacity:
                    fnode = self.head.next
                    dnode = fnode.head.next
                    del self.DataMap[dnode.key]
                    self.removeDataNode(dnode)
                    if self.isEmpty(fnode):
                        del self.FreqMap[fnode.freq]
                        self.removeFreqNode(fnode)
                
                self.ensureFirstFreq()
                fnode = self.FreqMap[1]
                dnode = DataNode(key, value)
                self.DataMap[key] = dnode
                self.insertDataNode(dnode, fnode)
                    
        
        def isEmpty(self, fnode):
            return fnode.head.next == fnode.tail
            
        def ensureFirstFreq(self):
            if self.head.next.freq == 1:
                return
            fnode = FreqNode(1)
            self.FreqMap[1] = fnode
            fnode.next = self.head.next
            fnode.prev = self.head
            self.head.next = fnode
            fnode.next.prev = fnode
            
        def insertDataNode(self, dnode, fnode):
            # 把dnode插入到fnode中的tail之前
            dnode.next = fnode.tail
            dnode.prev = fnode.tail.prev
            dnode.next.prev = dnode
            dnode.prev.next = dnode
            
        def removeFreqNode(self, fnode):
            fnode.prev.next = fnode.next
            fnode.next.prev = fnode.prev
            
        def removeDataNode(self, dnode):
            dnode.prev.next = dnode.next
            dnode.next.prev = dnode.prev
            
        def updateFreq(self, dnode):
            prev_fnode = self.FreqMap[dnode.freq]
            # 检查freq+1是否存在，若存在，则移动，否则先建freq+1的node
            if dnode.freq + 1 not in self.FreqMap:
                new_fnode = FreqNode(dnode.freq + 1)
                new_fnode.prev = prev_fnode
                new_fnode.next = prev_fnode.next
                new_fnode.prev.next = new_fnode;
                new_fnode.next.prev = new_fnode;
                self.FreqMap[dnode.freq + 1] = new_fnode
            
            new_fnode = self.FreqMap[dnode.freq + 1]
            self.removeDataNode(dnode)
            self.insertDataNode(dnode, new_fnode)
            if self.isEmpty(prev_fnode):
                del self.FreqMap[dnode.freq]
                self.removeFreqNode(prev_fnode)
            dnode.freq += 1
```