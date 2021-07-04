# Number of Wonderful Substrings

_update Jun 27_

\_\_

A **wonderful** string is a string where **at most one** letter appears an **odd** number of times.

* For example, `"ccjjc"` and `"abab"` are wonderful, but `"ab"` is not.

Given a string `word` that consists of the first ten lowercase English letters \(`'a'` through `'j'`\), return _the **number of wonderful non-empty substrings** in_ `word`_. If the same substring appears multiple times in_ `word`_, then count **each occurrence** separately._

A **substring** is a contiguous sequence of characters in a string.

**Example 1:**

```text
Input: word = "aba"
Output: 4
Explanation: The four wonderful substrings are underlined below:
- "aba" -> "a"
- "aba" -> "b"
- "aba" -> "a"
- "aba" -> "aba"
```

**Example 2:**

```text
Input: word = "aabb"
Output: 9
Explanation: The nine wonderful substrings are underlined below:
- "aabb" -> "a"
- "aabb" -> "aa"
- "aabb" -> "aab"
- "aabb" -> "aabb"
- "aabb" -> "a"
- "aabb" -> "abb"
- "aabb" -> "b"
- "aabb" -> "bb"
- "aabb" -> "b"
```

**Example 3:**

```text
Input: word = "he"
Output: 2
Explanation: The two wonderful substrings are underlined below:
- "he" -> "h"
- "he" -> "e"
```

**Constraints:**

* `1 <= word.length <= 105`
* `word` consists of lowercase English letters from `'a'` to `'j'`.

## Basic Idea

因为这道题给的数据量比较大， 肯定不能用暴力方法做，考虑到string中的字母范围就是a-j的十个字母，而且我们只关心每个字母出现的奇偶次数，于是我们可以考虑使用bitmask表示任意的一个substring，每一位代表一个字母，无论substring的长度如何，我们只关心每个字母出现次数的奇偶性。这样，对于 `s[0,p]` 的mask 和 `s[0, q]` 的 mask，如果 `q > p`, 只要`s[0,p]mask`和`s[0,q] mask` 相等或者只差一个bit，就表示 `s[p+1,q]` 是满足条件的。于是我们可以用一个count来记录所有prefix的mask出现的次数，每次算出当前的mask，然后加上前面与其相同以及只差一个bit的mask的个数。

总的时间复杂度为 `O(10n)`, 其中 n 为word的长度，每次需要检查10个bit。

## Java Code:

```java
class Solution {
    public long wonderfulSubstrings(String word) {
        int[] count = new int[1<<10];
        // 每个bit表示一个字母，最多10 bits，0表示偶数，1表示奇数
        int mask = 0;
        long res = 0;
        count[0] = 1;
        for (char c : word.toCharArray()) {
            int ord = c - 'a';
            mask = (mask ^ (1 << ord));
            // 加上之前出现过的所有奇偶情况相同的prefix mask的个数
            // 因为此时在他们之间的部分所有字母出现次数均为偶数
            res += count[mask];
            // 检查所有prefix mask，加上所有和当前mask只差1 bit的个数
            // 因为此时相当于中间的部分只有一个字母出现过奇数次
            for (int i = 0; i < 10; ++i) {
                res += count[mask ^ (1 << i)];
            }
            count[mask]++;
        }
        return res;
    }
}
```

