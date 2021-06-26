# Number of Matching Subsequences

_update Jun 24, 2021_

![image](https://user-images.githubusercontent.com/24964756/123303800-0ad74480-d4d3-11eb-8a82-a545ba5860dd.png)

## Basic Idea

### 1. Binary Search

用一个长度为26的数组记录每个字符在s中出现过的index，然后对于每个word，用二分法检查它是否是s的subsequence。

### 2. 维持每个word下一个需要match的字母

同样用一个长度为26的数组，但其中存放的是每个字母对应的下一个要match这个字母的word。然后扫描s，更新每个word下一个要match的index以及将他们放到对应字母的位置上。 例如对于 s = abc, word = ab, 则一开始 word 在 a 对应的位置，index为0，扫描过 `s[0]` 后，word的index变为1，挪到了 b 对应的位置。扫描过 `s[1]` 后，word index 变为2，结束。

## Java Code:

**1. Binary Search**

```java
class Solution {
    public int numMatchingSubseq(String s, String[] words) {
        List<Integer>[] mapping = new ArrayList[255];
        for (int i = 'a'; i <= 'z'; ++i) {
            mapping[i] = new ArrayList<>();
        }
        for (int i = 0; i < s.length(); ++i) {
            mapping[s.charAt(i)].add(i);
        }
        int ret = 0;
        for (String word : words) {
            if (check(mapping, word)) {
                ret++;
            }
        }
        return ret;
    }

    // 检查能否对于word中每个字符找到mapping中对应的递增index的序列
    private boolean check(List<Integer>[] mapping, String word) {
        int last = -1;
        for (char c : word.toCharArray()) {
            List<Integer> indices = mapping[c];
            if (indices.isEmpty()) {
                return false;
            }
            // 二分找大于last的最小index
            int left = 0, right = indices.size() - 1;
            while (left + 1 < right) {
                int mid = (left + right) / 2;
                if (indices.get(mid) <= last) {
                    left = mid;
                } else {
                    right = mid;
                }
            }
            if (indices.get(left) > last) {
                last = indices.get(left);
            } else if (indices.get(right) > last) {
                last = indices.get(right);
            } else {
                return false;
            }
        }
        return true;
    }
}
```

**2. 为每个word维持nextIndex**

```java
class Solution {
    private static class Node {
        String s;
        int nextIndex;
        Node(String s, int nextIndex) {
            this.s = s;
            this.nextIndex = nextIndex;
        }
    }

    public int numMatchingSubseq(String str, String[] words) {
        List<Node>[] mapping = new ArrayList[255];
        for (char c = 'a'; c <= 'z'; ++c) {
            mapping[c] = new ArrayList<>();
        }
        // 将每个word按照首字母放入mapping
        for (String word : words) {
            mapping[word.charAt(0)].add(new Node(word, 0));
        }
        int ret = 0;
        // 从左到右扫描s，同时如果如果当前字母和某些word匹配，则后移word中的index，
        // 同时将该word挪到新的地方
        for (char c : str.toCharArray()) {
            List<Node> matched = mapping[c];
            // 新建一个当作更新后的当前c对应的list，避免一边for一边改
            List<Node> newMatched = new ArrayList<>();
            for (Node word : matched) {
                int index = word.nextIndex;
                String s = word.s;
                if (index == s.length() - 1) {
                    ret++;
                } else if (s.charAt(index) == s.charAt(index + 1)) {
                    // 仍然是同一个c
                    newMatched.add(new Node(s, index + 1));
                } else {
                    mapping[s.charAt(index + 1)].add(new Node(s, index + 1));
                }
            }
            mapping[c] = newMatched;
        }
        return ret;
    }
}
```

