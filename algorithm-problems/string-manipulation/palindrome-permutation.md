# Palindrome Permutation

_update Sep 10,2017 21:46_

[LeetCode](https://leetcode.com/problems/palindrome-permutation/description/)

Given a string, determine if a permutation of the string could form a palindrome.

**For example,**  
"code" -&gt; False, "aab" -&gt; True, "carerac" -&gt; True.

## Basic Idea:

**思路 1：Brute Force DFS**  
“The more you think you know, the less you will see.”  
因为一开始没看到这是一道easy，所以一上来看到permutation就写了个dfs，果然TLE了。

Java Code:

```java
    class Solution {
        public boolean canPermutePalindrome(String s) {
            if (s.length() < 2) return true;
            return helper(s, 0, "", new boolean[s.length()]);
        }
        private boolean helper(String s, int pos, String path, boolean[] used) {
            if (pos == s.length()) return isPalindrome(path);
            for (int i = 0; i < s.length(); ++i) {
                if (used[i]) continue;
                used[i] = true;
                if (helper(s, pos + 1, path + s.charAt(i), used)) return true;
                used[i] = false;
            }
            return false;
        }
        private boolean isPalindrome(String s) {
            int left = 0, right = s.length() - 1;
            while (left <= right) {
                if (s.charAt(left) == s.charAt(right)) {
                    left++;
                    right--;
                } else {
                    return false;
                }
            }
            return true;
        }
    }
```

**思路 2， HashMap**  
这才是正常的思路。因为可以组成回文序列只需要string中的每个字符至多有一种是单数个数的就可以了。

```python
    class Solution(object):
        def canPermutePalindrome(self, s):
            """
            :type s: str
            :rtype: bool
            """
            counter = collections.defaultdict(int)
            for c in s:
                counter[c] += 1
            return len([counter[c] for c in counter if counter[c] % 2 != 0]) <= 1
```

_update May 7,2018 21:05_

## C++ Code

仍然是记录每个字母出现个数，然后计数单数字母个数，如果有不止一个字母是单数，就返回false。这里进行另外一个优化，就是使用bitset, 每一个字母只对应 1 bit，偶数会抵消为0，留下是 1 的都是 odd。

```cpp
    class Solution {
    public:
        bool canPermutePalindrome(string s) {
            bitset<256> count;
            for (char c : s) {
                count.flip(c);
            }
            int odd = 0;
            for (int i = 0; i < 256; ++i) {
                if (count[i]) {
                    if (++odd > 1) return false;
                }
            }
            return true;
        }
    };
```

