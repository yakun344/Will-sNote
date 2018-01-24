## Shortest Word Distance
_update Sep 11, 2017  13:44_

---
[LeetCode](https://leetcode.com/problems/shortest-word-distance/description/)

Given a list of words and two words word1 and word2, return the shortest distance between these two words in the list.

**For example,**

    Assume that words = ["practice", "makes", "perfect", "coding", "makes"].
    
    Given word1 = “coding”, word2 = “practice”, return 3.
    Given word1 = "makes", word2 = "coding", return 1.

**Note:**
You may assume that word1 does not equal to word2, and word1 and word2 are both in the list.

#### Basic Idea:
先遍历一遍 words 数组，得到 word1 和 word2 的 indices list，然后用双指针找这两个已经排序数组中元素的最小差值就可以了。

具体地，在找两sorted array最小差时，我们令 i，j 起初都为 0，每次计算差值和当前最小取min，然后令 i，j 所对应值较小的后移一位，再继续比较。

#### Java Code：
```java
    class Solution {
        public int shortestDistance(String[] words, String word1, String word2) {
            List<Integer> indices1 = new ArrayList<>();
            List<Integer> indices2 = new ArrayList<>();
            for (int i = 0; i < words.length; ++i) {
                if (words[i].equals(word1)) indices1.add(i);
                else if (words[i].equals(word2)) indices2.add(i);
            }
            int minDist = Integer.MAX_VALUE;
            int i = 0, j = 0;
            while (i < indices1.size() && j < indices2.size()) {
                int diff = indices1.get(i) - indices2.get(j);
                minDist = Math.min(Math.abs(diff), minDist);
                if (diff > 0) j++;
                else i++;
            }
            return minDist;
        }
    }
```

---
## Shortest Word Distance III (follow up)
[LeetCode](https://leetcode.com/problems/shortest-word-distance-iii/description/)

This is a follow up of Shortest Word Distance. The only difference is now word1 could be the same as word2.

Given a list of words and two words word1 and word2, return the shortest distance between these two words in the list.

word1 and word2 may be the same and they represent two individual words in the list.

**For example,**

    Assume that words = ["practice", "makes", "perfect", "coding", "makes"].
    
    Given word1 = “makes”, word2 = “coding”, return 1.
    Given word1 = "makes", word2 = "makes", return 3.

**Note:**  
You may assume word1 and word2 are both in the list.

#### Basic Idea:
只需要增加一种情况，当 word1和word2 相同的时候，另外考虑即可。

Java Code:
```java
    class Solution {
        public int shortestWordDistance(String[] words, String word1, String word2) {
            if (word1.equals(word2)) {
                int ret = Integer.MAX_VALUE, lastIdx = -1;
                for (int i = 0; i < words.length; ++i) {
                    if (words[i].equals(word1)) {
                        if (lastIdx != -1) ret = Math.min(i - lastIdx, ret);
                        lastIdx = i;
                    }
                }
                return ret;
            }
            // 如果不相等，和之前那道一样    
            List<Integer> idx1 = new ArrayList<>();
            List<Integer> idx2 = new ArrayList<>();
            for (int i = 0; i < words.length; ++i) {
                if (words[i].equals(word1)) idx1.add(i);
                else if (words[i].equals(word2)) idx2.add(i);
            }
            int i = 0, j = 0, ret = Integer.MAX_VALUE;
            while (i < idx1.size() && j < idx2.size()) {
                int diff = idx1.get(i) - idx2.get(j);
                ret = Math.min(ret, Math.abs(diff));
                if (diff > 0) j++;
                else i++;
            }
            return ret;
        }
    }
```
---
_update Dec 2, 2017  1:22_

### Update
#### 求两排序数组的最小差 (谁小移谁)
找两个 sorted array 元素之间的最小差值，使用两个指针分别指向两个array中的元素，则两个array中的元素分别为 `arr1[i] 和 arr2[j]`， 如果 `arr1[i] > arr2[j]`，我们令 `j++`，否则的话 `i++`，并在这个过程中每次跟踪diff的最小值。这个方法我觉得非常重要，需要着重记忆和理解。

#### 更新一个 follow up 的 python 解法
在这个solution中，将两种情况打包成两个function，更加结构分明:
```python
    class Solution:
        def shortestWordDistance(self, words, word1, word2):
            """
            :type words: List[str]
            :type word1: str
            :type word2: str
            :rtype: int
            """
            def sameWordsCase():
                minDiff = float('inf')
                lastIdx = None
                for i in range(len(words)):
                    if words[i] == word1:
                        if lastIdx is None:
                            lastIdx = i
                        else:
                            minDiff = min(minDiff, abs(i - lastIdx))
                            lastIdx = i
                return minDiff
            
            
            def differentWordsCase():
                lst1 = []
                lst2 = []
                for i in range(len(words)):
                    if words[i] == word1:
                        lst1.append(i)
                    elif words[i] == word2:
                        lst2.append(i)
                # find min diff in two sorted arrays
                i, j = 0, 0
                minDiff = float('inf')
                while i < len(lst1) and j < len(lst2):
                    diff = lst1[i] - lst2[j]
                    minDiff = min(minDiff, abs(diff))
                    if diff > 0: j += 1
                    else: i += 1
                return minDiff
            
            
            # Main Part
            if word1 == word2: return sameWordsCase()
            else: return differentWordsCase()
```


















