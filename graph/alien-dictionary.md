# Alien Dictionary
_update Sep 6 2018, 0:36_

---
There is a new alien language which uses the latin alphabet. However, the order among letters are unknown to you. You receive a list of non-empty words from the dictionary, where words are sorted lexicographically by the rules of this new language. Derive the order of letters in this language.

**Example 1:**

    Input:
    [
      "wrt",
      "wrf",
      "er",
      "ett",
      "rftt"
    ]

    Output: "wertf"

**Example 2:**

    Input:
    [
      "z",
      "x"
    ]

    Output: "zx"

**Example 3:**

    Input:
    [
      "z",
      "x",
      "z"
    ]

    Output: ""

    Explanation: The order is invalid, so return "".

**Note:**

* You may assume all letters are in lowercase.
* You may assume that if a is a prefix of b, then a must appear before b in the given dictionary.
* If the order is invalid, return an empty string.
* There may be multiple valid order of letters, return any one of them is fine.

### Basic Idea:
输入为一串按照字典顺序排序的字符串，要求根据给定顺序，输入一个符合条件的字符字典顺序序列，有可能不存在符合要求的顺序。

我们可以将这个问题抽象为图的问题，每个字符可以视为一个 GraphNode，每两个给定字符串之间的相互顺序可以抽象为图的边,例如 `abc, abe` 则可以推得一条边：`c->e`。之后我们可以利用这种关系生成 Graph，对所有的 node 进行topological sort，这样就可以求得解。需要注意，如果没有valid解，体现为图中有环， sort的时候检查环即可。检查的方法为如果当queue为空后，`res.size() != graph.size()`，则说明有环。

### Java Code:
```java
class Solution {
    private class GraphNode {
        char c;
        int indegree;
        List<GraphNode> neighbors;
        public GraphNode(char c) {
            this.c = c;
            this.indegree = 0;
            neighbors = new ArrayList<>();
        }
    }
    private Map<Character, GraphNode> graph;

    private void initGraph(String[] words) {
        graph = new HashMap<Character, GraphNode>();
        generateNodes(words);
        for (int i = 0; i < words.length - 1; ++i) {
            for (int j = i + 1; j < words.length; ++j) {
                generateEdges(words[i], words[j]);
            }
        }
    }

    private void generateNodes(String[] words) {
        for (String word : words) {
            for (char c : word.toCharArray()) {
                if (! graph.containsKey(c)) {
                    graph.put(c, new GraphNode(c));
                }
            }
        }
    }

    private void generateEdges(String small, String large) {
        int i = 0, j = 0;
        while (true) {
            if (i >= small.length() || j >= large.length()) break;
            else if (small.charAt(i) == large.charAt(j)) {
                i++;
                j++;
            } else {
                GraphNode smallNode = graph.get(small.charAt(i));
                GraphNode largeNode = graph.get(large.charAt(j));
                smallNode.neighbors.add(largeNode);
                largeNode.indegree++;
                break;
            }
        }
    }

    private List<GraphNode> topologicalSort() {
        Deque<GraphNode> queue = new ArrayDeque<>();
        List<GraphNode> ret = new ArrayList<>();
        for (Map.Entry<Character, GraphNode> entry : graph.entrySet()) {
            if (entry.getValue().indegree == 0) {
                queue.offerLast(entry.getValue());
            }
        }
        while (! queue.isEmpty()) {
            GraphNode currNode = queue.pollFirst();
            ret.add(currNode);
            for (GraphNode neighbor : currNode.neighbors) {
                neighbor.indegree--;
                if (neighbor.indegree == 0) {
                    queue.offerLast(neighbor);
                }
            }
        }
        // check if there is circle in the graph
        if (ret.size() == graph.size()) return ret;
        else return null;
    }

    public String alienOrder(String[] words) {
        initGraph(words);
        List<GraphNode> sortedNodes = topologicalSort();
        if (sortedNodes == null) return "";
        StringBuilder sb = new StringBuilder();
        for (GraphNode node : sortedNodes) {
            sb.append(node.c);
        }
        return sb.toString();
    }
}
```
