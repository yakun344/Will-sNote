# Alien Dictionary

_update Sep 6 2018, 0:36_

There is a new alien language which uses the latin alphabet. However, the order among letters are unknown to you. You receive a list of non-empty words from the dictionary, where words are sorted lexicographically by the rules of this new language. Derive the order of letters in this language.

**Example 1:**

```text
Input:
[
  "wrt",
  "wrf",
  "er",
  "ett",
  "rftt"
]

Output: "wertf"
```

**Example 2:**

```text
Input:
[
  "z",
  "x"
]

Output: "zx"
```

**Example 3:**

```text
Input:
[
  "z",
  "x",
  "z"
]

Output: ""

Explanation: The order is invalid, so return "".
```

**Note:**

* You may assume all letters are in lowercase.
* You may assume that if a is a prefix of b, then a must appear before b in the given dictionary.
* If the order is invalid, return an empty string.
* There may be multiple valid order of letters, return any one of them is fine.

## Basic Idea:

输入为一串按照字典顺序排序的字符串，要求根据给定顺序，输入一个符合条件的字符字典顺序序列，有可能不存在符合要求的顺序。

我们可以将这个问题抽象为图的问题，每个字符可以视为一个 GraphNode，每两个给定字符串之间的相互顺序可以抽象为图的边,例如 `abc, abe` 则可以推得一条边：`c->e`。之后我们可以利用这种关系生成 Graph，对所有的 node 进行topological sort，这样就可以求得解。需要注意，如果没有valid解，体现为图中有环， sort的时候检查环即可。检查的方法为如果当queue为空后，`res.size() != graph.size()`，则说明有环。

## Java Code:

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

## Update: Without Node class

之前的实现没有考虑重复边的问题，导致可能会有同样的边被多次加入，这样会增加时间复杂度。另外，其实也可以不另外定义Node class，可以用一个 `Map<Character, Set<Character>` 和一个 `int[] indegrees` 来表示graph和indegree map。

### Java Code:

```java
class Solution {
    private Map<Character, Set<Character>> graph;
    private int[] indegrees; // 长度26

    public String alienOrder(String[] words) {
        graph = new HashMap<>();
        indegrees = new int[26];
        Arrays.fill(indegrees, -1);
        getAllNode(words);
        initGraph(words);
        return topologicalSort();
    }

    private void getAllNode(String[] words) {
        for (String word : words) {
            for (int i = 0; i < word.length(); ++i) {
                if (! graph.containsKey(word.charAt(i))) {
                    graph.put(word.charAt(i), new HashSet<>());
                    indegrees[word.charAt(i) - 'a'] = 0; // 初始化为0表示出现过该char
                }
            }
        }
    }

    private void initGraph(String[] words) {
        for (int i = 0; i < words.length - 1; ++i) {
            String prev = words[i];
            String next = words[i + 1];
            for (int j = 0; j < prev.length() && j < next.length(); ++j) {
                char u = prev.charAt(j);
                char v = next.charAt(j);
                if (u != v) {
                    // 说明产生了边 u-v
                    if (graph.get(u).add(v)) {
                        // 如果之前没有考虑过相同的边，则 v 的入度 +1
                        indegrees[v - 'a']++;
                    }
                    break;
                }
            }
        }
    }

    // 直接返回sort之后的char的string，即所求的解
    private String topologicalSort() {
        StringBuilder sb = new StringBuilder();
        Deque<Character> queue = new ArrayDeque<>();
        for (int i = 0; i < 26; ++i) {
            if (indegrees[i] == 0) queue.offerLast((char)(i + 'a'));
        }
        while (! queue.isEmpty()) {
            char c = queue.pollFirst();
            sb.append(c);
            for (char neighbor : graph.get(c)) {
                // 将c的所有neighbor入度 -1
                if (--indegrees[neighbor - 'a'] == 0) {
                    queue.offerLast(neighbor);
                }
            }
        }
        if (sb.length() != graph.size()) return "";
        else return sb.toString();
    }
}
```

