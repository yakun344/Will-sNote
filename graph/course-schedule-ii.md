## Course Schedule II
_update Jul 20, 2017 12:10_

---
[lintcode](http://www.lintcode.com/en/problem/course-schedule-ii/)    
[LeetCode](https://leetcode.com/problems/course-schedule-ii/description/)   

There are a total of n courses you have to take, labeled from 0 to n - 1.
Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: [0,1]

Given the total number of courses and a list of prerequisite pairs, return the ordering of courses you should take to finish all courses.

There may be multiple correct orders, you just need to return one of them. If it is impossible to finish all courses, return an empty array.

**Example**

    Given n = 2, prerequisites = [[1,0]]
    Return [0,1]

    Given n = 4, prerequisites = [1,0],[2,0],[3,1],[3,2]]
    Return [0,1,2,3] or [0,2,1,3]

#### Basic Idea:
其实就是求topological sort，如果无法sort，返回空列表。注意实现细节，python code is more concise than Java.

#### Java Code:
```java
    public class Solution {
        /**
         * @param numCourses a total of n courses
         * @param prerequisites a list of prerequisite pairs
         * @return the course order
         */

        // 其实就是求topological sort的结果，如果不能，则返回空数组
        public int[] findOrder(int numCourses, int[][] prerequisites) {
            if (numCourses == 0) {
                return new int[]{};
            }
            Map<Integer, Set<Integer>> graph = initGraph(numCourses, prerequisites);
            // count indegree
            Map<Integer, Integer> count = new HashMap<>();
            for (int i = 0; i < numCourses; ++i) count.put(i, 0);
            for (int i = 0; i < numCourses; ++i) {
                for (int neighbor : graph.get(i)) {
                    count.put(neighbor, count.get(neighbor) + 1);
                }
            }
            // bfs
            List<Integer> res = new ArrayList<>();
            Deque<Integer> queue = new LinkedList<>();
            for (int node : count.keySet()) {
                if (count.get(node) == 0) {
                    res.add(node);
                    queue.addFirst(node);
                }
            }
            while (! queue.isEmpty()) {
                int node = queue.removeLast();
                for (int neighbor : graph.get(node)) {
                    count.put(neighbor, count.get(neighbor) - 1);
                    if (count.get(neighbor) == 0) {
                        res.add(neighbor);
                        queue.addFirst(neighbor);
                    }
                }
            }
            for (int node : count.keySet()) {
                if (count.get(node) > 0) return new int[]{};
            }
            int[] ret = new int[numCourses];
            for (int i = 0; i < ret.length; ++i) {
                ret[i] = res.get(ret.length - 1 - i);
            }
            return ret;
        }
        private Map<Integer, Set<Integer>> initGraph(int n, int[][] edges) {
            Map<Integer, Set<Integer>> graph = new HashMap<>();
            for (int i = 0; i < n; ++i) {
                graph.put(i, new HashSet<Integer>());
            }
            for (int[] edge : edges) {
                graph.get(edge[0]).add(edge[1]);
            }
            return graph;
        }
    }
```

#### Python Code:
```python
    class Solution:
        # @param {int} numCourses a total of n courses
        # @param {int[][]} prerequisites a list of prerequisite pairs
        # @return {int[]} the course order
        def findOrder(self, numCourses, prerequisites):
            if numCourses == 0: return []
            graph = self.initGraph(numCourses, prerequisites)
            # count indegree
            count = {}
            for i in range(numCourses):
                count[i] = 0
            for node in range(numCourses):
                for neighbor in graph[node]:
                    count[neighbor] += 1
            # do bfs
            res = [node for node in graph if count[node] == 0]
            queue = collections.deque(res)
            while queue:
                node = queue.pop()
                for neighbor in graph[node]:
                    count[neighbor] -= 1
                    if count[neighbor] == 0:
                        queue.appendleft(neighbor)
                        res.append(neighbor)
            # check if the graph can be topologically sorted
            if [node for node in graph if count[node] > 0]:
                return []
            res.reverse()
            return res


        def initGraph(self, n, edges):
            graph = {}
            for i in range(n):
                graph[i] = set()
            for edge in edges:
                u, v = edge[0], edge[1]
                graph[u].add(v)
            return graph
```

<br>

---
_update 2018-05-19 19:59:46_

#### C++ Kahn's Algorithm BFS Solution
```cpp
class Solution {
    vector<vector<int>>& initGraph(int size, vector<pair<int, int>>& edges) {
        vector<vector<int>>& graph = *(new vector<vector<int>>(size));
        for (auto& _pair : edges) {
            graph[_pair.second].push_back(_pair.first); // 反过来写是为了让边从先修课指向后修课
        }
        return graph;
    }
public:
    vector<int> findOrder(int numCourses, vector<pair<int, int>>& prerequisites) {
        auto& graph = initGraph(numCourses, prerequisites);
        unordered_map<int, int> indegreeCount;
        for (int i = 0; i < numCourses; ++i) {
            indegreeCount[i];
            for (int neighbor : graph[i]) {
                ++indegreeCount[neighbor];
            }
        }

        deque<int> _queue;
        for (auto& _pair : indegreeCount) {
            if (_pair.second == 0) _queue.push_back(_pair.first);
        }

        vector<int> res;
        while (! _queue.empty()) {
            int node = _queue.front();
            _queue.pop_front();
            res.push_back(node);
            for (int neighbor : graph[node]) {
                if (--indegreeCount[neighbor] == 0) {
                    _queue.push_back(neighbor);
                }
            }
        }

        // 查看是否还有 indegree > 0 的 node
        for (auto& _pair : indegreeCount) {
            if (_pair.second > 0) return {};
        }

        return res;
    }
};
```

#### Java Naive DFS Solution
```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        List<List<Integer>> graph = initGraph(numCourses, prerequisites);
        List<Integer> res = new ArrayList<>();
        Set<Integer> visited = new HashSet<>();
        for (int i = 0; i < numCourses; ++i) {
            if (! dfs(graph, new HashSet<>(), visited, res, i)) {
                return new int[]{};
            }
        }
        int[] ret = new int[res.size()];
        for (int i = 0; i < ret.length; ++i) {
            ret[i] = res.get(res.size() - 1 - i);
        }
        return ret;
    }

    private List<List<Integer>> initGraph(int size, int[][] edges) {
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < size; ++i) graph.add(new ArrayList<Integer>());
        for (int[] edge : edges) {
            graph.get(edge[1]).add(edge[0]);
        }
        return graph;
    }

    private boolean dfs(List<List<Integer>> graph, Set<Integer> path, Set<Integer> visited, List<Integer> res, int curr) {
        if (path.contains(curr)) {
            return false;
        }
        else if (visited.contains(curr)) return true;
        path.add(curr);
        visited.add(curr);
        for (int neighbor : graph.get(curr)) {
            if (! dfs(graph, path, visited, res, neighbor)) {
                return false;
            }
        }
        path.remove(curr);
        res.add(curr);
        return true;
    }
}
```

---
_update 2018-06-19 22:40:53_

#### Update: 精简版 C++ Count Indegree Solution
```cpp
class Solution {
public:
    vector<int> findOrder(int numCourses, vector<pair<int, int>>& prerequisites) {
        vector<int> res;
        // 建图，统计入度indegree
        unordered_map<int, int> indegrees;
        vector<vector<int>> graph(numCourses);
        for (auto edge : prerequisites) {
            graph[edge.second].push_back(edge.first);
            indegrees[edge.first]++;
        }

        deque<int> q;
        for (int i = 0; i < numCourses; ++i) {
            if (! indegrees.count(i)) q.push_back(i);
        }
        int count = 0;
        while (! q.empty()) {
            int curr = q.front(); q.pop_front();
            res.push_back(curr);
            for (int neighbor : graph[curr]) {
                if (--indegrees[neighbor] == 0) q.push_back(neighbor);
            }
            count++;
        }
        if (count != numCourses) return {};
        return res;
    }
};
```

---
_update 2018-10-10 22:24:21_

### Update: Java DFS Solution
之前的写法都是基于 count indegree 的，这种方法的确是一个更好的做法，也更容易实现，但之前上算法课的时候老师也讲过使用 DFS 来做 topological sort 的方法。

需要注意的有两点：

* 要用 `visited` 和 `finished` 两个set来分别探测是否有环以及当前node是否已经在别的支路中被搜索过，这里 finished 的作用其实就是看当前节点是否已经被加入 res 中；
* 因为结果被存放在res中，可以利用dfs函数的返回值来标志是否有环。

#### Java Code：
```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < numCourses; ++i) graph.add(new ArrayList<>());
        for (int[] edge : prerequisites) {
            graph.get(edge[0]).add(edge[1]);
        }
        List<Integer> res = new ArrayList<>();
        Set<Integer> finished = new HashSet<>();
        for (int i = 0; i < numCourses; ++i) {
            if (! finished.contains(i)) {
                if (! dfs(graph, i, new HashSet<>(), finished, res)) return new int[]{};
            }
        }
        return res.stream().mapToInt(a->{return Integer.valueOf(a);}).toArray();
    }

    private boolean dfs(List<List<Integer>> graph, int curr, Set<Integer> visited, Set<Integer> finished, List<Integer> res) {
        if (finished.contains(curr)) return true;
        else if (! visited.add(curr)) return false;
        for (int neighbor : graph.get(curr)) {
            if (! dfs(graph, neighbor, visited, finished, res)) return false;
        }
        visited.remove(curr);
        finished.add(curr);
        res.add(curr);
        return true;
    }
}
```
