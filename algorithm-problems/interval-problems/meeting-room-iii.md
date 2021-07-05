---
description: 'Jul 4, 2021'
---

# Meeting Room III

[LintCode](https://www.lintcode.com/problem/1897/description?_from=%5B%27ladder%27%5D&fromId=%5B%27154%27%5D)

![](../../.gitbook/assets/image%20%283%29.png)

### Basic Idea:

这道题的重点是如何快速处理每个ask中的query，即快速得知某一段时间是否available。因为时间的范围并不大，只有50k，我们可以开一个这么大的数组，这样对于每个分钟都可以分别记录状态。

这里我们可以反过来考虑，如果有一个数组标记状态，如何能快速得知一段时间是否OK？我们可以先将所有OK的时间都标记为0，不OK的时间标记为1，然后求prefixSum，这样对于一段时间的左右两端，如果他们之间的时间都OK，则意味着prefixSum在这一段没有变化，我们就可以利用这点快速求解。

然后如何知道某一分钟是否OK？我们可以先对于每个interval标记event，按照start +1，end -1的方法，然后从左到右扫描所有event，可以得到每一分钟需要的最少room数量，然后对于没有空room的分钟标记为1，有空room的分钟标记为0即可。

### Java Code：

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

