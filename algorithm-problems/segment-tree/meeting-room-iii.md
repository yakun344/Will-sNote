---
description: 'Jul 4, 2021'
---

# Meeting Room III

[LintCode](https://www.lintcode.com/problem/1897/description?_from=%5B%27ladder%27%5D&fromId=%5B%27154%27%5D)

![](../../.gitbook/assets/image%20%283%29.png)

### 1. Prefix Sum 解法:

这道题的重点是如何快速处理每个ask中的query，即快速得知某一段时间是否available。因为时间的范围并不大，只有50k，我们可以开一个这么大的数组，这样对于每个分钟都可以分别记录状态。

这里我们可以反过来考虑，如果有一个数组标记状态，如何能快速得知一段时间是否OK？我们可以先将所有OK的时间都标记为0，不OK的时间标记为1，然后求prefixSum，这样对于一段时间的左右两端，如果他们之间的时间都OK，则意味着prefixSum在这一段没有变化，我们就可以利用这点快速求解。

然后如何知道某一分钟是否OK？我们可以先对于每个interval标记event，按照start +1，end -1的方法，然后从左到右扫描所有event，可以得到每一分钟需要的最少room数量，然后对于没有空room的分钟标记为1，有空room的分钟标记为0即可。

还有一点，在得到prefixSum之后，判断时间点`[p,q]` 是否OK实际上要判断的是`if (prefixSum[q - 1] == prefixSum[p - 1])` 这里的 `-1` 是因为上一个event结束的同时下一个就可以开始，所以需要1分钟的错位。

#### Java Code：

```java
public class Solution {
    /**
     * @param intervals: the intervals
     * @param rooms: the sum of rooms
     * @param ask: the ask
     * @return: true or false of each meeting
     */
    public boolean[] meetingRoomIII(int[][] intervals, int rooms, int[][] ask) {
        int maxTime = 0;
        int[] events = new int[60000];
        for (int[] interval : intervals) {
            events[interval[0]]++;
            events[interval[1]]--;
            maxTime = Math.max(maxTime, interval[1]);
        }
        int[] prefixSum = new int[60000];
        int roomNeeded = 0;
        for (int i = 0; i <= maxTime; ++i) {
            roomNeeded += events[i];
            if (roomNeeded == rooms) {
                prefixSum[i] = 1;
            } else {
                prefixSum[i] = 0;
            }
        }
        for (int i = 1; i < prefixSum.length; ++i) {
            prefixSum[i] = prefixSum[i - 1] + prefixSum[i];
        }
        boolean[] res = new boolean[ask.length];
        for (int i = 0; i < ask.length; ++i) {
            int start = ask[i][0];
            int end = ask[i][1];
            res[i] = prefixSum[end - 1] == prefixSum[start - 1];
        }
        return res;
    }
}
```

### 2. 线段树解法

我们可以利用线段树的性质，对于这道题目而言，我们可以在线段树中维持每个区间需要的最小房间数量。对于一个大区间，它所存放的是左右两个小区间的所需房间数量的最大值。所以事实上这里所需要的是一个维持maxValue的线段树。

#### Java Code

```java
public class Solution {
    class TreeNode {
        int start, end, roomNeeded;
        TreeNode left, right;

        TreeNode(int start, int end, int roomNeeded, TreeNode left, TreeNode right) {
            this.start = start;
            this.end = end;
            this.roomNeeded = roomNeeded;
            this.left = left;
            this.right = right;
        }
    }

    private TreeNode construct(int[] roomRequirmentArr, int start, int end) {
        if (start == end) return new TreeNode(start, end, roomRequirmentArr[start], null, null);
        int mid = (start + end) / 2;
        TreeNode left = construct(roomRequirmentArr, start, mid);
        TreeNode right = construct(roomRequirmentArr, mid + 1, end);
        return new TreeNode(start, end, Math.max(left.roomNeeded, right.roomNeeded), left, right);
    }

    private void update(TreeNode root, int index, int newRoomNeeded) {
        // this method is not needed in this case
    }

    private int query(TreeNode root, int start, int end) {
        if (root.start == start && root.end == end) return root.roomNeeded;
        int mid = (root.start + root.end) / 2;
        if (end <= mid) {
            return query(root.left, start, end);
        } else if (start > mid) {
            return query(root.right, start, end);
        } else {
            return Math.max(query(root.left, start, mid), query(root.right, mid + 1, end));
        }
    }

    private TreeNode root;

    public boolean[] meetingRoomIII(int[][] intervals, int rooms, int[][] ask) {
        int[] events = new int[50001];
        for (int[] interval : intervals) {
            events[interval[0]]++;
            events[interval[1]]--;
        }
        int[] roomRequirementArr = new int[events.length];
        int count = 0;
        for (int i = 0; i < events.length; ++i) {
            count += events[i];
            roomRequirementArr[i] = count;
        }
        // System.out.println(Arrays.toString(roomRequirementArr));
        this.root = construct(roomRequirementArr, 0, roomRequirementArr.length - 1);
        boolean[] ret = new boolean[ask.length];
        for (int i = 0; i < ask.length; ++i) {
            int[] interval = ask[i];
            // 如果区间内所需房间最大值小于给定房间数量
            if (query(root, interval[0], interval[1] - 1) < rooms) ret[i] = true;
        }
        return ret;
    }
}
```

