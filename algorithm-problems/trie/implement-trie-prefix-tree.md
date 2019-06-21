# Implement Trie

_update Sep 27 2018, 23:49_

[LeetCode](https://leetcode.com/problems/implement-trie-prefix-tree/description/)

Implement a trie with insert, search, and startsWith methods.

**Example:**

```text
Trie trie = new Trie();

trie.insert("apple");
trie.search("apple");   // returns true
trie.search("app");     // returns false
trie.startsWith("app"); // returns true
trie.insert("app");   
trie.search("app");     // returns true
```

**Note:**

You may assume that all inputs are consist of lowercase letters a-z. All inputs are guaranteed to be non-empty strings.

## Basic Idea:

基本思路就是定义好trie node，每个node包含一个 `Node[26]`；

### Java Code:

```java
class Trie {
    private class Node {
        boolean end;
        Node[] child;
        public Node() {
            end = false;
            child = new Node[26];
        }
    }

    private Node root;

    /** Initialize your data structure here. */
    public Trie() {
        root = new Node();
    }

    /** Inserts a word into the trie. */
    public void insert(String word) {
        Node curr = root;
        for (char c : word.toCharArray()) {
            if (curr.child[c - 'a'] == null) {
                curr.child[c - 'a'] = new Node();
            }
            curr = curr.child[c - 'a'];
        }
        curr.end = true;
    }

    /** Returns if the word is in the trie. */
    public boolean search(String word) {
        Node curr = root;
        for (char c : word.toCharArray()) {
            if (curr.child[c - 'a'] == null) return false;
            curr = curr.child[c - 'a'];
        }
        return curr.end;
    }

    /** Returns if there is any word in the trie that starts with the given prefix. */
    public boolean startsWith(String prefix) {
        Node curr = root;
        for (char c : prefix.toCharArray()) {
            if (curr.child[c - 'a'] == null) return false;
            curr = curr.child[c - 'a'];
        }
        return true;
    }
}
```

