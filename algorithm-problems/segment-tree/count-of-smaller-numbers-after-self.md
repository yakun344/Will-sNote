---
description: 'Jul 8, 2021'
---

# Count of Smaller Numbers After Self

[Leetcode](https://leetcode.com/problems/count-of-smaller-numbers-after-self/)

You are given an integer array `nums` and you have to return a new `counts` array. The `counts` array has the property where `counts[i]` is the number of smaller elements to the right of `nums[i]`.

**Example 1:**

```text
Input: nums = [5,2,6,1]
Output: [2,1,1,0]
Explanation:
To the right of 5 there are 2 smaller elements (2 and 1).
To the right of 2 there is only 1 smaller element (1).
To the right of 6 there is 1 smaller element (1).
To the right of 1 there is 0 smaller element.
```

**Example 2:**

```text
Input: nums = [-1]
Output: [0]
```

**Example 3:**

```text
Input: nums = [-1,-1]
Output: [0,0]
```

**Constraints:**

* `1 <= nums.length <= 105`
* `-104 <= nums[i] <= 104`

### Merge Sort 解法：

在sort的过程中，每次当我们merge两个part的时候，我们可以记录下对于每个数字，有几个数字差到了他的前面，这样当我们最终完成排序的时候，就可以得到结果。

#### Java Code:

```java
class Solution {
    int[] res;
    
    public List<Integer> countSmaller(int[] nums) {
        this.res = new int[nums.length];
        int[][] arr = new int[nums.length][2];
        for (int i = 0; i < nums.length; ++i) {
            arr[i][0] = nums[i];
            arr[i][1] = i;
        }
        mergeSort(arr, 0, arr.length - 1);
        return Arrays.stream(res).boxed().collect(Collectors.toList());
    }
    
    private void mergeSort(int[][] arr, int p, int r) {
        if (p >= r) return;
        int q = (p + r) / 2;
        mergeSort(arr, p, q);
        mergeSort(arr, q + 1, r);
        merge(arr, p, q, r);
    }
    
    // arr1 在左边，arr2在右边，分别已经排序，需要统计出arr1中每个数字
    // 前面插入了几个arr2中的元素
    private void merge(int[][] arr, int p, int q, int r) {
        if (p >= r) return;
        int[][] arr1 = Arrays.copyOfRange(arr, p, q + 1);
        int[][] arr2 = Arrays.copyOfRange(arr, q + 1, r + 1);
        int i = 0, j = 0;
        int count = 0;
        for (int k = p; k <= r; ++k) {
            // System.out.println(i + "," + j + " " + Arrays.toString(arr1));
            if (i >= arr1.length) {
                arr[k] = arr2[j++];
                count++;
            } else if (j >= arr2.length) {
                res[arr1[i][1]] += count;
                arr[k] = arr1[i++];
            } else if (arr1[i][0] <= arr2[j][0]) {
                res[arr1[i][1]] += count;
                arr[k] = arr1[i++];
            } else {
                arr[k] = arr2[j++];
                count++;
            }
        }
    }
}
```

### Segment Tree 解法：

我们可以用一个array来存每个值出现过的次数，这样如果想知道小于某个数字的有多少个，可以利用线段树来维持这个array的sum，对于一个数字num，我们只需要query `[-inf, num-1]` 有多少个即可。

接下来有三个细节需要注意：

1. 其一是原始数据范围是`[-10000,10000]` ，而线段树需要从0开始，对此我们可以使用一个OFFSET来解决，给所有num都加上OFFSET。
2. 其二就是题目要求的是每个数字右边比他小的，而不是全局的，对此我们可以利用线段树logN update的性质，从右往左边update边query，这样每次query到的比当前num小的数字就都是它右边的了。
3. 最后就是在update的时候我们不是直接替换，因为有可能有重复数字。例如原本有两个 3，又来了一个3，update的时候需要变成4。

#### Java Code:

```java
class Solution {
    private static class TreeNode {
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
    
    // 这利用10001而不用10000是为了避免query时候对于0会出现负数
    private final int OFFSET = 10001;
    
    private TreeNode root;
    
    private TreeNode construct(int[] arr, int start, int end) {
        if (start == end) {
            return new TreeNode(start, start, arr[start], null, null);
        }
        int mid = (start + end) / 2;
        TreeNode left = construct(arr, start, mid);
        TreeNode right = construct(arr, mid + 1, end);
        return new TreeNode(start, end, left.sum + right.sum, left, right);
    }
    
    // update的时候需要注意不是替换当前val，而是需要加上，因为需要处理重复数字
    private void update(TreeNode root, int index, int val) {
        if (root.start == root.end && root.start == index) {
            root.sum += val;
            return;
        }
        int mid = (root.start + root.end) / 2;
        if (index <= mid) update(root.left, index, val);
        else update(root.right, index, val);
        root.sum = root.left.sum + root.right.sum;
    }
    
    private int query(TreeNode root, int start, int end) {
        if (root.start == start && root.end == end) {
            return root.sum;
        }
        int mid = (root.start + root.end) / 2;
        if (end <= mid) {
            return query(root.left, start, end); 
        } else if (start > mid) {
            return query(root.right, start, end);
        } else {
            return query(root.left, start, mid) + query(root.right, mid + 1, end);
        }
    }
    
    public List<Integer> countSmaller(int[] nums) {
        // 本来是-1e4到1e4范围，向右移动1e4 OFFSET
        int[] arr = new int[20002];
        // 先新建一个全部为0的线段树
        this.root = construct(arr, 0, arr.length - 1);
        
        int MIN = 0;
        List<Integer> res = new ArrayList<>();
        
        // 从右往左，先query所有小于当前num的个数，然后加入当前num，这样就只会考虑到num右边的数字
        for (int i = nums.length - 1; i >= 0; --i) {
            res.add(query(this.root, MIN, nums[i] - 1 + OFFSET));
            update(this.root, nums[i] + OFFSET, 1);
        }
        Collections.reverse(res);
        return res;
    }
}
```

