## Friend Circles
_update Sep 7, 18:26_

---
[LeetCode](https://leetcode.com/problems/friend-circles/description/)

There are N students in a class. Some of them are friends, while some are not. Their friendship is transitive in nature. For example, if A is a direct friend of B, and B is a direct friend of C, then A is an indirect friend of C. And we defined a friend circle is a group of students who are direct or indirect friends.

Given a `N*N` matrix M representing the friend relationship between students in the class. If `M[i][j] = 1`, then the ith and jth students are direct friends with each other, otherwise not. And you have to output the total number of friend circles among all the students.

**Example 1:**

    Input: 
    [[1,1,0],
     [1,1,0],
     [0,0,1]]
    Output: 2
Explanation:The 0th and 1st students are direct friends, so they are in a friend circle. 
The 2nd student himself is in a friend circle. So return 2.

**Example 2:**

    Input: 
    [[1,1,0],
     [1,1,1],
     [0,1,1]]
    Output: 1
Explanation:The 0th and 1st students are direct friends, the 1st and 2nd students are direct friends, 
so the 0th and 2nd students are indirect friends. All of them are in the same friend circle, so return 1.

**Note:**

-  N is in range [1,200].
-  `M[i][i] = 1` for all students.
-  If `M[i][j] = 1`, then `M[j][i] = 1.`

#### Basic Idea:
**思路1：DFS or BFS **
每个人相当于一个 vertex，从每个vertex出发进行dfs，每次dfs所发现的其他 v 都是属于同一个连通量（同一个朋友圈）。因此，只要每次进入dfs时判断当前 v 是否已被发现，如没有，则 ret++;   
Time Complexity: 因为dfs或bfs都会每个vertex visit 一遍，所以总时间复杂度为O(n^2)；

Python Code:
```python
    class Solution(object):
        def findCircleNum(self, M):
            """
            :type M: List[List[int]]
            :rtype: int
            """
            def dfs(v):
                if v in visited:
                    return 0
                visited.add(v)
                for i in range(len(M)):
                    if v != i and M[v][i] == 1:
                        dfs(i)
                return 1
                
            ret = 0
            visited = set()
            for i in range(len(M)):
                ret += dfs(i)
            return ret
```

**思路2：Union Find**
每个朋友圈都相当于一个不相交集合。我们通过对并查集的合并，最终所剩下的不相交集合的数量就是朋友圈的数量。  
基本思路是：
-  首先初始化并查集，每个人为一个独立的 disjoint set；
-  接下来对每个人遍历其他人，执行 union(people1, people2)；
-  完成之后，返回当前 disjoint set 的数量；

Python Code:
```python
    class Solution(object):
        def findCircleNum(self, M):
            """
            :type M: List[List[int]]
            :rtype: int
            """
            def find(x):
                while ids[x] != x:
                    ids[x] = find(ids[x])
                    x = ids[x]
                return x
            
            def union(x, y):
                rootx = find(x)
                rooty = find(y)
                if rootx == rooty:
                    return
                if rank[rootx] < rank[rooty]:
                    ids[rootx] = rooty
                elif rank[rootx] > rank[rooty]:
                    ids[rooty] = rootx
                else:
                    ids[rootx] = rooty
                    rank[rooty] += 1
            # 合并成功之后，count--;
                self.count -= 1
            
            def getNumber():
                return self.count
            
            ids = [i for i in range(len(M))]
            rank = [0 for i in range(len(M))]
        # 初始化count为总人数 
            self.count = len(M)
            
            for people in range(len(M)):
                for friend in range(people + 1, len(M)):
                    if M[people][friend] == 1:
                        union(people, friend);
    
            return getNumber();
```

Java Code:
```java
    // 传说中的 Union Find Algorithm
    public class Solution {
        private class UnionFind {
            private int[] ids;
            private int[] rank;
            private int count;
            public UnionFind(int size) {
                this.ids = new int[size];
                this.rank = new int[size];
                this.count = size;
                // 初始化
                Arrays.fill(rank, 0);
                for (int i = 0; i < size; ++i) ids[i] = i;
            }
            
            public int find(int x) {
                while (ids[x] != x) {
                    ids[x] = find(ids[x]);
                    x = ids[x];
                }
                return x;
            }
            
            public void union(int x, int y) {
                int rootx = find(x);
                int rooty = find(y);
                if (rootx == rooty) return;
                if (rank[rootx] < rank[rooty]) {
                    ids[rootx] = rooty;
                } else if (rank[rootx] > rank[rooty]) {
                    ids[rooty] = rootx;
                } else {
                    ids[rootx] = rooty;
                    rank[rooty]++;
                }
                count--;
            }
            
            public int getCount() { 
                return count;
            }
        }
        public int findCircleNum(int[][] M) {
            UnionFind uf = new UnionFind(M.length); 
            for (int i = 0; i < M.length; ++i) {
                for (int j = i + 1; j < M.length; ++j) {
                    if (M[i][j] == 1) {
                        uf.union(i, j);
                    }
                }
            }
            return uf.getCount();
        }
    }
```

___
_update 2018-05-18 20:36:22_

#### C++ Union Find Solution
```cpp
    class Solution {
    private:
        vector<int>* ids;
        vector<int>* ranks;
        int count = 0;
        void makeSet(int size) {
            count = size;
            ranks = new vector<int>(size);
            ids = new vector<int>(size);
            for (int i = 0; i < size; ++i) {
                (*ids)[i] = i;
            }
        }
        int find(int id) {
            if ((*ids)[id] == id) return id;
            while ((*ids)[id] != id) {
                id = (*ids)[id];
            }
            return id;
        }
        void _union(int x, int y) {
            int rootx = find(x);
            int rooty = find(y);
            if (rootx == rooty) return;
            if ((*ranks)[rootx] < (*ranks)[rooty]) {
                (*ids)[rootx] = rooty;
            } else if ((*ranks)[rootx] > (*ranks)[rooty]) {
                (*ids)[rooty] = rootx;
            } else {
                (*ids)[rootx] = rooty;
                ++(*ranks)[rooty];
            }
            --count;
        }
    public:
        int findCircleNum(vector<vector<int>>& M) {
            makeSet(M.size());
            for (int i = 0; i < M.size(); ++i) {
                for (int j = 0; j < M[0].size(); ++j) {
                    if (M[i][j]) _union(i, j);
                }
            }
            return count;
        }
    };
```






