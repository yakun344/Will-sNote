# K Empty Slots
_update Sep21 2018, 21:40_

---
[LeetCode](https://leetcode.com/problems/k-empty-slots/description/)

There is a garden with N slots. In each slot, there is a flower. The N flowers will bloom one by one in N days. In each day, there will be exactly one flower blooming and it will be in the status of blooming since then.

Given an array flowers consists of number from 1 to N. Each number in the array represents the place where the flower will open in that day.

For example, `flowers[i] = x` means that the unique flower that blooms at day i will be at position x, where i and x will be in the range from 1 to N.

Also given an integer k, you need to output in which day there exists two flowers in the status of blooming, and also the number of flowers between them is k and these flowers are not blooming.

If there isn't such day, output -1.

**Example 1:**

    Input:
    flowers: [1,3,2]
    k: 1
    Output: 2
    Explanation: In the second day, the first and the third flower have become blooming.

**Example 2:**

    Input:
    flowers: [1,2,3]
    k: 1
    Output: -1

**Note:**  
The given array will be in the range `[1, 20000]`.

<br/>

### Basic Idea:
输入一组数长度为N，index代表N天，`flowers[i]`表示第 i 天开了的花的位置。问到哪一天为止出现如下情况：两朵开了的花之间有k个花盆没有开花。

* #### 思路1：TreeSet O(nlogn)
  从左向右扫描flowers数组，相当于逐天考虑这一天开的花，将花的位置存入treeSet，每次检查treeSet中之前一盆开了的花和之后一盆开了的花的位置，如果中间距离等于k，就返回当前天数。

* #### 思路2：Sliding Window + minQueue O(n)
  先将flowers数组转化为days数组，即转化为 days[i] 表示第 i 盆花开的天数。然后keep一个size为k的window，从左向右扫描，维持window中的最小的开花天数。每次检查当前window中最小开花天数和window外两侧开花天数，如果window中最小开花天数大于两侧天数表示当window两侧花开的时候窗内一朵都没开，此时就有了大小为k的空当。返回值就是所有满足条件的window两侧较大开花天数中的最小值。

  如果不用MinQueue优化，每次找min day的时候需要O(k)的时间复杂度，总共需要O(nk)。使用MinQueue优化之后可以O(n)完成。

O(n) 解法的精髓就是先将输入数组从第i开花的位置flowers[i]转化为了第i盆花开的天数days[i]。从而让直接用sliding window考虑k个相连花盆成为可能。

#### Java Code: _Sliding Window 无优化 O(nk)_
```java
class Solution {
    public int kEmptySlots(int[] flowers, int k) {
        int[] days = new int[flowers.length + 1];
        for (int i = 0; i < flowers.length; ++i) {
            days[flowers[i]] = i + 1;
            if (k == 0) {
                if (flowers[i] > 1 && days[flowers[i] - 1] != 0) return i + 1;
                else if (flowers[i] < days.length - 1 && days[flowers[i] + 1] != 0) return i + 1;
            }
        }
        if (k == 0) return -1;

        int left = 2, right = 1, ret = Integer.MAX_VALUE;
        while (true) {
            while (right - left + 1 < k) {
                if (right + 1 == days.length - 1) break;
                right++;
            }
            if (right - left + 1 != k) break;
            // get min day number in window
            int minDay = Integer.MAX_VALUE;
            for (int i = left; i <= right; ++i) {
                minDay = Math.min(minDay, days[i]);
            }
            // 中间有k大小的空当
            if (minDay > days[left - 1] && minDay > days[right + 1]) {
                ret = Math.min(ret, Math.max(days[left - 1], days[right + 1]));
            }
            // shrink window
            left++;
        }
        return ret == Integer.MAX_VALUE ? -1 : ret;
    }
}
```

#### Java Code: _Sliding Window + MinQueue_
```java
class Solution {
    public int kEmptySlots(int[] flowers, int k) {
        int[] days = new int[flowers.length + 1];
        for (int i = 0; i < flowers.length; ++i) {
            days[flowers[i]] = i + 1;
            if (k == 0) {
                if (flowers[i] > 1 && days[flowers[i] - 1] != 0) return i + 1;
                else if (flowers[i] < days.length - 1 && days[flowers[i] + 1] != 0) return i + 1;
            }
        }
        if (k == 0) return -1;
        // minQueue here
        Deque<Integer> minQueue = new ArrayDeque<>();
        int left = 2, right = 1, ret = Integer.MAX_VALUE;
        while (true) {
            while (right - left + 1 < k) {
                if (right + 1 == days.length - 1) break;
                right++;
                while (! minQueue.isEmpty() && days[minQueue.peekLast()] > days[right]) {
                    minQueue.pollLast();
                }
                minQueue.offerLast(right);
            }
            if (right - left + 1 != k) break;
            // get min day number in window
            int minDay = days[minQueue.peekFirst()];
            // 中间有k大小的空当
            if (minDay > days[left - 1] && minDay > days[right + 1]) {
                ret = Math.min(ret, Math.max(days[left - 1], days[right + 1]));
            }
            // shrink window
            if (left == minQueue.peekFirst()) minQueue.pollFirst();
            left++;
        }
        return ret == Integer.MAX_VALUE ? -1 : ret;
    }
}
```
