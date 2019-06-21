# Word Break II

_update Sep 16 2018, 11:05_

[LeetCode](https://leetcode.com/problems/word-break-ii/description/)

Given a non-empty string s and a dictionary wordDict containing a list of non-empty words, add spaces in s to construct a sentence where each word is a valid dictionary word. Return all such possible sentences.

**Note:**

The same word in the dictionary may be reused multiple times in the segmentation. You may assume the dictionary does not contain duplicate words.

**Example 1:**

```text
Input:
s = "catsanddog"
wordDict = ["cat", "cats", "and", "sand", "dog"]
Output:
[
  "cats and dog",
  "cat sand dog"
]
```

**Example 2:**

```text
Input:
s = "pineapplepenapple"
wordDict = ["apple", "pen", "applepen", "pine", "pineapple"]
Output:
[
  "pine apple pen apple",
  "pineapple pen apple",
  "pine applepen apple"
]
Explanation: Note that you are allowed to reuse a dictionary word.
```

**Example 3:**

```text
Input:
s = "catsandog"
wordDict = ["cats", "dog", "sand", "and", "cat"]
Output:
[]
```

## Basic Idea:

[Word Break I](https://willguo-private.gitbook.io/xiaozheng-algo/algorithm-problems/dynamic-programming/word-break#basic-idea) 求的是给定字符串能否被分割成字典中的元素，因为只求一个boolean，可以用DP的思路求解，对于一个string s，我们只要考虑能否将其分解为子问题 `s[0:k] 能被成功分割, 且s[k:]是dict中的一个word，k from 0 to end`。

而对于这道题目，要求的是所有可能的解，我们就需要使用DFS了。与之前dp的思路略有不同，这次我们每次从前面开始考虑，`s[0:k]` 为 dict 中的word，后面部分为下一个子问题。dfs function 的返回值为当前问题的所有可能的结果 `List<String>`，则对于一个子问题 string s，我们尝试用dict中的每个word来匹配s最前面的部分，然后调用dfs function计算其后面部分的所有可能解，再将prefix放在每个解的前面，返回即可。需要注意的是后面部分可能会被重复计算，所以我们需要用一个 `Map<Integer, List<String>>` 来存储已经计算过的右边部分的解。

* **时间复杂度分析：**

  如果不考虑优化，时间复杂度为 `O(n * 2^n)`，因为解集的大小可以是 `O(2^n)`,即怎么分都valid，共有n-1个空可以分割，总分割可能为`O(2^(n - 1))`，而每种情况生成解集的时间为`O(n)`.

### Java Code:

```java
class Solution {
    public List<String> wordBreak(String s, List<String> wordDict) {        
        Set<String> dict = new HashSet<>(wordDict);
        Map<Integer, List<String>> memory = new HashMap<>();
        List<String> res = dfs(s, 0, dict, memory);
        return res;
    }

    private List<String> dfs(String s, int start, Set<String> dict, Map<Integer, List<String>> memory) {
        if (memory.containsKey(start)) return memory.get(start);
        List<String> ret = new ArrayList<>();
        if (start == s.length()) {
            ret.add("");
            memory.put(start, ret);
            return ret;
        }

        for (int i = start; i < s.length(); ++i) {
            String prefix = s.substring(start, i + 1);
            if (dict.contains(prefix)) {
                List<String> rightPaths = dfs(s, i + 1, dict, memory);
                for (String rightPath : rightPaths) {
                    if (rightPath.length() == 0) {
                        ret.add(prefix);
                    } else {
                        ret.add(prefix + " " + rightPath);
                    }
                }
            }
        }
        memory.put(start, ret);
        return ret;
    }
}
```

