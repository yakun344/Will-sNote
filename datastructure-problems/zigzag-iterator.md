## Zigzag Iterator
_update Aug 24, 2017  21:25_

---
[LintCode](http://www.lintcode.com/en/problem/zigzag-iterator/)

Given two 1d vectors, implement an iterator to return their elements alternately.

**Example**

    Given two 1d vectors:
    
    v1 = [1, 2]
    v2 = [3, 4, 5, 6]
    
    By calling next repeatedly until hasNext returns false, 
    the order of elements returned by next should be: [1, 3, 2, 4, 5, 6].
    
#### Basic Idea:
这是基础版本，只要用一个boolean指示下一次的元素出自 1 还是 2 就可以了。

#### Java Code：
```java
    public class ZigzagIterator {
        /**
         * @param v1 v2 two 1d vectors
         */
        List<Integer> lst1;
        List<Integer> lst2;
        int i1;
        int i2;
        int next;
        boolean nextIs1;
        public ZigzagIterator(List<Integer> v1, List<Integer> v2) {
            this.lst1 = v1;
            this.lst2 = v2;
            this.i1 = 0;
            this.i2 = 0;
            this.nextIs1 = true;
            this.next = Integer.MIN_VALUE;
        }
    
        public int next() {
            return next;
        }
    
        public boolean hasNext() {
            if (i1 == lst1.size() && i2 == lst2.size()) {
                return false;
            } else if (i1 == lst1.size()) {
                next = lst2.get(i2);
                i2++;
            } else if (i2 == lst2.size()) {
                next = lst1.get(i1);
                i1++;
            } else {
                if (nextIs1) {
                    next = lst1.get(i1);
                    i1++;
                    nextIs1 ^= true;
                } else {
                    next = lst2.get(i2);
                    i2++;
                    nextIs1 ^= true;
                }
            }
            return true;
        }
    }
```

---

## Zigzag Iterator II
[LintCode](http://www.lintcode.com/en/problem/zigzag-iterator-ii/)

Follow up Zigzag Iterator: What if you are given k 1d vectors? How well can your code be extended to such cases? The "Zigzag" order is not clearly defined and is ambiguous for `k > 2` cases. If "Zigzag" does not look right to you, replace "Zigzag" with "Cyclic".


**Example**

    Given k = 3 1d vectors:
    
    [1,2,3]
    [4,5,6,7]
    [8,9]
    Return [1,4,8,2,5,9,3,6,7].
    
#### Basic Idea:
这道题目是之前那道的 Follow Up。事实上需要改动的地方就是把之前的 boolean flag 改为一个数字，用来标识下一个element 所在 list 的序号。同时，还需要建一个 HashMap 记录每个 list 下一个要访问的index；

具体地，我们用一个getNextList 的 helper function 简化逻辑。这个function接受一个当前list的编号，计算下一个仍未遍历完的list编号。如果所有list都已经遍历完，则返回-1；

#### Java Code:
```java
    public class ZigzagIterator2 {
        /**
         * @param vecs a list of 1d vectors
         */
        int currList;
        ArrayList<ArrayList<Integer>> lists;
        Map<Integer, Integer> map;
        int listNum;
        int next;
        
        public ZigzagIterator2(ArrayList<ArrayList<Integer>> vecs) {
            this.lists = vecs;
            this.currList = -1;
            this.listNum = lists.size();
            this.map = new HashMap<Integer, Integer>();
            this.next = Integer.MAX_VALUE;
            for (int i = 0; i < listNum; ++i) {
                this.map.put(i, 0);
            }
        }
    
        public int next() {
            return next;
        }
    
        public boolean hasNext() {
            currList = getNextList(currList);
            if (currList == -1) return false;
            int index = map.get(currList);
            map.put(currList, map.get(currList) + 1);
            next = lists.get(currList).get(index);
            return true;
        }
        
        private int getNextList(int i) {
            int t = 0;
            while (t < listNum) {
                if (i == listNum - 1) {
                    i = 0;
                } else {
                    i++;
                }
                if (map.get(i) < lists.get(i).size()) {
                    return i;
                }
                t++;
            }
            return -1;
        }
    }
```

#### Python Code:
```python
    class ZigzagIterator2:
    
        # @param {int[][]} a list of 1d vectors
        def __init__(self, vecs):
            self.nextElement = float('inf')
            self.map = {}
            self.lists = vecs
            self.currList = -1
            self.listNum = len(self.lists)
            for i in range(len(self.lists)):
                self.map[i] = 0
                
    
        def next(self):
            return self.nextElement
    
    
        def hasNext(self):
            def getNextList(i):
                t = 0
                while t < self.listNum:
                    if i == self.listNum - 1:
                        i = 0
                    else:
                        i += 1
                    if self.map[i] < len(self.lists[i]):
                        return i
                    t += 1
                return -1
                
            self.currList = getNextList(self.currList)
            if self.currList == -1:
                return False
            index = self.map[self.currList]
            self.map[self.currList] += 1
            self.nextElement = self.lists[self.currList][index]
            return True
```




    
    