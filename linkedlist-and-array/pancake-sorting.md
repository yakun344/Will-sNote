# Pancake Sorting
_update Jan 8 2019, 21:31_

---
[LeetCode](https://leetcode.com/problems/pancake-sorting/)

Given an array A, we can perform a pancake flip: We choose some positive integer k <= A.length, then reverse the order of the first k elements of A.  We want to perform zero or more pancake flips (doing them one after another in succession) to sort the array A.

Return the k-values corresponding to a sequence of pancake flips that sort A.  Any valid answer that sorts the array within 10 * A.length flips will be judged as correct.

 

**Example 1:**

    Input: [3,2,4,1]
    Output: [4,2,4,3]
    Explanation: 
    We perform 4 pancake flips, with k values 4, 2, 4, and 3.
    Starting state: A = [3, 2, 4, 1]
    After 1st flip (k=4): A = [1, 4, 2, 3]
    After 2nd flip (k=2): A = [4, 1, 2, 3]
    After 3rd flip (k=4): A = [3, 2, 1, 4]
    After 4th flip (k=3): A = [1, 2, 3, 4], which is sorted. 

**Example 2:**

    Input: [1,2,3]
    Output: []
    Explanation: The input is already sorted, so there is no need to flip anything.
    Note that other answers, such as [3, 3], would also be accepted.
    

**Note:**

1. `1 <= A.length <= 100`
2. `A[i] is a permutation of [1, 2, ..., A.length]`

<br/>

### Basic Idea:
给定一个长度为len，由`1,2,3...len` 组成的数组，每次可以reverse 前k个数字，问每次需要reverse几个，最终可以使array排序。

基本思路就是我们可以通过两次reverse将任意一个数字换到末尾，所以我们可以每次换一个数到末尾。
```
例如对于输入 [3,2,4,1] 
 k = 3,    [4,2,3,1]
 k = 4,    [1,3,2,4], 此时4已经在最终位置上，令len-- = 3
 k = 2,    [3,1,2,4]
 k = 3,    [2,1,3,4], 3 done，len = 2
 k = 2,    [1,2,3,4], done
```
时间复杂度为 `O(n^2)`。

#### Java Code:
```java
class Solution {
    public List<Integer> pancakeSort(int[] A) {
        List<Integer> res = new ArrayList<>();
        LinkedList<Integer> nums = new LinkedList<>();
        for (int num : A) nums.offerLast(num);
        int len = A.length;
        // target为当前要找的最大值，从A.length开始，逐一递减
        for (int target = A.length; target >= 1; --target) {
            LinkedList<Integer> next = new LinkedList<>();
            // 从后往前搜索，找target
            for (int i = len - 1; i >= 0; --i) {
                if (nums.get(i) == target) {
                    if (i == len - 1) break;
                    res.add(i + 1);
                    res.add(len);
                    for (int j = 0; j <= i; ++j) {
                        next.offerLast(nums.get(j));
                    }
                    nums = next;
                    break;
                } else {
                    next.offerLast(nums.get(i));
                }
            }
            len--;
        }
        return res;
    }
}
```
