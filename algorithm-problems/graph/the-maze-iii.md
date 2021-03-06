# The Maze III

_update Sep 6, 2017 19:59_

[LeetCode](https://leetcode.com/problems/the-maze-iii/description/)

There is a ball in a maze with empty spaces and walls. The ball can go through empty spaces by rolling up \(u\), down \(d\), left \(l\) or right \(r\), but it won't stop rolling until hitting a wall. When the ball stops, it could choose the next direction. There is also a hole in this maze. The ball will drop into the hole if it rolls on to the hole.

Given the ball position, the hole position and the maze, find out how the ball could drop into the hole by moving the shortest distance. The distance is defined by the number of empty spaces traveled by the ball from the start position \(excluded\) to the hole \(included\). Output the moving directions by using 'u', 'd', 'l' and 'r'. Since there could be several different shortest ways, you should output the lexicographically smallest way. If the ball cannot reach the hole, output "impossible".

The maze is represented by a binary 2D array. 1 means the wall and 0 means the empty space. You may assume that the borders of the maze are all walls. The ball and the hole coordinates are represented by row and column indexes.

**Example 1**

```text
Input 1: a maze represented by a 2D array

0 0 0 0 0
1 1 0 0 1
0 0 0 0 0
0 1 0 0 1
0 1 0 0 0

Input 2: ball coordinate (rowBall, colBall) = (4, 3)
Input 3: hole coordinate (rowHole, colHole) = (0, 1)

Output: "lul"
```

Explanation: There are two shortest ways for the ball to drop into the hole.  
The first way is left -&gt; up -&gt; left, represented by "lul".  
The second way is up -&gt; left, represented by 'ul'.  
Both ways have shortest distance 6, but the first way is lexicographically smaller because 'l' &lt; 'u'. So the output is "lul".

**Example 2**

```text
Input 1: a maze represented by a 2D array

0 0 0 0 0
1 1 0 0 1
0 0 0 0 0
0 1 0 0 1
0 1 0 0 0

Input 2: ball coordinate (rowBall, colBall) = (4, 3)
Input 3: hole coordinate (rowHole, colHole) = (3, 0)
Output: "impossible"
```

Explanation: The ball cannot reach the hole.

**Note:**

* There is only one ball and one hole in the maze.  
* Both the ball and hole exist on an empty space, and they will not be at the same position initially.  
* The given maze does not contain border \(like the red rectangle in the example pictures\), but you could assume the border of the maze are all walls.   
* The maze contains at least 2 empty spaces, and the width and the height of the maze won't exceed 30.  

### Basic Idea:

???????????????????????????????????????????????????????????????????????????????????????dfs???bfs????????????????????????????????????????????????????????????Greg?????????????????????????????????????????????????????????????????????????????? Dijkstra ??????????????????

**????????????** ?????????

* ?????????????????????????????????path????????? Vertex class?????????????????? `distance[][]` table ?????? pq ?????????????????????
* ???????????????????????????????????????????????????????????????????????? pq ??? distance table ??? Vertex ??????????????????path ???????????? append ???????????? char??????
* ????????? side effect???distance ?????? pq ??? ????????????????????? vertex ????????????????????????
* ?????? poll ????????????????????? Vertex ???????????? hole ????????????????????????????????? path???
* ???????????????????????????Dijkstra ??????????????????????????????????????????

  > ????????????????????????????????????neighbor???distance???????????? oldDistance == newDistance ????????????????????????pred?????????????????????????????????pred???????????????list?????????????????????????????????????????????

* ????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????

  > ????????????????????????????????????????????????????????????????????????????????????????????????????????????JAVA??????????????????override compareTo ??????????????? 1st key: distance, 2nd key: path ?????????????????????????????????????????????neighbor ???????????????

* ?????????????????????????????? hole ?????????????????????????????????????????????????????????????????????hole?????????????????????

  > ??????????????? hole ???????????????????????? break ???????????????hole???????????? distance ??? path ?????? pq????????? Dijkstra ????????????????????? hole ???????????? pq ?????? poll ???????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????????? pq ???????????????????????????????????? override ??? compareTo ????????????????????????path????????????

### Java Code:

```java
    class Solution {
        private class Vertex implements Comparable<Vertex>{
            int r, c, distance;
            String path;
            public Vertex(int r, int c, int distance) {
                this.r = r;
                this.c = c;
                this.distance = distance;
                this.path = "";
            }
            public Vertex(Vertex that) {
                this.r = that.r;
                this.c = that.c;
                this.path = that.path;
                this.distance = that.distance;
            }

            // ????????????????????????distance??? 1st key??????path??? 2nd key?????????v.compareTo(anotherVertex) ??????
            @Override
            public int compareTo(Vertex v) {
                if (this.distance != v.distance) return this.distance - v.distance;
                else return this.path.compareTo(v.path);
            }
        }

        int[][] MAZE;
        int R, C;

        public String findShortestWay(int[][] maze, int[] ball, int[] hole) {
            MAZE = maze;
            R = maze.length;
            C = maze[0].length;
            int[] dr = new int[]{-1, 1, 0, 0};
            int[] dc = new int[]{0, 0, -1, 1};
            char[] dir = new char[]{'u', 'd', 'l', 'r'};
            Vertex[][] distance = new Vertex[R][C];
            PriorityQueue<Vertex> pq = new PriorityQueue<>();

            // dijkstra
            int r = ball[0], c = ball[1];
            distance[r][c] = new Vertex(r, c, 0);
            pq.offer(new Vertex(r, c, 0));
            while (! pq.isEmpty()) {
                Vertex v = pq.poll();
                if (v.r == hole[0] && v.c == hole[1]) return distance[hole[0]][hole[1]].path;
                // ???????????????????????????????????????????????????pq????????????
                if (distance[v.r][v.c] != null && v.compareTo(distance[v.r][v.c]) > 0) continue;
                for (int i = 0; i < 4; ++i) {
                    r = v.r;
                    c = v.c;
                    int count = 0;
                    while (isValid(r + dr[i], c + dc[i])) {
                        r += dr[i];
                        c += dc[i];
                        count++;
                        // ??????break?????????hole????????????distance??????pq????????????poll???pq???????????????????????????????????????????????????
                        // ?????????????????? distance[hole].path
                        if (r == hole[0] && c == hole[1]) {
                            break;
                        }
                    }
                    // ??????distance table ??? pq ????????? Vertex ??????????????? side effect
                    if (count == 0) continue;
                    Vertex temp = new Vertex(r, c, v.distance + count);
                    temp.path = v.path + dir[i];
                    if (distance[r][c] == null || distance[r][c].compareTo(temp) > 0) {
                        distance[r][c] = temp;
                        pq.offer(new Vertex(temp));
                    }
                }
            }
            return "impossible";
        }

        private boolean isValid(int r, int c) {
            if (r < 0 || r >= R || c < 0 || c >= C) return false;
            if (MAZE[r][c] == 1) return false;
            return true;
        }
    }
```

### Python Code:

**???????????????**  
???????????????????????? [Top K Frequent Words](https://will-gxz.gitbooks.io/xiaozheng_algo/content/datastructure-problems/top-k-frequent-words.html) ????????????????????????????????????python?????????????????? string ?????????????????????????????? key ?????? priority queue ???????????????????????????????????????????????????????????????????????????inner class????????? override `__lt__` ?????????????????? Java ?????? interface Comparable ?????? compareTo ????????????????????????????????????????????????????????? heapq ??????????????????

```python
    class Solution(object):
        # define an inner class Vertex, just like java way
        class Vertex(object):
            def __init__(self, r, c, distance, path):
                self.r = r
                self.c = c
                self.distance = distance
                self.path = path

            # override less than method?????????pq?????????
            def __lt__(self, that):
                if self.distance != that.distance:
                    return self.distance < that.distance
                else:
                    return self.path < that.path


        def findShortestWay(self, maze, ball, hole):
            """
            :type maze: List[List[int]]
            :type ball: List[int]
            :type hole: List[int]
            :rtype: str
            """
            def isValid(r, c):
                if r < 0 or r >= R or c < 0 or c >= C: return False
                if maze[r][c] == 1: return False
                return True

            R, C = len(maze), len(maze[0])
            dr = [-1, 1, 0, 0]
            dc = [0, 0, -1, 1]
            dirs = ['u', 'd', 'l', 'r']
            pq = []
            distance = [[None for i in range(C)] for j in range(R)]

            # Dijkstra
            r, c = ball[0], ball[1]
            distance[r][c] = Solution.Vertex(r, c, 0, '')
            heapq.heappush(pq, Solution.Vertex(r, c, 0, ''))
            while pq:
                v = heapq.heappop(pq)
                # ??? hole ???????????????????????????
                if (v.r == hole[0] and v.c == hole[1]):
                    return distance[v.r][v.c].path
                # ????????????????????????????????????
                if distance[v.r][v.c] and distance[v.r][v.c] < v: # ????????? < ????????????????????????__lt__
                    continue
                # ???????????????
                for i in range(4):
                    r, c = v.r, v.c
                    count = 0
                    while isValid(r + dr[i], c + dc[i]):
                        r += dr[i]
                        c += dc[i]
                        count += 1
                        if r == hole[0] and c == hole[1]:
                            break
                    newVertex = Solution.Vertex(r, c, v.distance + count, v.path + dirs[i])
                    if distance[r][c] is None or newVertex < distance[r][c]:
                        distance[r][c] = newVertex
                        heapq.heappush(pq, Solution.Vertex(r, c, newVertex.distance, newVertex.path))

            return 'impossible'
```

_update 2018-05-25 21:53:35_

### C++ Solution

??????????????????????????????????????? Dijkstra ????????????????????????????????????????????????????????????????????????

* ???????????? generate ???????????????????????? dist ???????????????????????? generate ??????????????????????????????????????????dist?????????dist??????????????????????????????????????????lazy deletion ?????????pq???????????????dist??????
* ?????????pq??????????????? pq ??????????????????????????????????????????????????????????????????????????? dist??????????????????????????????????????????????????? ???lazy deletion??????
* ?????? hole ????????????????????? break????????????????????????????????? pq ??????

```cpp
class Solution {
    struct Vertex {
        int r, c, dist;
        string path;
        Vertex(int r, int c, int dist, string path): r(r), c(c), dist(dist), path(path) {}
    };

    bool isValid(vector<vector<int>>& maze, int r, int c) {
        if (r < 0 || r >= maze.size() || c < 0 || c >= maze[0].size()) return false;
        else if (maze[r][c]) return false;
        else return true;
    }

public:
    string findShortestWay(vector<vector<int>>& maze, vector<int>& ball, vector<int>& hole) {
        vector<int> dr{1, 0, 0, -1};
        vector<int> dc{0, -1, 1, 0};
        vector<string> dir{"d", "l", "r", "u"};
        auto comp = [](Vertex v1, Vertex v2)->bool{
            if (v1.dist != v2.dist) return v1.dist > v2.dist;
            else return v1.path > v2.path;
        };
        // ???dist???path?????????key??? min heap
        priority_queue<Vertex, vector<Vertex>, decltype(comp)> pq(comp);
        // ???hashmap?????????dists
        unordered_map<string, int> dists;
        pq.emplace(ball[0], ball[1], 0, "");
        dists[to_string(ball[0]) + "," + to_string(ball[1])] = 0;

        while (! pq.empty()) {
            Vertex curr = pq.top(); pq.pop();
            // ?????? lazy deletion ??? vertex
            if (dists[to_string(curr.r) + "," + to_string(curr.c)] < curr.dist) continue;
            else if (curr.r == hole[0] && curr.c == hole[1]) {
                return curr.path; // ???????????????????????????????????????????????????
            }
            for (int i = 0; i < 4; ++i) {
                int r = curr.r, c = curr.c, dist = curr.dist;
                string path = curr.path;
                while (isValid(maze, r + dr[i], c + dc[i])) {
                    r += dr[i];
                    c += dc[i];
                    ++dist;
                    if (r == hole[0] && c == hole[1]) {
                        cout << dist << " " << path + dir[i] << endl;
                        break;
                    }
                }
                if (r == curr.r && c == curr.c) continue;
                path += dir[i];
                string key = to_string(r) + "," + to_string(c);
                // ?????????????????????????????????iterator????????????hashmap?????????
                auto it = dists.find(key);
                if (it != dists.end() && it->second >= dist) {
                    it->second = dist;
                    pq.emplace(r, c, dist, path);
                } else if (it == dists.end()) {
                    dists[key] = dist;
                    pq.emplace(r, c, dist, path);
                }
            }
        }
        return "impossible";
    }
};
```

_update 2018-07-27 00:08:03_

## Update Java

????????????????????????visited?????????hashmap??????????????????2d array??????????????????????????????

```java
class Solution {
    int[] dr = {0, 1, -1, 0};
    int[] dc = {1, 0, 0, -1};
    char[] dir = {'r', 'd', 'u', 'l'};

    private class Node {
        int r, c, dist;
        String path;
        public Node(int r, int c, int dist, String path) {
            this.r = r; this.c = c; this.dist = dist; this.path = path;
        }
    }

    public String findShortestWay(int[][] maze, int[] ball, int[] hole) {
        PriorityQueue<Node> pq = new PriorityQueue<>((a, b)->{
            if (a.dist != b.dist) return Integer.compare(a.dist, b.dist);
            else return a.path.compareTo(b.path);
        });
        Map<String, Integer> dists = new HashMap<>();
        pq.offer(new Node(ball[0], ball[1], 0, ""));
        while (! pq.isEmpty()) {
            Node curr = pq.poll();
            String currKey = curr.r + "," + curr.c;
            if (dists.getOrDefault(currKey, Integer.MAX_VALUE) <= curr.dist) continue; // ?????????node???lazy delete???????????????
            else if (curr.r == hole[0] && curr.c == hole[1]) {
                return curr.path; // ??????
            } else {
                dists.put(currKey, curr.dist); // ??????pq???poll???????????????????????????????????????
            }
            // expand ????????????????????????
            for (int i = 0; i < 4; ++i) {
                int r = curr.r, c = curr.c, dist = curr.dist;
                String path = curr.path;
                while (isValid(maze, r + dr[i], c + dc[i])) {
                    r += dr[i];
                    c += dc[i];
                    dist++;
                    if (r == hole[0] && c == hole[1]) {
                        break;
                    }
                }
                if (r == curr.r && c == curr.c) continue; // ??????????????????????????????
                path += dir[i];
                String thisKey = r + "," + c;
                if (dist <= dists.getOrDefault(thisKey, Integer.MAX_VALUE)) {
                    pq.offer(new Node(r, c, dist, path));
                }
            }
        }
        return "impossible";
    }

    private boolean isValid(int[][] maze, int r, int c) {
        if (r < 0 || r >= maze.length || c < 0 || c >= maze[0].length) return false;
        else if (maze[r][c] == 1) return false;
        else return true;
    }
}
```

