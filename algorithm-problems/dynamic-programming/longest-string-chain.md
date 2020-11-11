# 1048. Longest String Chain

_update Nov 10, 2020_

---

[Leetcode](https://leetcode.com/problems/longest-string-chain/)

Given a list of words, each word consists of English lowercase letters.

Let's say word1 is a predecessor of word2 if and only if we can add exactly one letter anywhere in word1 to make it equal to word2.  For example, "abc" is a predecessor of "abac".

A word chain is a sequence of words `[word_1, word_2, ..., word_k]` with `k >= 1`, where word_1 is a predecessor of word_2, word_2 is a predecessor of word_3, and so on.

Return the longest possible length of a word chain with words chosen from the given list of words.


**Example 1:**
```
Input: ["a","b","ba","bca","bda","bdca"]
Output: 4
Explanation: one of the longest word chain is "a","ba","bda","bdca".
```

**Note:**

```
1. 1 <= words.length <= 1000
2. 1 <= words[i].length <= 16
3. words[i] only consists of English lowercase letters.
```

## Basic Idea:

1. 最基本的思路是从每个词出发，每次生成它添加一个字母能生成的所有单词，然后检查他们是否在给定的数组中，BFS，但我们发现这样做是有很多重复计算的，例如 A->B->C, 当我们计算了从B出发的最大长度，当我们从A出发发现B时，就可以直接利用之前的结果。但事实上这样仍然是比较慢的，因为每次我们都要生成所有的添加一个字母能组成的string，这增加了非常多不必要的工作。
2. 我们可以从比较长的string开始，每次减少一个字母，然后判断剩下的string是否在给定的数组中，同样我们可以由短到长进行cache的操作，这样就比解法 1 快上许多。
3. 思路2 也可以使用dp的方法来做

## Java Code
#### Solution 1
```java
class Solution {
    private Set<String> set = new HashSet<>();
    private Map<String, Integer> cache = new HashMap<>();
    
    public int longestStrChain(String[] words) {
        Arrays.sort(words, (a, b) -> Integer.compare(b.length(), a.length()));
        for (String s : words) set.add(s);
        
        int ret = 0;
        for (int i = 0; i < words.length; ++i) {
            ret = Math.max(ret, bfs(words[i]));
        }
        return ret;
    }
    
    private int bfs(String start) {
        Deque<String> queue = new ArrayDeque<>();
        queue.offerFirst(start);
        int ret = 0;
        int level = 0;
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; ++i) {
                String curr = queue.pollLast();
                if (cache.containsKey(curr)) {
                    ret = Math.max(ret, level + cache.get(curr));
                } else {
                    List<String> neighbors = getNeighbors(curr);
                    for (String neighbor : neighbors) {
                        if (set.contains(neighbor)) {
                            queue.offerFirst(neighbor);
                        }
                    }
                }
            }
            level++;
        }
        ret = Math.max(ret, level);
        cache.put(start, ret);
        return ret;
    }
    
    private List<String> getNeighbors(String base) {
        List<String> res = new ArrayList<>();
        for (char c = 'a'; c <= 'z'; ++c) {
            for (int i = 0; i < base.length() + 1; ++i) {
                if (i == 0) {
                    res.add(c + base);
                } else {
                    res.add(base.substring(0, i) + c + base.substring(i));
                }
            }
        }
        return res;
    }
}
```

#### Solution 2 (faster, dfs, delete one char at a time)
```java
class Solution {
    private Map<String, Integer> cache = new HashMap<>();
    private Set<String> wordSet = new HashSet<>();
    
    public int longestStrChain(String[] words) {
        for (String word : words) wordSet.add(word);
        Arrays.sort(words, (a, b) -> Integer.compare(b.length(), a.length()));
        int ret = 0;
        for (String word : words) {
            ret = Math.max(ret, dfs(word));
        }
        return ret;
    }
    
    private int dfs(String start) {
        if (start.length() == 0 || !wordSet.contains(start)) return 0;
        if (cache.containsKey(start)) return cache.get(start);
        int maxDepth = 0;
        for (int i = 0; i < start.length(); ++i) {
            String next;
            if (i == 0) next = start.substring(1);
            else next = start.substring(0, i) + start.substring(i + 1);
            maxDepth = Math.max(maxDepth, dfs(next));
        }
        cache.put(start, maxDepth + 1);
        return maxDepth + 1;
    }
}
```

#### Solution 3 (DP)
按照长度从小到大排序，然后从短的string开始依次向右，这样前面的结果一定已经被算出。
```java
class Solution {
    
    public int longestStrChain(String[] words) {
        Set<String> wordSet = new HashSet<>();
        for (String word : words) wordSet.add(word);

        int ret = 0;
        Map<String, Integer> cache = new HashMap<>();
        Arrays.sort(words, (a, b) -> Integer.compare(a.length(), b.length()));
        for (String word : words) {
            int maxLen = 0;
            for (int i = 0; i < word.length(); ++i) {
                String next;
                if (i == 0) next = word.substring(i + 1);
                else next = word.substring(0, i) + word.substring(i + 1);
                if (wordSet.contains(next)) {
                    maxLen = Math.max(maxLen, cache.get(next));
                }
            }
            ret = Math.max(ret, maxLen + 1);
            cache.put(word, maxLen + 1);
        }
        return ret;
    }
}
```
