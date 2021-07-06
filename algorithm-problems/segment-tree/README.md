---
description: 'Jul 6, 2021'
---

# Segment Tree

线段树对我来说很长时间以来都是一种传说中的数据结构，一直没有狠下心来学习，趁着这次准备面试就找来视频啃了一下，发现其实并不难。这充分说明了一个道理，很多困难都是纸老虎。下面就简单说一下这种数据结构。

### 简介

线段树简单来说就是一种平衡二叉树，并且支持任意节点值的update以及range query操作。其中构造数据结构的时间复杂度为`O(N)` ，update时间为 `O(logN)`, range query 为 `O(logN + K)` 其中k为过程中需要access的总节点个数，下面详细说。整个basic idea其实就是类似于之前上学时候学到的CLRS中的Augumented Tree，即在tree的基础上增加一些数据，同时在更新的过程中进行update以及aggregate。

一般来说使用这种数据结构时候的输入为一个数组，其中需要aggregated的值可以是每一段区间的sum，min或者max。rangeQuery的输入为组中的一个index区间。

### 基本操作解释

以下使用 [Range Sum Query - Mutable](https://leetcode.com/problems/range-sum-query-mutable/) 的代码进行解释，这里存储的值是每个区间的sum。

#### 1. TreeNode 定义

```java
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
```

#### 2. 建🌲 construct

每次将当前区间一分为二，左右两边分别构造，然后aggregate左右两边的value，构造当前节点。

```java
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
```

### 3. update

依据输入target index和当前node左右边界比较，递归向左右进行update，注意update结束之后aggregate更新当前node的值。

```java
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
            // 此处无需判断null，因为保证完全二叉树
            root.sum = root.left.sum + root.right.sum;
        }
```

### 4. range query

依据输入的区间范围于当前root的范围以及mid进行比较，递归向下进行query操作。需要注意的是当输入区间的start和end恰好和当前node相同时我们就不必继续向下递归，总的时间复杂度为 `O(logN + k)` ，k为一共需要access的节点数量。

```java
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
```

