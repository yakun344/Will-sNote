# Group Anagrams

_udpate Sep 17 2018, 11:30_

[LeetCode](https://leetcode.com/problems/group-anagrams/description/)

Given an array of strings, group anagrams together.

**Example:**

```text
Input: ["eat", "tea", "tan", "ate", "nat", "bat"],
Output:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```

**Note:**

1. All inputs will be in lowercase.
2. The order of your output does not matter.

## Basic Idea:

* **Solution 1: Sorting**

  将每个单词构造为一个pair，pair.second 为其自己，pair.first 为其所包含字符排序后的序列，然后再将整个数组按照first排序，这样相同组的string就会排在一起，只要进行一次扫描就可以了；

  ```java
  class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        String[][] arr = new String[strs.length][2];
        for (int i = 0; i < strs.length; ++i) {
            arr[i][0] = sortString(strs[i]);
            arr[i][1] = strs[i];
        }
        Arrays.sort(arr, (a, b)->{ return a[0].compareTo(b[0]); });

        for (String[] s : arr) {
            System.out.println(s[0] + " " + s[1]);
        }

        List<List<String>> res = new ArrayList<>();
        for (int i = 0; i < strs.length; ++i) {
            int j = i;
            List<String> group = new ArrayList<>();
            while (j < arr.length && arr[i][0].equals(arr[j][0])) {
                group.add(arr[j][1]);
                j++;
            }
            res.add(group);
            i = j > i ? j - 1 : i;
        }
        return res;
    }

    private String sortString(String s) {
        char[] arr = s.toCharArray();
        Arrays.sort(arr);
        return new String(arr);
    }
  }
  ```

* **Solution 2: Using HashMap**

  使用一个`Map<String, List<String>>`，Key为一个string包含的字符排序后形成的新string。

  ```java
  class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        for (String s : strs) {
            char[] arr = s.toCharArray();
            Arrays.sort(arr);
            String key = new String(arr);
            if (! map.containsKey(key)) map.put(key, new ArrayList<String>());
            map.get(key).add(s);
        }
        List<List<String>> res = new ArrayList<>();
        for (Map.Entry<String, List<String>> entry : map.entrySet()) {
            res.add(entry.getValue());
        }
        return res;
    }
  }
  ```

