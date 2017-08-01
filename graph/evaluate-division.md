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
    // 正统方法
    public class Solution {
        // 先建graph，然后dfs, 不能用bfs，因为要沿途累乘weight, 跟踪path
        // 图的形式是adjacent list，Map<String, Map<String, Double>>, adj list 是一个map，因为每个neighbor对应一个weight
        public double[] calcEquation(String[][] equations, double[] values, String[][] queries) {
            List<Double> res = new ArrayList<>();
            // init graph
            Map<String, Map<String, Double>> Graph = initGraph(equations, values);
            // 对每个query，dfs graph，从第一个开始找第二个，沿途乘积就是解
            for (String[] query : queries) {
                String start = query[0];
                String target = query[1];
                Set<String> visited = new HashSet<>();
                Double ans = dfs(Graph, start, target, visited, 1);
                if (ans != null) res.add(ans);
                else res.add(-1.0);
            }    
            // convert res list to array
            double[] ret = new double[res.size()];
            for (int i = 0; i < ret.length; ++i) ret[i] = res.get(i);
            return ret;
        }
        private Double dfs(Map<String, Map<String, Double>> Graph, 
                           String start, String target, Set<String> visited, double product) {
            if ((! Graph.containsKey(start)) || (! Graph.containsKey(target))) return null;
            if (start.equals(target)) return product;
            if (visited.contains(start)) return null;
            visited.add(start);
            Map<String, Double> neighbors = Graph.get(start);
            for (String neighbor : neighbors.keySet()) {
                Double weight = neighbors.get(neighbor);
                Double ans = dfs(Graph, neighbor, target, visited, product * weight);
                if (ans != null) return ans;
            }
            return null;
        }
        
        private Map<String, Map<String, Double>> initGraph(String[][] equations, double[] values) {
            Map<String, Map<String, Double>> Graph = new HashMap<>();
            for (int i = 0; i < equations.length; ++i) {
                String d1 = equations[i][0];
                String d2 = equations[i][1];
                double weight = values[i];
                if (! Graph.containsKey(d1)) Graph.put(d1, new HashMap<String, Double>());
                if (! Graph.containsKey(d2)) Graph.put(d2, new HashMap<String, Double>());
                Graph.get(d1).put(d2, weight);
                Graph.get(d2).put(d1, 1 / weight);
            }
            return Graph;
        }
    }
```

#### Python Code:
```python
    class Solution(object):
        def calcEquation(self, equations, values, queries):
            """
            :type equations: List[List[str]]
            :type values: List[float]
            :type queries: List[List[str]]
            :rtype: List[float]
            """
            Graph = self.initGraph(equations, values)
            res = []
            # bfs
            for query in queries:
                start, target = query[0], query[1]
                visited = set()
                ans = self.dfs(Graph, start, target, visited, 1.0)
                if ans is not None: res.append(ans)
                else: res.append(-1.0)
            return res
                
            
        # dict<str, dict<str, double>> Graph, str start, str target, 
        # set<str> visited, double product
        def dfs(self, Graph, start, target, visited, product):
            if start not in Graph: return None
            if start == target: return product
            if start in visited: return None
            visited.add(start)
            neighbors = Graph[start]
            for neighbor in neighbors:
                weight = neighbors[neighbor]
                ans = self.dfs(Graph, neighbor, target, visited, product * weight)
                if ans is not None: return ans
            return None
        
        
        # init graph, dict(str, dict(str, double)).
        def initGraph(self, equations, values):
            Graph = {}
            i = 0
            for equation in equations:
                d1, d2, ans = equation[0], equation[1], values[i]
                i += 1
                if d1 not in Graph: Graph[d1] = {}
                if d2 not in Graph: Graph[d2] = {}
                Graph[d1][d2] = ans
                Graph[d2][d1] = 1 / ans
            return Graph
```