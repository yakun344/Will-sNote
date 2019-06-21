# Letter Combinations of a Phone Number \(Medium\)

_update Feb 19, 2018 16:51_

[LeetCode](https://leetcode.com/problems/letter-combinations-of-a-phone-number/description/)

Given a digit string, return all possible letter combinations that the number could represent.

A mapping of digit to letters \(just like on the telephone buttons\) is given below.

![](http://upload.wikimedia.org/wikipedia/commons/thumb/7/73/Telephone-keypad2.svg/200px-Telephone-keypad2.svg.png)

```text
Input:Digit string "23"
Output: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
```

**Note:**

Although the above answer is in lexicographical order, your answer could be in any order you want.

## Basic Idea:

* **思路 1： DFS**

  使用dfs求解，整个递归分为 `len(digits)` 层，每层考虑一个 input 的数字，每层有当前层数次对应字母个数个分支，时间复杂度大约为 `O(每个数字对应字母个数 ^ len(digits))`，空间复杂度如果不考虑解集占用空间，为递归栈所占，`O(len(digits))`;

  * **Java Code：**

    ```java
        class Solution {
            public List<String> letterCombinations(String digits) {
                String[] arr = new String[]{"0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
                List<String> res = new ArrayList<>();
                if (digits == null || digits.length() < 1) return res;
                dfs(res, 0, digits, arr, new StringBuilder());
                return res;
            }

            private void dfs(List<String> res, int pos, String digits, String[] arr, StringBuilder path) {
                if (pos == digits.length()) {
                    res.add(path.toString());
                    return;
                }
                int digit = digits.charAt(pos) - '0';
                String chars = arr[digit];
                for (int i = 0; i < chars.length(); ++i) {
                    path.append(chars.charAt(i));
                    dfs(res, pos + 1, digits, arr, path);
                    path.deleteCharAt(path.length() - 1);
                }
            }
        }
    ```

* **思路 2：BFS，iterative**

  利用一个 queue，每次讲之前的生成的path扩展一层，例如input为 `23`, 对应字母分别为 `abc, def`。则我们第一层生成 `a,b,c` ，第二层分别针对候选的 `d,e,f` 进行扩展，生成最后结果：`ad,ae,af,bd,be,bf,cd,ce,cf`；

  * **Java Code:**

    ```java
    class Solution {
        public List<String> letterCombinations(String digits) {
            List<String> res = new ArrayList<>();
            String[] arr = {"0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
            if (digits == null || digits.length() < 1) return res;
            Deque<String> queue = new ArrayDeque<>();
            queue.offerFirst("");
            for (int i = 0; i < digits.length(); ++i) {
                int size = queue.size();
                String chars = arr[digits.charAt(i) - '0'];
                for (int n = 0; n < size; ++n) {
                    String node = queue.pollLast();
                    for (char c : chars.toCharArray()) {
                        queue.offerFirst(node + c);
                    }
                }
            }
            res.addAll(queue);
            return res;
        }
    }
    ```

