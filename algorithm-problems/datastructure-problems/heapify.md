# Heapify

_update Aug 23, 2017 23:29_

[LintCode](http://www.lintcode.com/en/problem/heapify/)

Given an integer array, heapify it into a **min-heap** array.

For a heap array A, A\[0\] is the root of heap, and for each `A[i]`, `A[i * 2 + 1]` is the left child of`A[i]` and `A[i * 2 + 2]` is the right child of `A[i]`.

**Clarification**

What is heap?

> Heap is a data structure, which usually have three methods: push, pop and top. where "push" add a new element the heap, "pop" delete the minimum/maximum element in the heap, "top" return the minimum/maximum element.

What is heapify?

> Convert an unordered integer array into a heap array. If it is min-heap, for each element `A[i]`, we will get `A[i * 2 + 1] >= A[i]` and `A[i * 2 + 2] >= A[i]`.

What if there is a lot of solutions?

> Return any of them.

**Example**

```text
Given [3,2,1,4,5], return [1,2,3,4,5] or any legal heap array.
```

**Challenge**  O\(n\) time complexity

## Basic Idea:

[这里](https://www.youtube.com/watch?v=ixdWTKWSz7s) 有一个youtube上面的视频，说得很好。

基本思路就是从倒数第二层最右边有孩子的节点开始（`idex = len(heap) / 2`），往下执行heapify。这样因为下面多数的node都只需要执行很少的层数，而上面执行深度大的node数量又少，所以整体而言整个建堆的时间复杂度为O\(n\);

## Python Code:

```python
    class Solution:
        # @param A: Given an integer array
        # @return: void
        def heapify(self, A):
            def _heapify_(i):
                left = getLeft(i)
                right = getRight(i)
                minIdx = i
                if left < len(A) and A[left] < A[minIdx]:
                    minIdx = left
                if right < len(A) and A[right] < A[minIdx]:
                    minIdx = right
                if minIdx == i:
                    return
                else:
                    swap(minIdx, i)
                    _heapify_(minIdx)

            def getLeft(i):
                return i * 2 + 1

            def getRight(i):
                return i * 2 + 2

            def swap(i, j):
                t = A[i]
                A[i] = A[j]
                A[j] = t

            for i in range(len(A) / 2 + 1, -1, -1):
                _heapify_(i)
```

## Java Code:

```java
    public class Solution {
        /**
         * @param A: Given an integer array
         * @return: void
         */
        public void heapify(int[] A) {
            // min-heap
            for (int i = A.length / 2 + 1; i >= 0; i--) {
                heapify(A, i);
            }
        }
        private void heapify(int[] A, int index) {
            int left = getLeft(index);
            int right = getRight(index);
            int min = index;
            if (left < A.length && A[left] < A[min]) min = left;
            if (right < A.length && A[right] < A[min]) min = right;
            if (min == index) return;
            else {
                swap(A, min, index);
                heapify(A, min);
            }
        }
        private void swap(int[] arr, int i, int j) {
            int t = arr[i];
            arr[i] = arr[j];
            arr[j] = t;
        } 
        private int getLeft(int i) {
            return i * 2 + 1;
        }
        private int getRight(int i ) {
            return i * 2 + 2;
        }
    }
```

_update Nov 22, 2018_

## Update: 略有修改的写法

这次的实现方法采用了直接换位置的办法，在处理root，left和right三者大小关系的时候。

```java
public class Solution {
    /*
     * @param A: Given an integer array
     * @return: nothing
     */
    public void heapify(int[] A) {
        for (int i = A.length / 2 - 1; i >= 0; --i) {
            heapify(A, i);
        }
    }

    private void heapify(int[] nums, int root) {
        if (root >= nums.length) return;
        int left = getLeft(root);
        int right = getRight(root);
        if (left < nums.length) {
            if (nums[root] > nums[left]) {
                swap(nums, root, left);
                heapify(nums, left);
            }
        } 
        if (right < nums.length) {
            if (nums[root] > nums[right]) {
                swap(nums, root, right);
                heapify(nums, right);
            }
        }
    }

    private void swap(int[] nums, int i, int j) {
        int t = nums[i];
        nums[i] = nums[j];
        nums[j] = t;
    }

    private int getLeft(int i) {
        return i * 2 + 1;
    }

    private int getRight(int i) {
        return i * 2 + 2;
    }
}
```

