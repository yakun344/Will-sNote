## 4Sum II
_Update Aug 19,2017  15:45_

---
[LeetCode](https://leetcode.com/problems/4sum-ii/description/)

Given four lists A, B, C, D of integer values, compute how many tuples (i, j, k, l) there are such that A[i] + B[j] + C[k] + D[l] is zero.

To make problem a bit easier, all A, B, C, D have same length of N where 0 ≤ N ≤ 500. All integers are in the range of -228 to 228 - 1 and the result is guaranteed to be at most 231 - 1.

**Example:**

    Input:
    A = [ 1, 2]
    B = [-2,-1]
    C = [-1, 2]
    D = [ 0, 2]

    Output:
    2

**Explanation:**
The two tuples are:

1.  `(0, 0, 0, 1) -> A[0] + B[0] + C[0] + D[1] = 1 + (-2) + (-1) + 2 = 0`
2.  `(1, 1, 0, 0) -> A[1] + B[1] + C[0] + D[0] = 2 + (-1) + (-1) + 0 = 0`

#### Basic Idea（分治）:
注意到这道题目是要从每个排序列表中选择一个数，我们可以考虑先把其分为两组，`(A,B) 和 (C,D)`然后算出每组中所有数字之间两两之和，存入HashMap，然后检查有多少组两组和相加等于0. 这其实是一种分治的思路。

#### Java Code：
```java
    class Solution {
        public int fourSumCount(int[] A, int[] B, int[] C, int[] D) {
            Map<Integer, Integer> map = new HashMap<>();
            for (int i = 0; i < A.length; ++i) {
                for (int j = 0; j < B.length; ++j) {
                    map.put(A[i] + B[j], map.getOrDefault(A[i] + B[j], 0) + 1);
                }
            }
            int ret = 0;
            for (int i = 0; i < C.length; ++i) {
                for (int j = 0; j < D.length; ++j) {
                    ret += map.getOrDefault(-(C[i] + D[j]), 0);
                }
            }
            return ret;
        }
    }
```

---
_update 2018-08-05 13:27:03_

#### Update C++ Solution
要特别注意unordered_map的用法，`[]`有可能会导致插入一个元素，所以要用find。
```cpp
  class Solution {
  public:
      int fourSumCount(vector<int>& A, vector<int>& B, vector<int>& C, vector<int>& D) {
          unordered_map<int, int> table;
          for (int a : A) {
              for (int b : B) {
                  int sum = a + b;
                  table[sum]++;
              }
          }
          int ret = 0;
          for (int c : C) {
              for (int d : D) {
                  int sum = c + d;
                  unordered_map<int, int>::iterator it = table.find(-sum);
                  if (it != table.end()) {
                      ret += it->second;
                  }
              }
          }
          return ret;
      }
  };
```
