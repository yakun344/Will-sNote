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
输入一组数长度为N，index代表N天，flowers[i]表示第 i 天开了的花的位置。问到哪一天为止出现如下情况：两朵开了的花之间有k个花盆没有开花。

* #### 思路1：TreeSet O(nlogn)
  从左向右扫描flowers数组，相当于逐天考虑这一天开的花，将花的位置存入treeSet，每次检查treeSet中之前一盆开了的花和之后一盆开了的花的位置，如果中间距离等于k，就返回当前天数。

* #### 思路2：Sliding Window + minQueue O(n)
  先将flowers数组转化为days数组，即转化为 days[i] 表示第 i 盆花开的天数。然后keep一个size为k的window，从左向右扫描，维持window中的最小的开花天数。每次检查当前window中最小开花天数和window外两侧开花天数，如果window中最小开花天数大于两侧天数表示当window两侧花开的时候窗内一朵都没开，此时就有了大小为k的空当。返回值就是所有满足条件的window两侧较大开花天数中的最小值。
