## Shortest Word Distance II
_update Sep 11,2017  14:13_

---
[LeetCode](https://leetcode.com/problems/shortest-word-distance-ii/description/)

**This is a follow up of Shortest Word Distance**. The only difference is now you are given the list of words and your method will be called repeatedly many times with different parameters. How would you optimize it?

Design a class which receives a list of words in the constructor, and implements a method that takes two words word1 and word2 and return the shortest distance between these two words in the list.

**For example,**

    Assume that words = ["practice", "makes", "perfect", "coding", "makes"].
    
    Given word1 = “coding”, word2 = “practice”, return 3.
    Given word1 = "makes", word2 = "coding", return 1.

**Note:**
You may assume that word1 does not equal to word2, and word1 and word2 are both in the list.

#### Basic Idea:
和前面一题类似，不同的是我们这次是先将所有词出现的index都分别存在一个 List 中，然后以 word 本身为 key，存入 hashmap。当query 的时候，还是相当于用双指针算法找两个排序数组中的最小差值。

#### Java Code：
```java
    class WordDistance {
        private Map<String, List<Integer>> map;
        public WordDistance(String[] words) {
            this.map = new HashMap<>();
            for (int i = 0; i < words.length; ++i) {
                if (! map.containsKey(words[i])) map.put(words[i], new ArrayList<Integer>());
                map.get(words[i]).add(i);
            }
        }
        
        public int shortest(String word1, String word2) {
            List<Integer> idx1 = map.get(word1);
            List<Integer> idx2 = map.get(word2);
            int i = 0, j = 0, minDist = Integer.MAX_VALUE;
            while (i < idx1.size() && j < idx2.size()) {
                int diff = idx1.get(i) - idx2.get(j);
                minDist = Math.min(minDist, Math.abs(diff));
                if (diff > 0) j++;
                else i++;
            }
            return minDist;
        }
    }
```