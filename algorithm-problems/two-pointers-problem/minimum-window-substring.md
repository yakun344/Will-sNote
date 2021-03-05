# Minimum Window Substring
_update Mar 5, 2021_

---

Given two strings source and target. Return the minimum substring of source which contains each char of target.
Example

**Example 1:**
```
Input: source = "abc", target = "ac"
Output: "abc"
```

**Example 2:**
```
Input: source = "adobecodebanc", target = "abc"
Output: "banc"
Explanation: "banc" is the minimum substring of source string which contains each char of target "abc".
```

**Example 3:**
```
Input: source = "abc", target = "aa"
Output: ""
Explanation: No substring contains two 'a'.
```

**Challenge**

`O(n) time`

**Notice**
```
    If there is no answer, return "".
    You are guaranteed that the answer is unique.
    target may contain duplicate char, while the answer need to contain at least the same number of that char.
```

## Basic Idea
要找到source中包含target中全部字符（需要重复次数相同）的最短substring，我们以先用一个map统计target中每个字符出现的次数，然后利用双指针，
保证每次循环中先前进右边界，找到当前满足条件的窗口，然后逐渐右移左边界，这样就可以找到左右满足条件的窗口。

## Java Code
```java
public class Solution {
    public String minWindow(String source , String target) {
        if (source.length() == 0) {
            return "";
        }
        Map<Character, Integer> targetMap = new HashMap<>();
        Map<Character, Integer> map = new HashMap<>();
        for (char c : target.toCharArray()) {
            targetMap.put(c, targetMap.getOrDefault(c, 0) + 1);
        }
        // count表示当前窗口中数量大于等于target中相同字母的个数，
        // 如果count==targetMap.size()则说明窗口内满足条件
        int count = 0, left = 0, right = -1;
        String ret = null;
    OUT:
        while (true) {
            while (count < targetMap.size()) {
                if (right + 1 == source.length()) {
                    break OUT;
                }
                char rc = source.charAt(++right);
                map.put(rc, map.getOrDefault(rc, 0) + 1);
                if (map.get(rc).equals(targetMap.getOrDefault(rc, 0))) {
                    count++;
                }
            }
            if (ret == null || ret.length() > right - left + 1) {
                ret = source.substring(left, right + 1);
            }
            // 右移左边界，如果造成窗口内不满足条件，下次while循环会右移右边界直到出界或者满足条件
            char lc = source.charAt(left++);
            map.put(lc, map.get(lc) - 1);
            if (map.get(lc) < targetMap.getOrDefault(lc, 0)) {
                count--;
            }
        }
        return ret == null ? "" : ret;
    }
}
```
