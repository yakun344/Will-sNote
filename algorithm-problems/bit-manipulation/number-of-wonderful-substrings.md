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

### Basic Idea



