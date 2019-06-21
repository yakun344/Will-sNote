# Word Pattern

_update Jan 25,2018 17:13_

[LeetCode](https://leetcode.com/problems/word-pattern/description/)

Given a pattern and a string str, find if str follows the same pattern.

Here follow means a full match, such that there is a bijection between a letter in pattern and a non-empty word in str.

**Examples:**

```text
pattern = "abba", str = "dog cat cat dog" should return true.
pattern = "abba", str = "dog cat cat fish" should return false.
pattern = "aaaa", str = "dog cat cat dog" should return false.
pattern = "abba", str = "dog dog dog dog" should return false.
```

**Notes:**

You may assume pattern contains only lowercase letters, and str contains lowercase letters separated by a single space.

## Basic Idea:

使用 HashMap，但是注意要对 `<pattern, str>, <str, pattern>` 进行双向map，因为有可能出现两个 pattern 对应着同样一个string的情况，要注意。

* **Java Code：**

  ```java
    class Solution {
        public boolean wordPattern(String pattern, String str) {
            String[] arr = str.split("\\s");
            if (pattern.length() != arr.length) return false;

            Map<Character, String> patternMap = new HashMap<>();
            Map<String, Character> stringMap = new HashMap<>();

            for (int i = 0; i < pattern.length(); ++i) {
                String sval = patternMap.get(pattern.charAt(i));
                Character pval = stringMap.get(arr[i]);
                // 如果两者map值等于对方，则通过，否则不通过
                if (sval == null && pval == null) {
                    patternMap.put(pattern.charAt(i), arr[i]);
                    stringMap.put(arr[i], pattern.charAt(i));
                } else if (arr[i].equals(sval) && new Character(pattern.charAt(i)).equals(pval)) {
                    continue;
                } else {
                    return false;
                }
            }
            return true;
        }
    }
  ```

_update May 7,2018 23:00_

### C++ Code

注意c++ split string的方法，用stringstream 和 getline 函数，传入 一个char 做分隔符。

```cpp
    class Solution {
    public:
        bool wordPattern(string pattern, string str) {
            // split string
            stringstream ss(str);
            string buffer;
            vector<string> arr;
            while (getline(ss, buffer, ' ')) {
                arr.push_back(buffer);
            }

            if (pattern.size() != arr.size()) return false;
            unordered_map<char, string> map1;
            unordered_map<string, char> map2;
            int i = 0;
            for (char c : pattern) {
                if (map1.count(c) && map1.find(c)->second != arr[i]
                   || map2.count(arr[i]) && map2.find(arr[i])->second != c) {
                    return false;
                }
                map1.insert(pair<char, string>{c, arr[i]});
                map2.insert(pair<string, char>{arr[i], c});
                ++i;
            }
            return true;
        }
    };
```

