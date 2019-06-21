# Letter Case Permutation \(Easy Facebook\)

_update 2018-05-16 11:15:08_

[LeetCode](https://leetcode.com/problems/letter-case-permutation/description/)

Given a string S, we can transform every letter individually to be lowercase or uppercase to create another string. Return a list of all possible strings we could create.

**Examples:**

```text
Input: S = "a1b2"
Output: ["a1b2", "a1B2", "A1b2", "A1B2"]

Input: S = "3z4"
Output: ["3z4", "3Z4"]

Input: S = "12345"
Output: ["12345"]
```

**Note:**

* S will be a string with length at most 12.
* S will consist only of letters or digits.

## Basic Idea:

典型的dfs题目，当遇到大写或者小写字母的时候，分别向转换和维持原状两条支路dfs，从左到右逐位处理。

* **C++ Code:**

  ```cpp
    class Solution {
        void dfs(vector<string>& res, string S, string path, int pos) {
            if (pos == S.size()) {
                res.push_back(path);
                return;
            }
            if (S[pos] >= 'a' && S[pos] <= 'z') dfs(res, S, path + char(S[pos] - 'a' + 'A'), pos + 1);
            else if (S[pos] >= 'A' && S[pos] <= 'Z') dfs(res, S, path + char(S[pos] - 'A' + 'a'), pos + 1);
            dfs(res, S, path + S[pos], pos + 1);
        }
    public:
        vector<string> letterCasePermutation(string S) {
            vector<string> res;
            dfs(res, S, "", 0);
            return res;
        }
    };
  ```

