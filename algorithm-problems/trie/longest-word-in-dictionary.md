# Longest Word in Dictionary

_update Jan 25,2018 15:08_

[LeetCode](https://leetcode.com/problems/longest-word-in-dictionary/description/)

Given a list of strings words representing an English Dictionary, find the longest word in words that can be built one character at a time by other words in words. If there is more than one possible answer, return the longest word with the smallest lexicographical order.

If there is no answer, return the empty string.

**Example 1:**

```text
Input:
words = ["w","wo","wor","worl", "world"]
Output: "world"
Explanation:
The word "world" can be built one character at a time by "w", "wo", "wor", and "worl".
```

**Example 2:**

```text
Input:
words = ["a", "banana", "app", "appl", "ap", "apply", "apple"]
Output: "apple"
Explanation:
Both "apply" and "apple" can be built from other words in the dictionary. However, "apple" is lexicographically smaller than "apply".
```

**Note:**

1. All the strings in the input will only contain lowercase letters.
2. The length of words will be in the range \[1, 1000\].
3. The length of words\[i\] will be in the range \[1, 30\].

## Basic Idea:

* **方法 1 （Trie + bfs）：**

    比较直观的方法就是建一个trie，然后从长度为 1 的 26 个字母开始搜索，逐层进行 bfs，例如如果 input 中有 `[a, b, ap, app, ba, ban, bana]`，第一层搜索会找到 `[a, b]`, 第二层会找到 `[ap, ba]`, 第三层 `[app, ban]`, 第四层 `[bana]`, 则 “bana” 最长，输出 “bana”；  
    这种方法时间复杂度略高，因为每次要检查26个字母，总时间复杂度 `O(26^n)`，n 为解的长度\(worst case, 每层每个node都有26个结果generated\)；

  * **C++ Code：**

    ```cpp
    class Solution {
        struct TrieNode {
            string word;
            TrieNode* child[26] = {nullptr};
        };

        string dfs(TrieNode* root) {
            string ret = root->word;
            for (int i = 0; i < 26; ++i) {
                if (root->child[i] && root->child[i]->word != "") {
                    string t = dfs(root->child[i]);
                    ret = t.size() > ret.size() ? t : ret;
                }
            }
            return ret;
        }

        TrieNode* constructTrie(vector<string>& words) {
            TrieNode* root = new TrieNode();
            for (string word : words) {
                TrieNode* curr = root;
                for (char c : word) {
                    if (curr->child[c - 'a'] == nullptr) {
                        curr->child[c - 'a'] = new TrieNode();
                    }
                    curr = curr->child[c - 'a'];
                }
                curr->word = word;
            }
            return root;
        }
    public:
        string longestWord(vector<string>& words) {
            TrieNode* root = constructTrie(words);
            return dfs(root);
        }
    };
    ```

* **方法 2 （sort）：**

    先对 input list 排序，这样可以保证短的都在前面，然后维持一个 `Set<String>` ，遍历input list，如果遇到长度为 1 的 string s，直接加入 set，否则检查 `s[:-1] 是否在 set 中， 如果在则将其加入 set`，于此同时，维持全局变量记录s长度，以及全局变量记录最长的字典顺序小的 string 作为 ret 进行更新;  
   这种方法时间复杂度为 `O(NlogN)`

  * **Java Code:**

    ```java
    class Solution {
    public String longestWord(String[] words) {
        Arrays.sort(words);
        Set<String> set = new HashSet<>();
        int maxLen = 0;
        String ret = "";
        for (String s : words) {
            // 长度为 1 或者 s[:-1] in set, 将其放入set，更新全局变量
            if (s.length() == 1 || set.contains(s.substring(0, s.length() - 1))) {
                set.add(s);
                if (s.length() > maxLen) {
                    maxLen = s.length();
                    ret = s;
                }
            }
        }
        return ret;
    }
    }
    ```

  * **C++ Code**

    ```cpp
    class Solution {
    public:
        string longestWord(vector<string>& words) {
            sort(words.begin(), words.end(), [](string s1, string s2){ return s1.size() < s2.size(); });
            unordered_set<string> _set;
            string ret;
            for (string s : words) {
                if (s.size() == 1 || _set.count(s.substr(0, s.size() - 1))) {
                    _set.insert(s);
                    if (s.size() > ret.size() || s.size() == ret.size() && s < ret) {
                        ret = s;
                    }
                }
            }
            return ret;
        }
    };
    ```

