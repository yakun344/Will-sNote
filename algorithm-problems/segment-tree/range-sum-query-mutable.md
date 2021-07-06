---
description: 'Jul 5, 2021'
---

# Range Sum Query - Mutable

[Leetcode](https://leetcode.com/problems/range-sum-query-mutable/)

Given an integer array `nums`, handle multiple queries of the following types:

1. **Update** the value of an element in `nums`.
2. Calculate the **sum** of the elements of `nums` between indices `left` and `right` **inclusive** where `left <= right`.

Implement the `NumArray` class:

* `NumArray(int[] nums)` Initializes the object with the integer array `nums`.
* `void update(int index, int val)` **Updates** the value of `nums[index]` to be `val`.
* `int sumRange(int left, int right)` Returns the **sum** of the elements of `nums` between indices `left` and `right` **inclusive** \(i.e. `nums[left] + nums[left + 1] + ... + nums[right]`\).

**Example 1:**

```text
Input
["NumArray", "sumRange", "update", "sumRange"]
[[[1, 3, 5]], [0, 2], [1, 2], [0, 2]]
Output
[null, 9, null, 8]

Explanation
NumArray numArray = new NumArray([1, 3, 5]);
numArray.sumRange(0, 2); // return 1 + 3 + 5 = 9
numArray.update(1, 2);   // nums = [1, 2, 5]
numArray.sumRange(0, 2); // return 1 + 2 + 5 = 8
```

**Constraints:**

* `1 <= nums.length <= 3 * 104`
* `-100 <= nums[i] <= 100`
* `0 <= index < nums.length`
* `-100 <= val <= 100`
* `0 <= left <= right < nums.length`
* At most `3 * 104` calls will be made to `update` and `sumRange`.

### Basic Idea:

这道题目就是典型的线段树的应用，利用线段树可以支持此题中所要求的update和range sum query操作，时间复杂度为 `O(logN)` update, `O(logN + k)` query.

### Java Code:

```java
class NumArray {
    // 实现线段🌲
    private static class SegmentTree {
        static class TreeNode {
            int start, end, sum;
            TreeNode left, right;

            TreeNode(int start, int end, int sum, TreeNode left, TreeNode right) {
                this.start = start;
                this.end = end;
                this.sum = sum;
                this.left = left;
                this.right = right;
            }
        }
        
        private TreeNode root;
        
        SegmentTree(int[] nums) {
            this.root = construct(nums, 0, nums.length - 1);
        }
        
        TreeNode construct(int[] nums, int start, int end) {
            if (start == end) {
                return new TreeNode(start, end, nums[start], null, null);
            }
            int mid = (start + end) / 2;
            TreeNode left = construct(nums, start, mid);
            TreeNode right = construct(nums, mid + 1, end);
            TreeNode curr = new TreeNode(start, end, left.sum + right.sum, left, right);
            return curr;
        }
        
        void update(TreeNode root, int index, int val) {
            int start = root.start;
            int end = root.end;
            if (start == index && end == index) {
                root.sum = val;
                return;
            }
            int mid = (start + end) / 2;
            if (index <= mid) {
                update(root.left, index, val);
            } else if (index > mid) {
                update(root.right, index, val);
            }
            // 此处无需判断null，因为保证满二叉树
            root.sum = root.left.sum + root.right.sum;
        }
        
        int sumRange(TreeNode root, int start, int end) {
            if (root.start == start && root.end == end) {
                return root.sum;
            }
            int mid = (root.start + root.end) / 2;
            if (end <= mid) {
                return sumRange(root.left, start, end);
            } else if (start > mid) {
                return sumRange(root.right, start, end);
            } else {
                return sumRange(root.left, start, mid) 
                    + sumRange(root.right, mid + 1, end);
            }
        }
        
        void update(int index, int val) {
            update(root, index, val);
        }
        
        int sumRange(int left, int right) {
            return sumRange(root, left, right);
        }
    }
    
    private SegmentTree tree;
    
    public NumArray(int[] nums) {
        this.tree = new SegmentTree(nums);
    }
    
    public void update(int index, int val) {
        tree.update(index, val);
    }
    
    public int sumRange(int left, int right) {
        return tree.sumRange(left, right);
    }
}

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray obj = new NumArray(nums);
 * obj.update(index,val);
 * int param_2 = obj.sumRange(left,right);
 */
```

