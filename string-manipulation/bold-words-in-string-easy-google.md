# Bold Words in String
_update 2018-05-16 00:22:27_

---
[LeetCode](https://leetcode.com/problems/bold-words-in-string/description/)


Given a set of keywords words and a string S, make all appearances of all keywords in S bold. Any letters between `<b>` and `</b>` tags become bold.

The returned string should use the least number of tags possible, and of course the tags should form a valid combination.

For example, given that words = `["ab", "bc"]` and S = "aabcd", we should return `"a<b>abc</b>d"`. Note that returning `"a<b>a<b>b</b>c</b>d"` would use more tags, so it is incorrect.

**Note:**
* words has length in range `[0, 50]`.
* `words[i]` has length in range `[1, 10]`.
* S has length in range `[0, 500]`.
* All characters in words[i] and S are lowercase letters.

<br>

### Basic Idea:
要在 `S` 中找到所有在 words array 中出现过的所有 part，并且要合并，最好的办法就是先找到所有 match 的部分，然后将其标记，最后再生成结果string；

* #### C++ Code:
```cpp
    class Solution {
        // find all start indices of matching substring, store the indices in vector
        vector<int> strStr(string str, string target) {
            vector<int> ret;
            for (int start = 0; start < str.size() - target.size() + 1; ++start) {
                int i = start;
                int j = 0;
                while (j < target.size() && str[i] == target[j]) {
                    ++i;
                    ++j;
                }
                if (j == target.size()) ret.push_back(start);
            }
            return ret;
        }
    public:
        string boldWords(vector<string>& words, string S) {
            vector<int> marker(S.size());
            for (string word : words) {
                vector<int> indice = strStr(S, word);
                // 将所有需要bold的index都标记为1
                for (int index : indice) {
                    for (int i = 0; i < word.size(); ++i) {
                        marker[index + i] = 1;
                    }
                }
            }
            
            // for (int num : marker) cout << num << " " ;
            
            // 根据marker array中标记为 1 的部分需要变 bold 来构建最终 string
            string ret;
            for (int i = 0; i < S.size(); ++i) {
                if ((i == 0 || marker[i - 1] == 0) && marker[i] == 1) ret.append("<b>");
                ret.append(string(1, S[i]));
                if ((marker[i] == 1 && (i == S.size() - 1 || marker[i + 1] == 0))) ret.append("</b>");
            }
            return ret;
        }
    };
```