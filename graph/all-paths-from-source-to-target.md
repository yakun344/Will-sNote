# All Paths From Source to Target

---
_update 2018-10-09 18:01:46_

[LeetCode](https://leetcode.com/problems/all-paths-from-source-to-target/description/)

Given a directed, acyclic graph of N nodes.  Find all possible paths from node 0 to node N-1, and return them in any order.

The graph is given as follows:  the nodes are 0, 1, ..., graph.length - 1.  `graph[i]` is a list of all nodes j for which the edge `(i, j)` exists.

**Example:**

    Input: [[1,2], [3], [3], []]
    Output: [[0,1,3],[0,2,3]]
    Explanation: The graph looks like this:
    0--->1
    |    |
    v    v
    2--->3
    There are two paths: 0 -> 1 -> 3 and 0 -> 2 -> 3.

**Note:**  

1. The number of nodes in the graph will be in the range `[2, 15]`.
2. You can print different paths in any order, but you should keep the order of nodes inside one path.

<br/>

### Basic Idea:
因为要求所有的路径，可以使用DFS。但是考虑到从同一个点出发到终点的所有路径可能被重复求解，使用memorization优化时间复杂度。

* #### Java Code：
  ```java
  class Solution {
      public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
          return dfs(graph, 0, new HashMap<>());
      }

      private List<List<Integer>> dfs(int[][] graph, int curr, Map<Integer, List<List<Integer>>> memory) {
          if (memory.containsKey(curr)) return memory.get(curr);
          List<List<Integer>> ret = new ArrayList<>();
          if (curr == graph.length - 1) {
              List<Integer> lst = new ArrayList<>();
              lst.add(graph.length - 1);
              ret.add(lst);
              memory.put(curr, ret);
              return ret;
          }
          for (int neighbor : graph[curr]) {
              List<List<Integer>> paths = dfs(graph, neighbor, memory);
              for (List<Integer> path : paths) {
                  List<Integer> lst = new ArrayList<>();
                  lst.add(curr);
                  lst.addAll(path);
                  ret.add(lst);
              }
          }
          memory.put(curr, ret);
          return ret;
      }
  }
  ```
