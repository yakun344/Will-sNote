# Word Break

_update Sep 7 2018, 2:23_

[LeetCode](https://leetcode.com/problems/word-break/description/)

Given a non-empty string s and a dictionary wordDict containing a list of non-empty words, determine if s can be segmented into a space-separated sequence of one or more dictionary words.

**Note:**

The same word in the dictionary may be reused multiple times in the segmentation.  
You may assume the dictionary does not contain duplicate words.

**Example 1:**

```text
Input: s = "leetcode", wordDict = ["leet", "code"]
Output: true
Explanation: Return true because "leetcode" can be segmented as "leet code".
```

**Example 2:**

```text
Input: s = "applepenapple", wordDict = ["apple", "pen"]
Output: true
Explanation: Return true because "applepenapple" can be segmented as "apple pen apple".
         Note that you are allowed to reuse a dictionary word.
```

**Example 3:**

```text
Input: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
Output: false
```

## Basic Idea:

看给定字符串能否被分割成仅由给定dict中words中word组成的段落。

* **思路1：DFS + Memory**

  从左到右进行匹配，匹配成功之后继续对右边剩下部分进行匹配。但是我们注意到DFS的过程中会遇到很多重复的情况。如果直接进行DFS，每层最多可以有 `s.length` 个分支，最多可以有 `s.length` 层，所以总时间复杂度为 `O(N^N)`。

  而如果用 Memory Table 进行优化，记录相同右边部分能否成功被break为dict中的部分的集合，可以把时间复杂度优化为 `O(N^2)`. 分析见下面DP的思路。

  * **Java Code：**

    ```java
    class Solution {
    private Set<String> dict = new HashSet<>();
    private Map<Integer, Boolean> memory = new HashMap<>();

    private int maxWordLen = 0;
    public boolean wordBreak(String s, List<String> wordDict) {
        for (String t : wordDict) {
            dict.add(t);
            maxWordLen = Math.max(maxWordLen, t.length());
        }
        return dfs(s, 0);
    }

    private boolean dfs(String s, int start) {
        if (start == s.length()) return true;
        if (memory.containsKey(start)) return memory.get(start);
        for (int i = s.length(); i > start; --i) {
            if (dict.contains(s.substring(start, i))) {
                if (dfs(s, i)) {
                    memory.put(start, true);
                    return true;
                }
            }
        }
        memory.put(start, false);
        return false;
    }
    }
    ```

* **思路2： BFS + visited**

  利用BFS可以最快速度找到终点，利用visited set来去重，搜索中发现已经完成就可以返回true。

* **思路3：DP**

  利用DP的思路考虑：

  ```c
    定义问题 dp[i] 为 s[0:i+1] 是否可以被成功分割，该问题可以被视为由子问题组成：
      dp[k] && s[k+1:i+1] for k from 0 to i

      换句话说，就是尝试将 s[0:i+1] 分成两部分，使得前一部分可以被分割，后部分是
      dict中的一个单词。满足这个条件，dp[i] 就是 true。时间复杂度为 O(N^2) ，
      因为对于每个i，我们需要 O(i) 的时间判断其能否被k分成满足要求的两部分。
  ```

  * **Java Code：**

    ```java
    class Solution {
        public boolean wordBreak(String s, List<String> wordDict) {
            Set<String> dict = new HashSet<>();
            for (String word : wordDict) dict.add(word);

            boolean[] dp = new boolean[s.length() + 1];
            dp[0] = true;
            for (int i = 0; i < s.length(); ++i) {
                for (int j = i; j >= 0; --j) {
                    if (dp[j]) {
                        if (dict.contains(s.substring(j, i + 1))) {
                            dp[i + 1] = true;
                            break;
                        }
                    }
                }
            }
            return dp[dp.length - 1];
        }
    }
    ```

