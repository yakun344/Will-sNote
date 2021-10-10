# Odd Even Jump

_update Jan 13 2019, 15:05_

[LeetCode](https://leetcode.com/problems/odd-even-jump/)

You are given an integer array A. From some starting index, you can make a series of jumps. The (1st, 3rd, 5th, ...) jumps in the series are called odd numbered jumps, and the (2nd, 4th, 6th, ...) jumps in the series are called even numbered jumps.

You may from index i jump forward to index j `(with i < j)` in the following way:

During odd numbered jumps (ie. jumps 1, 3, 5, ...), you jump to the index j such that `A[i] <= A[j]` and `A[j]` is the smallest possible value. If there are multiple such indexes j, you can only jump to the smallest such index j. During even numbered jumps (ie. jumps 2, 4, 6, ...), you jump to the index j such that `A[i] >= A[j]` and `A[j]` is the largest possible value. If there are multiple such indexes j, you can only jump to the smallest such index j. (It may be the case that for some index i, there are no legal jumps.) A starting index is good if, starting from that index, you can reach the end of the array (index A.length - 1) by jumping some number of times (possibly 0 or more than once.)

Return the number of good starting indexes.

**Example 1:**

```
Input: [10,13,12,14,15]
Output: 2
Explanation: 
From starting index i = 0, we can jump to i = 2 (since A[2] is the smallest among A[1], A[2], A[3], A[4] that is greater or equal to A[0]), then we can't jump any more.
From starting index i = 1 and i = 2, we can jump to i = 3, then we can't jump any more.
From starting index i = 3, we can jump to i = 4, so we've reached the end.
From starting index i = 4, we've reached the end already.
In total, there are 2 different starting indexes (i = 3, i = 4) where we can reach the end with some number of jumps.
```

**Example 2:**

```
Input: [2,3,1,1,4]
Output: 3
Explanation: 
From starting index i = 0, we make jumps to i = 1, i = 2, i = 3:

During our 1st jump (odd numbered), we first jump to i = 1 because A[1] is the smallest value in (A[1], A[2], A[3], A[4]) that is greater than or equal to A[0].

During our 2nd jump (even numbered), we jump from i = 1 to i = 2 because A[2] is the largest value in (A[2], A[3], A[4]) that is less than or equal to A[1].  A[3] is also the largest value, but 2 is a smaller index, so we can only jump to i = 2 and not i = 3.

During our 3rd jump (odd numbered), we jump from i = 2 to i = 3 because A[3] is the smallest value in (A[3], A[4]) that is greater than or equal to A[2].

We can't jump from i = 3 to i = 4, so the starting index i = 0 is not good.

In a similar manner, we can deduce that:
From starting index i = 1, we jump to i = 4, so we reach the end.
From starting index i = 2, we jump to i = 3, and then we can't jump anymore.
From starting index i = 3, we jump to i = 4, so we reach the end.
From starting index i = 4, we are already at the end.
In total, there are 3 different starting indexes (i = 1, i = 3, i = 4) where we can reach the end with some number of jumps.
```

**Example 3:**

```
Input: [5,1,3,4,2]
Output: 3
Explanation: 
We can reach the end from starting indexes 1, 2, and 4.
```

**Note:**

1. `1 <= A.length <= 20000`
2. `0 <= A[i] < 100000`

## Basic Idea:

*   **Solution 1: Brute Force + Memorization**

    我们可以利用DP的思路，从右向左逐一判断从每个index出发能否到达终点，利用两个set分别记录odd和even起点时哪些index可以到达终点。这个做法较brute force有所优化，但是应该没有数量级上的变化。时间复杂度应该是 `O(n^3)`，因为对于每个index，我们每一步需要检查其后面每一个元素，而可能每次都只前进一步。

    ```java
      class Solution {
          private Set<Integer> oddGood = new HashSet<>();
          private Set<Integer> evenGood = new HashSet<>();
          private int[] arr;

          public int oddEvenJumps(int[] A) {
              arr = A;
              oddGood.add(A.length - 1);
              evenGood.add(A.length - 1);
              int count = 0;
              for (int i = A.length - 1; i >= 0; --i) {
                  if (isGood(i, true)) count++;
              }
              return count;
          }

          private boolean isGood(int start, boolean isOdd) {
              if (start == -1) return false;
              if (isOdd && oddGood.contains(start)) {
                  return true;
              }
              if (! isOdd && evenGood.contains(start)) {
                  return true;
              }
              int next = -1;
              if (isOdd) {
                  // 找右边最近的大于等于curr的最小值
                  for (int i = arr.length - 1; i > start; --i) {
                      if (arr[i] >= arr[start]) {
                          if (next == -1 || arr[i] <= arr[next]) next = i;
                      }
                  }
                  boolean ret = isGood(next, ! isOdd);
                  if (ret) oddGood.add(start);
                  return ret;
              } else {
                  // 找右边最近的小于等于curr的最大值
                  for (int i = arr.length - 1; i > start; --i) {
                      if (arr[i] <= arr[start]) {
                          if (next == -1 || arr[i] >= arr[next]) next = i;
                      }
                  }
                  boolean ret = isGood(next, ! isOdd);
                  if (ret) evenGood.add(start);
                  return ret;
              }
          }
      }
    ```
*   **Solution 2: Monotonic Stack + DP (`O(NlogN)`)**

    Brute Force的方法中，我们在检查每个index出发能否到达终点的时候，每一步都需要O(n)时间来找该index右边（以odd为例）比它大的元素中最小而且最左边的。如果我们可以通过一步预处理，得到一个array: `oddNext`，表示每个元素在odd step中下一步的位置，同时另外O(n)时间生成一个`evenNext`，我们就可以再通过一个O(N)的dp生成最终结果，这样可以极大优化时间复杂度，取决于预处理生成`oddNext`和`evenNext`的时间。

    预处理（以oddNext为例）的这一步我们要用到单调栈。印象里单调栈一般用来解决诸如 “找每个元素右边第一个小于自身的元素” 之类的问题，但是这里我们需要解决的是 “找每个元素右边大于它的元素中最小且最左边的元素”，看似很复杂，但是我们可以先对输入数组进行一些处理，将问题转化为单调栈问题：

    ```c
      输入数组： [2,3,1,1,4]
                0 1 2 3 4 
      转换为二维数组，将index也存入，然后按照val进行排序，得到：
              [1,1,2,3,4]
              [2,3,0,1,4]
      这样当我们从左往右扫描的时候val就已经是sorted了，我们只需要找到每个元素右边第一
        个原index值大于自己原index的元素，这样问题就被转换成为了一个单调栈问题。
    ```

    我们可以再多做一步分析，对于上面的问题 “找每个元素右边大于它的元素中最小且最左边的元素”，“大于它的元素中最小”这个条件暗示我们对原数组进行排序，相当于“下一个比他大的元素”，“右边最左边”则暗示了“第一个index大于当前元素的元素”这个条件，这两个条件结合在一起，就可以想到用单调栈了。

    这里对**单调栈**的应用非常巧妙，要记得勤复习。

    在我们经过 O(nlogn) 的预处理，生成了 `oddNext` 和 `evenNext` 两个数组之后，我们就可以从右往左做一个DP，O(N)时间得到结果了。DP的方法如下：

    ```c
      输入数组为 arr。
      odd[arr.length - 1] = true;
      even[arr.length - 1] = true;

      odd[i] = even[oddNext[i]];
      even[i] = odd[evenNext[i]];
      if (oddNext[i] or evenNext[i]) that val should be false;

      这样，i from arr.length - 1 to 0 逐一递推，odd中为true的个数就是最终结果。
    ```

    **Java Code:**

    ```java
      class Solution {
          public int oddEvenJumps(int[] A) {
              int[][] arr = new int[A.length][2];
              for (int i = 0; i < A.length; ++i) {
                  arr[i][0] = A[i];
                  arr[i][1] = i;
              }
              Arrays.sort(arr, (a, b)->Integer.compare(a[0], b[0]));
              int[] oddNext = getNextArray(arr);
              Arrays.sort(arr, (a, b)->Integer.compare(b[0], a[0]));
              int[] evenNext = getNextArray(arr);

              int count = 1;
              boolean[] odd = new boolean[A.length];
              boolean[] even = new boolean[A.length];
              odd[A.length - 1] = true;
              even[A.length - 1] = true;
              for (int i = A.length - 2; i >= 0; --i) {
                  if (evenNext[i] != -1) even[i] = odd[evenNext[i]];
                  if (oddNext[i] != -1) odd[i] = even[oddNext[i]];
                  if (odd[i]) count++;
              }
              return count;
          }

          // 这个function求的是每个元素对应的原数组中第一个index大于它的index
          // 这样，如果输入数组按照val生序排列，我们求的就是oddNext （“右边大于它的最左边最小值”）
          // 如果输入数组按照val降序排列，我们求的就是evenNext （“右边小于它的最左边最大值”）
          private int[] getNextArray(int[][] nums) {
              int[] ret = new int[nums.length];
              Arrays.fill(ret, -1);
              Deque<int[]> stack = new ArrayDeque<>();
              for (int i = 0; i < nums.length; ++i) {
                  while (! stack.isEmpty() && stack.peekLast()[1] < nums[i][1]) {
                      ret[stack.pollLast()[1]] = nums[i][1];
                  }
                  stack.offerLast(nums[i]);
              }
              return ret;
          }
      }
    ```
*   **Solution 2: TreeMap + DP (`O(NlogN)`)**

    利用TreeMap可以动态维持一个有序序列。我们只要从右向左扫描，就可以保证对于每个元素，当前在treemap中的其他元素都在该元素右边。我们以val为key，就可以 `O(logN)` 时间得到当前元素右边最近的大于它的最小值或者小于它的最大值（因为同样的key，后进入map的会覆盖之前的，自然就保证了相同的值只保留最左边的）。同时我们进行DP的步骤，更新 odd 和 even 数组，总时间复杂度为 `O(NlogN)`, 和之前的单调栈相同。

    ```java
      class Solution {
          public int oddEvenJumps(int[] A) {
              TreeMap<Integer, Integer> treeMap = new TreeMap<>(); // <val, index>
              boolean[] odd = new boolean[A.length];
              boolean[] even = new boolean[A.length];
              odd[A.length - 1] = true;
              even[A.length - 1] = true;
              int count = 0;
              for (int i = A.length - 1; i >= 0; --i) {
                  Map.Entry<Integer, Integer> oddNext = treeMap.ceilingEntry(A[i]);
                  Map.Entry<Integer, Integer> evenNext = treeMap.floorEntry(A[i]);
                  if (oddNext != null) odd[i] = even[oddNext.getValue()];
                  if (evenNext != null) even[i] = odd[evenNext.getValue()];
                  if (odd[i]) count++;
                  treeMap.put(A[i], i);
              }
              return count;
          }
      }
      
    ```

    需要注意这里一定要使用TreeMap，因为从右向左扫描，所以相同的Key只会存放最左边的index。使用TreeSet\<int\[]> 是不行的，因为floor 会返回小于当前值的最大的key最右边出现过的index。

