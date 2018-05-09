## Number of Boomerangs
_update May 9,2018  0:34_

---
[LeetCode](https://leetcode.com/problems/number-of-boomerangs/description/)

Given n points in the plane that are all pairwise distinct, a "boomerang" is a tuple of points (i, j, k) such that the distance between i and j equals the distance between i and k (the order of the tuple matters).

Find the number of boomerangs. You may assume that n will be at most 500 and coordinates of points are all in the range `[-10000, 10000]` (inclusive).

**Example:**

    Input:
    [[0,0],[1,0],[2,0]]
    
    Output:
    2

**Explanation:**  

    The two boomerangs are [[1,0],[0,0],[2,0]] and [[1,0],[2,0],[0,0]]
    
### Basic Idea:
没什么特别巧妙的方法，解法基于穷举。

**穷举法：** 首先想到的是穷举，对于每个点，以其作为顶点计算其与其他所有点的距离，最终统计组成的个数。共有 n 个点，每个点耗时 `O(n^2)`, 总计 `O(n^3)`;

**穷举的优化：** 我们可以进行一点优化。因为对于一个点来说，穷举其与所有点的距离，再对每个距离穷举其他距离，耗时 `O(n^2)`。实际上我们只需要将每个点出发的所有距离及其出现次数存入hashmap，可以构成等腰的个数对于一个同样距离dist，count=c 来说，个数即为 `C(c,2) = c * (c - 1)`。 这种方法共耗时 `O(n^2)`;

* #### C++ Code:
```cpp
    class Solution {
        int getDist(const pair<int, int>& p1, const pair<int, int>& p2) {
            return pow(p1.first - p2.first, 2) + pow(p1.second - p2.second, 2);
        }
    public:
        int numberOfBoomerangs(vector<pair<int, int>>& points) {
            int ret = 0;
            for (auto& p1 : points) {
                unordered_map<int, int> dists;
                for (auto& p2 : points) {
                    int dist = getDist(p1, p2);
                    ++dists[dist];
                }
                for (auto pair : dists) {
                    ret += pair.second * (pair.second - 1);
                }
            }
            return ret;
        }
    };
```



