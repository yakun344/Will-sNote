## Palindrome Partitioning
_update Jul 20, 2017 17:54_

---
[lintcode](http://www.lintcode.com/en/problem/palindrome-partitioning/)

Given a string s, partition s such that every substring of the partition is a palindrome.

Return all possible palindrome partitioning of s.

**Example**

      Given s = "aab", return:
      
      [
        ["aa","b"],
        ["a","a","b"]
      ]
      
#### Basic Idea:
首先我们需要求出所有的partition的方案，这是一个combination的问题，具体的，是一个求all subsets的问题。我们可以把每个可以partition的位置作为关注点，求他们所有的subset，然后对于每个我们考虑当前partition是否满足回文的条件。如果一条路径上所有的partition都满足直到最后，我们把它加入res。

#### Java Code：
基本版本
```java
    public class Solution {
        /**
         * @param s: A string
         * @return: A list of lists of string
         */
        
        // 把问题当做一个subset的问题，考虑所有的分割点位置
        public List<List<String>> partition(String s) {
            List<List<String>> res = new ArrayList<>();
            if (s.length() == 0) {
                res.add(new ArrayList<String>());
                return res;
            }
            helper(s, new ArrayList<String>(), 0, res);
            return res;
        }
        // 考虑从以pos字母开始的substring所有的分割位置，把符合要求的加入path，如果
        // 全部符合要求，就把path加入res，return
        private void helper(String str,
                            List<String> path,
                            int pos,
                            List<List<String>> res) {
            if (pos == str.length()) {
                res.add(new ArrayList<String>(path));
                return;
            }
            for (int i = pos; i < str.length(); ++i) {
                String substring = str.substring(pos, i + 1);
                if (! isPalindrome(substring)) continue;
                path.add(substring);
                helper(str, path, i + 1, res);
                path.remove(path.size() - 1);
            }
        }
        private boolean isPalindrome(String str) {
            for (int i = 0; i < str.length() / 2; ++i) {
                if (str.charAt(i) != str.charAt(str.length() - 1 - i)) {
                    return false;
                }
            }
            return true;
        }
    }
```

优化版本
```java
    public class Solution {
        /**
         * @param s: A string
         * @return: A list of lists of string
         */

        // 优化版本
        // 1. 把检测出不符合的substring的index pair存入一个二维数组中（len(str) * len(str)）
        //    加快检测是否回文的速度
        // 2. 把传递、存储substring的方法改为首尾index
        
        int[][] table;
        
        public List<List<String>> partition(String s) {
            List<List<String>> res = new ArrayList<>();
            if (s.length() == 0) {
                res.add(new ArrayList<String>());
                return res;
            }
            table = new int[s.length()][s.length()];
            helper(s, 0, new ArrayList<int[]>(), res);
            return res;
        }
        private void helper(String str, 
                            int pos,
                            List<int[]> path,
                            List<List<String>> res) {
            if (pos == str.length()) {
                List<String> partition = new ArrayList<>();
                for (int[] index : path) {
                    partition.add(str.substring(index[0], index[1]));
                }
                res.add(partition);
                return;
            }          
            for (int i = pos; i < str.length(); ++i) {
                if (! isPalindrome(str, pos, i)) continue;
                path.add(new int[]{pos, i + 1}); // +1是因为substring函数
                helper(str, i + 1, path, res);
                path.remove(path.size() - 1);
            }
        }
        private boolean isPalindrome(String str, int start, int end) {
            // 如果substring(start, end(include))是，则存为1， 不是则存为-1
            if (table[start][end] == 1) return true;
            if (table[start][end] == -1) return false;
            int p = start, r = end;
            while (p < r) {
                if (str.charAt(p++) != str.charAt(r--)) {
                    table[start][end] = -1;
                    return false;
                }
            }
            table[start][end] = 1;
            return true;
        }
    }
```