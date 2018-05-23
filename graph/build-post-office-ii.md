## Build Post Office II
_update Jul 19, 2017 16:49_

---
[lintcode](http://www.lintcode.com/en/problem/build-post-office-ii/)

Given a 2D grid, each cell is either a wall 2, an house 1 or empty 0 (the number zero, one, two), find a place to build a post office so that the sum of the distance from the post office to all the houses is smallest.

Return the smallest sum of distance. Return -1 if it is not possible.

** Notice**

    You cannot pass through wall and house, but can pass through empty.
    You only build post office on an empty.

**Example**
    Given a grid:
    
    0 1 0 0 0
    1 0 0 2 1
    0 1 0 0 0
return 8, You can build at (1,1). (Placing a post office at (1,1), the distance that post office to all the house sum is smallest.)

#### Basic Idea:
**方法1**:从空格出发  
循环枚举所有的office修建位置的可能性(空格)   
计算从这个位置出发到达所有房子的距离之和   
在所有方案中找到最小的距离和  

**方法2**:从房子出发  
循环枚举所有的房子的位置    
从房子出发,计算每个空格到达房子的距离  
累加某个空格到达其他所有房子的距离之和    
在所有空格中,找到最小距离和  

以上是九章中提出的用来讨论的两种方式，其实从时间复杂度的角度考虑，如果房子的数量等于空地数量，两种方法是差不多的。但是就这道题而言，方法二会好一些，因为房子的数量相对较少。整个方法的实现有些细节需要关注，但整体其实比较无脑。

**具体的**，需要记录每个空格到所有房子的距离和，以及每个空格能否被所有房子reach。选择能被所有房子reach的最小距离和的空格即可。

#### Java Code:
```java
    public class Solution {
        /**
         * @param grid a 2D grid
         * @return an integer
         */
        
        private int[][] grid;
        private int R, C;
        private int WALL = 2;
        private int HOUSE = 1;
        private int EMPTY = 0;
        private int[] dx = new int[]{1, 0, -1, 0};
        private int[] dy = new int[]{0, 1, 0, -1};
        
        public int shortestDistance(int[][] grid) {
            if (grid == null || grid.length == 0 || grid[0].length == 0) return -1;
            setGrid(grid);
            List<int[]> houses = getCords(HOUSE);
            int[][] distanceSum = new int[R][C];
            int[][] visTimes = new int[R][C];
            for (int[] house : houses) {
                bfs(house, distanceSum, visTimes);
            }
            int minDistanceSum = Integer.MAX_VALUE;
            List<int[]> emptys = getCords(EMPTY);
            
            for (int[] empty : emptys) {
                int m = empty[0];
                int n = empty[1];
                if (visTimes[m][n] != houses.size()) continue;
                minDistanceSum = Math.min(minDistanceSum, distanceSum[m][n]);
            }
            if (minDistanceSum == Integer.MAX_VALUE) return -1;
            return minDistanceSum;
        }
        
        private void setGrid(int[][] grid) {
            this.grid = grid;
            R = grid.length;
            C = grid[0].length;
        }
        
        private List<int[]> getCords(int type) {
            List<int[]> ret = new ArrayList<>();
            for (int i = 0; i < R; ++i) {
                for (int j = 0; j < C; ++j) {
                    if (grid[i][j] == type) {
                        ret.add(new int[]{i, j});
                    }
                }
            }
            return ret;
        }
        
        private boolean isValid(int[] cord) {
            int r = cord[0], c = cord[1];
            if (r < 0 || r >= R || c < 0 || c >= C) {
                return false;
            }
            return true;
        }
        
        // 用distanceSum来记录每个空地的距离和，用visTimes记录每个空地可以被几个房子
        // 接触，如果不能被所有房子接触的空地就不予考虑最终位置。
        private void bfs(int[] cord, int[][] distanceSum, int[][] visTimes) {
            Deque<int[]> queue = new LinkedList<>();
            queue.addFirst(cord);
            boolean[][] visited = new boolean[R][C];
            int distance = 0;
            while (! queue.isEmpty()) {
                int size = queue.size();
                distance++;
                for (int i = 0; i < size; ++i) {
                    int[] rc = queue.removeLast();
                    for (int j = 0; j < 4; ++j) {
                        int m = rc[0] + dx[j];
                        int n = rc[1] + dy[j];
                        if (! isValid(new int[]{m, n})) continue;
                        if (visited[m][n]) continue;
                        if (grid[m][n] == 0) {
                            // visit
                            visTimes[m][n]++;
                            distanceSum[m][n] += distance;
                            visited[m][n] = true;
                            queue.addFirst(new int[]{m, n});
                        }
                    }
                }
            }
        }
    }
```

---
_update 2018-05-22 20:55:37_

#### C++ Code
这次的实现和之前java实现的结构略有不同，感觉这次的更加利于理解：
```cpp
    class Solution {
    public:
        /**
         * @param grid: a 2D grid
         * @return: An integer
         */
        
        const int EMPTY = 0;
        const int HOUSE = 1;
        const int WALL = 2;
        const int dr[4]{-1, 0, 1, 0};
        const int dc[4]{0, -1, 0, 1};
        
        int shortestDistance(vector<vector<int>> &grid) {
            vector<vector<int>> dists(grid.size());
            vector<vector<int>> visitTimes(grid.size());
            for (int i = 0; i < grid.size(); ++i) {
                dists[i].resize(grid[0].size());
                visitTimes[i].resize(grid[0].size());
            }
            auto& houses = getCoords(grid, HOUSE);
            auto& empties = getCoords(grid, EMPTY);
            
            computeDist(grid, houses, visitTimes, dists);
            
            int ret = 0x7fffffff;
            for (auto& coord : empties) {
                int r = coord.first, c = coord.second;
                if (visitTimes[r][c] != houses.size()) continue;
                ret = min(ret, dists[r][c]);
            }
            
            return ret == 0x7fffffff ? -1 : ret;
        }
        
        // 获取house或者empty的全部坐标pair
        vector<pair<int, int>>& getCoords(vector<vector<int>>& grid, int type) {
            vector<pair<int, int>>& ret = *(new vector<pair<int, int>>());
            for (int r = 0; r < grid.size(); ++r) {
                for (int c = 0; c < grid[0].size(); ++c) {
                    if (grid[r][c] == type) ret.push_back(make_pair(r, c));
                }
            }
            return ret;
        }
        
        // 计算从每个house出发到所有empty的距离，更新dists为每个empty到所有house的距离和
        // 并且在 visitTimes 中记录每个 empty 被 visit 的次数，如果一个empty的次数小于house
        // 数量，说明它不能被所有房子reach到，则不考虑该位置
        void computeDist(vector<vector<int>>& grid, vector<pair<int, int>>& houses, 
            vector<vector<int>>& visitTimes, vector<vector<int>>& dists) {
            for (auto& coord : houses) {
                // bfs 计算该house到所有empty的距离
                vector<vector<bool>> visited(grid.size());
                for (vector<bool>& v : visited) v.resize(grid[0].size());
                deque<pair<int, int>> _queue;
                _queue.push_back(coord);
                int step = 1;
                while (! _queue.empty()) {
                    int size = _queue.size();
                    for (int i = 0; i < size; ++i) {
                        auto& currCoord = _queue.front();
                        _queue.pop_front();
                        for (int j = 0; j < 4; ++j) {
                            int r = currCoord.first + dr[j], c = currCoord.second + dc[j];
                            if (r < 0 || r >= grid.size() || c < 0 || c >= grid[0].size()) continue;
                            else if (grid[r][c] == WALL || grid[r][c] == HOUSE) continue;
                            else if (visited[r][c]) continue;
                            _queue.push_back(make_pair(r, c));
                            dists[r][c] += step;
                            visitTimes[r][c]++;
                            visited[r][c] = true;
                        }
                    }
                    ++step;
                }
            }
        }
    };
```

