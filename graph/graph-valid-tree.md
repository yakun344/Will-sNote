## Graph Valid Tree
_update Jul 16, 2017 15:54_

---
Given n nodes labeled from 0 to n - 1 and a list of undirected edges (each edge is a pair of nodes), write a function to check whether these edges make up a valid tree.

**Notice**

You can assume that no duplicate edges will appear in edges. Since all edges are undirected, [0, 1] is the same as [1, 0] and thus will not appear together in edges.

    Example
    Given n = 5 and edges = [[0, 1], [0, 2], [0, 3], [1, 4]], return true.
    
    Given n = 5 and edges = [[0, 1], [1, 2], [2, 3], [1, 3], [1, 4]], return false.
    
#### Basic Idea:
首先是树的两个性质：

1.  `#node = 1 + #edge`
2.  所有node连通

于是我们从这两个性质出发，先判断node和edge的个数是否符合要求，然后用一个BFS来判断是否connected。

题目中给的是node个数以及edges，我们需要先把图转换为adjacent list的形式，即Java中的：`List<Set<Integer>>`的形式，然后再做bfs。为了应对`back edge`，我们需要用一个`HashSet visited` 记录已经`visited` 的 `node`。

#### Java Code：
```java
    // 方法1，对每个V BFS，结束之后看是否已经完成所有V
    class Solution {
        public boolean validTree(int n, int[][] edges) {
            if (edges == null || edges.length != n - 1) return false;
            List<Set<Integer>> graph = initGraph(n, edges);
            
            // bfs, 以 0 为入口
            Deque<Integer> queue = new LinkedList<>();
            Set<Integer> visited = new HashSet<>();
            queue.addFirst(0);
            visited.add(0);
            while (! queue.isEmpty()) {
                int v = queue.removeLast();
                // 出队的才是要visit的，所以此时加入visited
                visited.add(v);
                for (int neighbor : graph.get(v)) {
                    if (visited.contains(neighbor)) continue;
                    queue.addFirst(neighbor);
                }
            }
            return visited.size() == n;
        }
        
        private List<Set<Integer>> initGraph(int n, int[][] edges) {
            List<Set<Integer>> graph = new ArrayList<>();
            for (int i = 0; i < n; ++i) {
                graph.add(new HashSet<Integer>());
            }
            for (int i = 0; i < edges.length; ++i) {
                int u = edges[i][0];
                int v = edges[i][1];
                graph.get(u).add(v);
                graph.get(v).add(u);
            }
            return graph;
        }
    }
```

#### Python Code:
```python
    class Solution:
        # @param {int} n an integer
        # @param {int[][]} edges a list of undirected edges
        # @return {boolean} true if it's a valid tree, or false
        def validTree(self, n, edges):
            # 先把edges list的形式转化成adjacent list的形式，即{node：neighbors}
            def init_graph(n, edges):
                graph = collections.defaultdict(set)
                for edge in edges:
                    u = edge[0]
                    v = edge[1]
                    graph[u].add(v)
                    graph[v].add(u)
                return graph
                
            # 先生成adj list格式的graph，然后用bfs判断是否连通
            if n != len(edges) + 1: return False
            if n == 1: return True
            graph = init_graph(n, edges)
            queue = collections.deque()
            visited = set()
            queue.appendleft(0)
            visited.add(0)
            while queue:
                node = queue.pop()
                for neighbor in graph[node]:
                    if neighbor in visited:
                        continue
                    queue.appendleft(neighbor)
                    visited.add(neighbor)
            return len(visited) == n
```