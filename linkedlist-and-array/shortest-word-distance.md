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

















