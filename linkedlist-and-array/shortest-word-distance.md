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




















