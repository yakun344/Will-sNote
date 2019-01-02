# Delete Columns to Make Sorted II
_update Jan 1,28:26 2019_

---
[LeetCode](https://leetcode.com/problems/delete-columns-to-make-sorted-ii/)

We are given an array A of N lowercase letter strings, all of the same length.

Now, we may choose any set of deletion indices, and for each string, we delete all the characters in those indices.

For example, if we have an array `A = ["abcdef","uvwxyz"]` and deletion indices `{0, 2, 3}`, then the final array after deletions is `["bef","vyz"]`.

Suppose we chose a set of deletion indices D such that after deletions, the final array has its elements in lexicographic order `(A[0] <= A[1] <= A[2] ... <= A[A.length - 1])`.

Return the minimum possible value of D.length.

 
**Example 1:**

    Input: ["ca","bb","ac"]
    Output: 1
    Explanation: 
    After deleting the first column, A = ["a", "b", "c"].
    Now A is in lexicographic order (ie. A[0] <= A[1] <= A[2]).
    We require at least 1 deletion since initially A was not in lexicographic order, so the answer is 1.

**Example 2:**

    Input: ["xc","yb","za"]
    Output: 0
    Explanation: 
    A is already in lexicographic order, so we don't need to delete anything.
    Note that the rows of A are not necessarily in lexicographic order:
    ie. it is NOT necessarily true that (A[0][0] <= A[0][1] <= ...)

**Example 3:**

    Input: ["zyx","wvu","tsr"]
    Output: 3
    Explanation: 
    We have to delete every column.
 

**Note:**

    1. 1 <= A.length <= 100
    2. 1 <= A[i].length <= 100

<br/>

### Basic Idea:
* #### `O(N*L^2)` Solution
  我们可以从左到右逐个考虑每一列，看能否将这一列加入结果中，而非判断要删除哪些列。如此一来，我们就有一个想法，即初始化一个String array，起初每个元素都是空字符串，每次往里插入当前列的字母，检查是否仍然有序，如果有序，就保留当前列。例如输入`"abc","bcd"`, 那么我们先把第一列插入，变成`"a","b"`，检查发现有序，则保留，再插入第二列，继续判断。时间复杂度为`O(N*L^2)`，因为检查是否排序耗时`O(NL)`, 一共需要检查L次 （其中N为String个数，L为长度）。

  * ##### Java Code:
    ```java
    class Solution {
        public int minDeletionSize(String[] A) {
            String[] arr = new String[A.length];
            Arrays.fill(arr, "");
            for (int c = 0; c < A[0].length(); ++c) {
                String[] curr = Arrays.copyOf(arr, arr.length);
                for (int r = 0; r < A.length; ++r) {
                    curr[r] += A[r].charAt(c);
                }
                if (sorted(curr)) arr = curr;
            }
            return A[0].length() - arr[0].length();
        }
        
        private boolean sorted(String[] arr) {
            for (int i = 0; i < arr.length - 1; ++i) {
                if (arr[i].compareTo(arr[i + 1]) > 0) return false;
            }
            return true;
        }
    }
    ```

* #### `O(N*L)` Solution
  上面的解法之所以比较慢，是因为我们每次检查插入当前列之后是否有序都需要检查包括前面列在内的部分，导致这一步耗时 `O(N*L)`，如果我们把这一步优化为 `O(N)`，那么总复杂度就会变成 `O(N*L)`。如何优化呢？我们希望只比较当前列。例如输入 `"axx","ayy","bee","bff"`，到了第二列，我们只需要分别比较a开头的xy和b开头的ef。所以我们可以用一个boolean数组标记不相等的row，例如扫描第一列时，到了 `a<b`，我们就标记 `stop[1]=true`，这样等到了第二列，我们就不需要考虑 `y < e ?` 的问题了。

  * ##### Java Code:
    ```java
    class Solution {
        public int minDeletionSize(String[] A) {
            int ret = 0;
            boolean[] stop = new boolean[A.length - 1];
            OUTER:
            for (int c = 0; c < A[0].length(); ++c) {
                for (int r = 0; r < A.length - 1; ++r) {
                    if (! stop[r] && A[r].charAt(c) > A[r + 1].charAt(c)) {
                        ret++;
                        continue OUTER;
                    }
                }
                // 更新 stop
                for (int r = 0; r < A.length - 1; ++r) {
                    if (A[r].charAt(c) < A[r + 1].charAt(c)) stop[r] = true;
                }
            }
            return ret;
        }
    }
    ```
