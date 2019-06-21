# Longest Consecutive Sequence

_update Aug 24, 2017 14:47_

[LintCode](http://www.lintcode.com/en/problem/longest-consecutive-sequence/)

Given an unsorted array of integers, find the length of the longest consecutive elements sequence.

**Clarification** Your algorithm should run in O\(n\) complexity.

**Example**

```text
Given [100, 4, 200, 1, 3, 2],
The longest consecutive elements sequence is [1, 2, 3, 4]. Return its length: 4.
```

### Basic Idea:

题目要求在O\(n\)时间内完成，首先排除了排序之类的相关方法，因为即使使用 counting sort，也需要O\(range\)的时间。接下来我们就想到了第一次遍历使用一个set存放所有数，第二次的时候从每个连续序列的第一个数（`n-1 not in set`）开始，测量这个连续序列的长度，最终返回最长长度。

一个例子说明：

```text
input: [100, 4, 200, 3, 1, 2, 8]
先存入set，然后开始第二次遍历：

    100 ------------- 是第一个数，len = 1；
    4   ------------- 3 存在，不是第一个数，跳过；
    200 ------------- 是第一个数，len = 1；
    3   ------------- 2 存在，跳过；
    1   ------------- 是第一个数，len = 4 （1，2，3，4）；
    2   ------------- 1 存在，跳过；
    8   ------------- 是第一个数，len = 1；

结束后返回最长len，即为 4，对应最长连续序列 [1,2,3,4];
```

因为事实上我们只会访问每个连续序列一次，所以时间复杂度为O\(n\);

### Python Code：

```python
    class Solution:
        """
        @param: num: A list of integers
        @return: An integer
        """
        def longestConsecutive(self, num):
            st = set(num)
            maxLen = 0
            for n in num:
                if n - 1 not in st:
                    length = 1
                    while n + 1 in st:
                        length += 1
                        n += 1
                    maxLen = max(maxLen, length)
            return maxLen
```

### Java Code:

```java
    public class Solution {
        /*
         * @param num: A list of integers
         * @return: An integer
         */
        public int longestConsecutive(int[] num) {
            if (num == null || num.length == 0) return 0;
            Set<Integer> set = new HashSet<>();
            for (int n : num) {
                set.add(n);
            }
            int maxLength = 1;
            for (int n : num) {
                // 如果当前n是连续序列的第一个数，依次计算n++，看该序列有多长
                if (! set.contains(n - 1)) {
                    int len = 1;
                    while (set.contains(++n)) {
                        len += 1;
                    }
                    maxLength = Math.max(maxLength, len);
                }
            }
            return maxLength;
        }
    }
```

_update 2018-10-22 20:34:35_

## Update: 基于删除元素

除了上面的方法，我们也可以每次拿到一个数作为中点，像两遍扩展，并且沿途将数字在set中删除。这样做的时间复杂度也是O\(n\)，因为每个数字都只会被加入set一次，再删除一次。

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) set.add(num);
        int ret = 0;
        for (int num : nums) {
            int curr = num;
            int len = 0;
            while (set.remove(curr)) {
                len++;
                curr++;
            }
            curr = num - 1;
            while (set.remove(curr)) {
                len++;
                curr--;
            }
            ret = Math.max(len, ret);
        }
        return ret;
    }
}
```

