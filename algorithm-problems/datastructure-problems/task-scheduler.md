# Task Scheduler

_update Aug 15,2017 0:00_

[LeetCode](https://leetcode.com/problems/task-scheduler/description/)

Given a char array representing tasks CPU need to do. It contains capital letters A to Z where different letters represent different tasks.Tasks could be done without original order. Each task could be done in one interval. For each interval, CPU could finish one task or just be idle.

However, there is a non-negative cooling interval n that means between two same tasks, there must be at least n intervals that CPU are doing different tasks or just be idle.

You need to return the **least** number of intervals the CPU will take to finish all the given tasks.

**Example 1:**

```text
Input: tasks = ['A','A','A','B','B','B'], n = 2
Output: 8
Explanation: A -> B -> idle -> A -> B -> idle -> A -> B.
```

**Note:** The number of tasks is in the range \[1, 10000\]. The integer n is in the range \[0, 100\].

## Basic Idea:

首先明确一点，cpu总时间其实和每个任务的id无关，只和每种任务的数量有关。我们为了达到最小操作周期的目的，必须在每个最小周期（n + 1）结束之后马上执行当前剩余最多的任务。

**思路1：计算idel周期** 这个思路来自[这里](https://discuss.leetcode.com/topic/92852/concise-java-solution-o-n-time-o-26-space)。

基本思想就是先统计每种任务的个数，然后排序。用出现最多的任务作为基本单元，对时间进行划分。例如最多的任务 A 出现了四次，n = 2，则我们划分之后为 `A__A__A__A`，然后我们可以把其他的任务填入空当。如图所示： ![](../../.gitbook/assets/wechatimg25%20%281%29.jpg)

java Code：

```java
    public class Solution {
        public int leastInterval(char[] tasks, int n) {
            int[] map = new int[26];
            for (char c : tasks) {
                map[c - 'A']++;
            }
            Arrays.sort(map);
            int i = 25;
            while (i >= 0 && map[i] == map[25]) i--;
            return Math.max(tasks.length, (map[25] - 1) * (n + 1) + 25 - i);
        }
    }
```

**思路2：使用 Priority Queue** 利用一个priority queue，实现在每个周期中，依次选择剩余数量更多的几个task。细节直接看code，应该可以看懂。

**---特别注意：**python中使用 max heap queue 不方便，可以直接把存入的元素变为一个tuple\[2\]，其中\[0\]是元素值的相反数，\[1\]是其本身。 例如：想要将 3 存入，则存入 \(-3, 3\)，heapq会自动将\[0\]位作为key。

Java Code:

```java
    public class Solution {
        public int leastInterval(char[] tasks, int n) {
            PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
            // 统计每种任务的个数，存入pq
            int[] map = new int[26];
            for (char c : tasks) {
                map[c - 'A']++;
            }
            for (int num : map) {
                if (num > 0) pq.offer(num);
            }
            // 每个周期中，依次执行当前数量最多的几个任务。操作方法就是将任务poll之后，剩余数量存入list，
            // 周期结束之后再把list中剩余的任务offer。当list和pq都空时结束。
            // 每个周期长度为 n+1
            int ret = 0;
            while (! pq.isEmpty()) {
                int time = 1;
                List<Integer> temp = new ArrayList<>();
                while (time <= n + 1) {
                    if (! pq.isEmpty()) {
                        int remain = pq.poll() - 1;
                        if (remain > 0) temp.add(remain);
                    }
                    ret++; // 更新总步数统计
                    time++; // 更新当前周期内的步数
                    if (pq.isEmpty() && temp.isEmpty()) {
                        // 结束
                        break;
                    }
                }
                pq.addAll(temp);
            }
            return ret;
        }
    }
```

Python Code:

```python
    class Solution(object):
        def leastInterval(self, tasks, n):
            """
            :type tasks: List[str]
            :type n: int
            :rtype: int
            """
            counter = [0] * 26
            for task in tasks:
                counter[ord(task) - ord('A')] += 1

            # push all numbers in pq
            pq = [(-a, a) for a in counter if a > 0]
            heapq.heapify(pq)

            # 每个周期中，一次从数量最多的task开始选择，剩余的存入list，周期结束后push回pq
            ret = 0
            while pq:
                lst = []
                time = 1
                while time <= n + 1:
                    if pq:
                        task = heapq.heappop(pq)
                        if task[1] > 1:
                            lst.append((task[0] + 1, task[1] - 1))
                    time += 1
                    ret += 1
                    if not pq and not lst:
                        break
                for task in lst:
                    heapq.heappush(pq, task)
            return ret
```

