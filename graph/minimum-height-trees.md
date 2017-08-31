## Minimum Height Trees
_update Aug 30, 2017  22:06_

---
[LeetCode](https://leetcode.com/problems/minimum-height-trees/description/)

For a undirected graph with tree characteristics, we can choose any node as the root. The result graph is then a rooted tree. Among all possible rooted trees, those with minimum height are called minimum height trees (MHTs). Given such a graph, write a function to find all the MHTs and return a list of their root labels.

**Format**
The graph contains n nodes which are labeled from 0 to n - 1. You will be given the number n and a list of undirected edges (each edge is a pair of labels).

You can assume that no duplicate edges will appear in edges. Since all edges are undirected, [0, 1] is the same as [1, 0] and thus will not appear together in edges.

**Example 1:**

        Given n = 4, edges = [[1, 0], [1, 2], [1, 3]]
        
                0
                |
                1
               / \
              2   3
        return [1]

**Example 2:**

        Given n = 6, edges = [[0, 3], [1, 3], [2, 3], [4, 3], [5, 4]]
        
             0  1  2
              \ | /
                3
                |
                4
                |
                5
        return [3, 4]

**Note:**

-  According to the definition of tree on Wikipedia: “a tree is an undirected graph in which any two vertices are connected by exactly one path. In other words, any connected graph without simple cycles is a tree.”

-  The height of a rooted tree is the number of edges on the longest downward path between the root and a leaf.

#### Basic Idea:
首先这是一道Graph的题目，根据图论中的内容，树有如下性质：

        (1) A tree is an undirected graph in which any two vertices are
        connected by exactly one path.
        
        (2) Any connected graph who has n nodes with n-1 edges is a tree.
        
        (3) The degree of a vertex of a graph is the number of
        edges incident to the vertex.
        
        (4) A leaf is a vertex of degree 1. An internal vertex is a vertex of
        degree at least 2.
        
        (5) A path graph is a tree with two or more vertices that is not
        branched at all.
        
        (6) A tree is called a rooted tree if one vertex has been designated
        the root.
        
        (7) The height of a rooted tree is the number of edges on the longest
        downward path between root and a leaf.

对于这道题而言，我们需要的是距离叶节点（degree==1）最大距离最小的节点，也就是图中尽可能接近“中心”的节点。这样就有了一种思路，我们每次删除最外层的一圈叶节点，同时更新与他们相连 node 的 degree，这样就会产生一圈新的叶节点，我们循环往复逐层删除，直到剩下的部分都是叶节点为止（只剩1或2个节点时）；

这个算法的时间复杂度为O(n)，因为我们只是删除每一个节点，而对于树而言，`#E = #V - 1`；

#### Java Code:
第一种写法，使用 `Map<Integer, Set<Integer>>` 的形式；
```java
    // 先将图转化为 adj-list 的形式，然后每次从图中去掉只有一边与其相连的node（叶子）；
    // 如此往复，保存每一层去掉的node，直到去掉了所有node，最后一层就是解
    class Solution {
        public List<Integer> findMinHeightTrees(int n, int[][] edges) {
            Map<Integer, Set<Integer>> Graph = initGraph(edges);
            List<Integer> res = new ArrayList<>();
            if (edges.length == 0) {
                res.add(0);
                return res;
            }
            while (! Graph.isEmpty()) {
                res = new ArrayList<>();
                // 分层操作，一定要分成两步来写，否则前面做出的变化会影响接下来的循环
                for (int node : Graph.keySet()) {
                    Set<Integer> neighbors = Graph.get(node);
                    if (neighbors.size() <= 1) res.add(node);
                }
                for (int leaf : res) {
                    Set<Integer> neighbors = Graph.get(leaf);
                    for (int neighbor : neighbors) Graph.get(neighbor).remove(leaf);
                    Graph.remove(leaf);
                }
            }
            return res;
        }
        private Map<Integer, Set<Integer>> initGraph(int[][] edges) {
            Map<Integer, Set<Integer>> Graph = new HashMap<>();
            for (int[] edge : edges) {
                int n1 = edge[0];
                int n2 = edge[1];
                if (! Graph.containsKey(n1)) Graph.put(n1, new HashSet<Integer>());
                if (! Graph.containsKey(n2)) Graph.put(n2, new HashSet<Integer>());
                Graph.get(n1).add(n2);
                Graph.get(n2).add(n1);
            }
            return Graph;
        }
    }
```

第二种写法，因为题目给出label为 [0, n-1] 的整数，我们可以用一个list代替Map；
```java
    // 和上面的解法不同，由于题目告知所给nodes的label是 [0, n-1]，我们可以利用这点简化代码，不再将Graph 存储在
    // Map<Integer, Set<Integer>> 中，而是可以将图存在 List<Set<Integer>> 中，用 index 标志label；
    class Solution {
        public List<Integer> findMinHeightTrees(int n, int[][] edges) {
            // 先构建图的结构
            List<Set<Integer>> Graph = new ArrayList<>();
            for (int i = 0; i < n; ++i) {
                Graph.add(new HashSet<Integer>());
            }
            for (int[] edge : edges) {
                Graph.get(edge[0]).add(edge[1]);
                Graph.get(edge[1]).add(edge[0]);
            }
            
            // 逐层将叶节点存入res，并在图中删去
            List<Integer> res = new ArrayList<>();
            if (edges.length == 0) {
                res.add(0);
                return res;
            }
            int node_num = n;
            while (node_num > 0) {
                res = new ArrayList<>();
                // 先获得 degree == 1 的节点，存入res，作为此层要删掉的leaf
                for (int i = 0; i < n; ++i) {
                    if (Graph.get(i) == null) continue;
                    Set<Integer> neighbors = Graph.get(i);
                    if (neighbors.size() <= 1) {
                        res.add(i);
                        node_num--;
                    }
                }
                // 在Graph中删去这些叶节点
                for (int leaf : res) {
                    Set<Integer> neighbors = Graph.get(leaf);
                    for (int neighbor : neighbors) Graph.get(neighbor).remove(leaf);
                    Graph.set(leaf, null);
                }
            }
            return res;
        }
    }
```

#### Python Code:
```python
    class Solution(object):
        def findMinHeightTrees(self, n, edges):
            """
            :type n: int
            :type edges: List[List[int]]
            :rtype: List[int]
            """
            if not edges:
                return [0]
            
            # 先得到Graph：dict<Set<int>>
            Graph = collections.defaultdict(set)
            for edge in edges:
                Graph[edge[0]].add(edge[1])
                Graph[edge[1]].add(edge[0])
            
            # 逐层记录leaf节点，并删除
            res = []
            while Graph:
                res = []
                for node in Graph:
                    if len(Graph[node]) <= 1:
                        res.append(node)
                for leaf in res:
                    neighbors = Graph[leaf]
                    for neighbor in neighbors:
                        Graph[neighbor].remove(leaf)
                    del Graph[leaf]
            return res
```
