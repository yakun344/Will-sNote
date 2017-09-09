## Connected Component in Undirected Graph
_update Jul 19, 2017 17:36_

---
[lintCode](http://www.lintcode.com/en/problem/connected-component-in-undirected-graph/)


Find the number connected component in the undirected graph. Each node in the graph contains a label and a list of its neighbors. (a connected component (or just component) of an undirected graph is a subgraph in which any two vertices are connected to each other by paths, and which is connected to no additional vertices in the supergraph.)

** Notice**

Each connected component should sort by label.

**Example**

    Given graph:
    
    A------B  C
     \     |  | 
      \    |  |
       \   |  |
        \  |  |
          D   E
    Return {A,B,D}, {C,E}. Since there are two connected component which is {A,B,D}, {C,E}
    
#### Basic Idea:
对每一个node进行bfs，每次bfs得到的结果就是一个component，同时在visited中标记即可

#### Java Code:
```java
    /**
     * Definition for Undirected graph.
     * class UndirectedGraphNode {
     *     int label;
     *     ArrayList<UndirectedGraphNode> neighbors;
     *     UndirectedGraphNode(int x) { label = x; neighbors = new ArrayList<UndirectedGraphNode>(); }
     * };
     */
    public class Solution {
        /**
         * @param nodes a array of Undirected graph node
         * @return a connected set of a Undirected graph
         */
        public List<List<Integer>> connectedSet(ArrayList<UndirectedGraphNode> nodes) {
            // 对每一个node进行bfs，每次bfs得到的结果就是一个component，同时在visited中标记即可
            List<List<Integer>> res = new ArrayList<>();
            if (nodes == null || nodes.size() == 0) {
                return res;
            }
            Set<UndirectedGraphNode> visited = new HashSet<>();
            for (UndirectedGraphNode node : nodes) {
                bfs(node, visited, res);
            }
            // 这部分排序其实不必要
            Collections.sort(res, new Comparator<List<Integer>>(){
                public int compare(List<Integer> lst1, List<Integer> lst2) {
                    if (lst1.get(0) < lst2.get(0)) return -1;
                    if (lst1.get(0) == lst2.get(0)) return 0;
                    else return 1;
                }
            });
            return res;
        }
        private void bfs(UndirectedGraphNode node, Set<UndirectedGraphNode> visited, 
                        List<List<Integer>> res) {
            if (visited.contains(node)) return;
            Deque<UndirectedGraphNode> queue = new LinkedList<>();
            List<Integer> component = new ArrayList<>();
            component.add(node.label);
            queue.addFirst(node);
            visited.add(node);
            while (! queue.isEmpty()) {
                node = queue.removeLast();
                for (UndirectedGraphNode neighbor : node.neighbors) {
                    if (visited.contains(neighbor)) continue;
                    queue.addFirst(neighbor);
                    visited.add(neighbor);
                    component.add(neighbor.label);
                }
            }
            Collections.sort(component);
            res.add(component);
        }
    }
```

---
_update Sep 8, 2017  23:21_

#### 更新：Union Find Algorithm Solution
求每个 connected component 中的元素，典型的连通性问题，可以使用并查集解决。  
基本思路：
1.  使用并查集统计所有 v 的连通性；
2.  find 所有 v，使用 `Map<Integer, List<Integer>>` 存储；
3.  按照要求排序，输出；

具体地，为了解决这里nodes的label无规律，无法直接作为 id 的问题，我们可以用一个 `Map<Integer, Node>` 为其人为制定 id;

















