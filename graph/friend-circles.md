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





