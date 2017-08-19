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

题目中给的是node个数以及edges，我们需要先把图转换为adjacent list的形式，即Java中的：`Map<node, Set<node>>`的形式，然后再做bfs。为了应对back edge，我们需要用一个HashSet visited 记录已经visited的node。

#### Java Code：
```java
    public class Solution {
        /**
         * @param n an integer
         * @param edges a list of undirected edges
         * @return true if it's a valid tree, or false
         */
        public boolean validTree(int n, int[][] edges) {
            if (n != edges.length + 1) return false;
            if (n == 1) return true;
            Map<Integer, Set<Integer>> graph = initGraph(n, edges);
            // 用bfs确定每个node是否连通
            Deque<Integer> queue = new LinkedList<>();
            Set<Integer> visited = new HashSet<>();
            queue.add(0);
            visited.add(0);
            while (! queue.isEmpty()) {
                int node = queue.removeFirst();
                for (int neighbor : graph.get(node)) {
                    if (visited.contains(neighbor)) continue;
                    visited.add(neighbor);
                    queue.add(neighbor);
                }
            }
            return visited.size() == n;
        }
        // 把edges转化为adjacent list的形式
        private Map<Integer, Set<Integer>> initGraph(int n, int[][] edges) {
            Map<Integer, Set<Integer>> graph = new HashMap<>();
            for (int[] edge : edges) {
                int u = edge[0];
                int v = edge[1];
                if (! graph.containsKey(u)) graph.put(u, new HashSet<Integer>());
                if (! graph.containsKey(v)) graph.put(v, new HashSet<Integer>());
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