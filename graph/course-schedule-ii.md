## Course Schedule II 
_update Jul 20, 2017 12:10_

---
[lintcode](http://www.lintcode.com/en/problem/course-schedule-ii/)

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