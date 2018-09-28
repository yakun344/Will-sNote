# Reconstruct Itinerary

---

_update Sep 27 2018, 23:29_

[LeetCode](https://leetcode.com/problems/reconstruct-itinerary/description/)


Given a list of airline tickets represented by pairs of departure and arrival airports `[from, to]`, reconstruct the itinerary in order. All of the tickets belong to a man who departs from JFK. Thus, the itinerary must begin with JFK.

**Note:**

If there are multiple valid itineraries, you should return the itinerary that has the smallest lexical order when read as a single string. For example, the itinerary `["JFK", "LGA"]` has a smaller lexical order than `["JFK", "LGB"]`.
All airports are represented by three capital letters (IATA code).
You may assume all tickets form at least one valid itinerary.

**Example 1:**

    Input: [["MUC", "LHR"], ["JFK", "MUC"], ["SFO", "SJC"], ["LHR", "SFO"]]
    Output: ["JFK", "MUC", "LHR", "SFO", "SJC"]

**Example 2:**

    Input: [["JFK","SFO"],["JFK","ATL"],["SFO","ATL"],["ATL","JFK"],["ATL","SFO"]]
    Output: ["JFK","ATL","JFK","SFO","ATL","SFO"]

**Explanation**: Another possible reconstruction is `["JFK","SFO","ATL","JFK","ATL","SFO"]`.But it is larger in lexical order.

<br/>          

### Basic Idea:
基本思路就是先建图，每个地点作为一个node，下一站的目标作为neighbor，将每个node的边存在priorityQueue中，这样就保证了字典顺序。然后从“JFK”开始进行dfs，每次发现一个neighbor之后将这个边删除，避免有环的重复访问。

#### Java Code:
```java
class Solution {
    public List<String> findItinerary(String[][] tickets) {
        Map<String, PriorityQueue<String>> graph = new HashMap<>();
        for (String[] edge : tickets) {
            if (! graph.containsKey(edge[0])) graph.put(edge[0], new PriorityQueue<>());
            graph.get(edge[0]).offer(edge[1]);
        }

        List<String> res = new ArrayList<>();
        dfs(graph, "JFK", res);
        Collections.reverse(res);
        return res;
    }

    private void dfs(Map<String, PriorityQueue<String>> graph, String node, List<String> res) {
        PriorityQueue<String> neighbors = graph.get(node);
        while (neighbors != null && ! neighbors.isEmpty()) {
            String next = neighbors.poll();
            dfs(graph, next, res);
        }
        res.add(node);
    }
}
```
