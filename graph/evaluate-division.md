## Evaluate Division
_update Jul,31 2017 22:43_

---
[LeetCode](https://leetcode.com/problems/evaluate-division/description/)

Equations are given in the format A / B = k, where A and B are variables represented as strings, and k is a real number (floating point number). Given some queries, return the answers. If the answer does not exist, return -1.0.

**Example:**

    Given a / b = 2.0, b / c = 3.0. 
    queries are: a / c = ?, b / a = ?, a / e = ?, a / a = ?, x / x = ? . 
    return [6.0, 0.5, -1.0, 1.0, -1.0 ].

The input is: `vector<pair<string, string>> equations, vector<double>& values, vector<pair<string, string>> queries` , where equations.size() == values.size(), and the values are positive. This represents the equations. Return `vector<double>`.

**According to the example above:**

    equations = [ ["a", "b"], ["b", "c"] ],
    values = [2.0, 3.0],
    queries = [ ["a", "c"], ["b", "a"], ["a", "e"], ["a", "a"], ["x", "x"] ]. 
    The input is always valid. You may assume that evaluating the queries 
    will result in no division by zero and there is no contradiction.
    
#### Basic Idea:
思路就是有题目所给信息构造一个 graph。例如：对于 a / b = 2，则新建 node a，neighbor b，weight = 2， node b，neighbor a，weight = 1/2。然后我们对每个 query，对 graph 做 dfs，从 query[0] 找到 query[1]，沿途的 edge weight 累乘的积就是结果。

#### Java Code:
```java
    public class Solution {
        private class GraphNode {
            String label = null;
            Map<String, Double> neighbors = null;
            public GraphNode(String label) {
                this.label = label;
                this.neighbors = new HashMap<>();
            }
        }
        // 先建graph，然后dfs, 不能用bfs，因为要沿途累乘weight, 跟踪path
        public double[] calcEquation(String[][] equations, double[] values, String[][] queries) {
            Map<String, GraphNode> graph = initGraph(equations, values);
            List<Double> res = new ArrayList<>();
            // dfs for each query
            for (String[] query : queries) {
                String s = query[0];
                String e = query[1];
                Set<String> visited = new HashSet<>();
                Double ans = dfs(graph, s, e, 1, visited);
                if (ans != null) res.add(ans);
                else res.add(-1.0);
            }
            // convert list to array
            double[] ret = new double[res.size()];
            for (int i = 0; i < ret.length; ++i) {
                ret[i] = res.get(i);
            }
            return ret;
        }
        
        // 把所有给定的equations存入graph
        // 例如：a / b = 2，则新建node a，neighbor b，weight = 2， node b，neighbor a，weight = 1/2
        private Map<String, GraphNode> initGraph(String[][] equations, double[] values) {
            Map<String, GraphNode> graph = new HashMap<>();
            for (int i = 0; i < equations.length; ++i) {
                String d1 = equations[i][0];
                String d2 = equations[i][1];
                double weight = values[i];
                // get node d1, d2, put neighbor and weight
                if (! graph.containsKey(d1)) graph.put(d1, new GraphNode(d1));
                if (! graph.containsKey(d2)) graph.put(d2, new GraphNode(d2));
                graph.get(d1).neighbors.put(d2, weight);
                graph.get(d2).neighbors.put(d1, 1 / weight);
            }
            return graph;
        }
        // dfs 来找到从start到end的路径，并返回沿途路径weight的乘积  
        private Double dfs(Map<String, GraphNode> graph, String start, String end, double product, Set<String> visited) {
            GraphNode node = graph.get(start);
            if (node == null) return null;
            if (start.equals(end)) return product;
            if (visited.contains(start)) return null;
            visited.add(start);
            for (String s : node.neighbors.keySet()) {
                double weight = node.neighbors.get(s);
                Double ans = dfs(graph, s, end, product * weight, visited);
                if (ans != null) return ans;
            }
            return null;
        }
    }
```