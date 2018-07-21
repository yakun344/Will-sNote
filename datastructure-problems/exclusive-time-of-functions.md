## Exclusive Time of Functions
_update Aug 14,2017  23:11_

---
[LeetCode](https://leetcode.com/problems/exclusive-time-of-functions/description/)

Given the running logs of n functions that are executed in a nonpreemptive single threaded CPU, find the exclusive time of these functions.

Each function has a unique id, start from `0` to `n-1`. A function may be called recursively or by another function.

A log is a string has this format : function_id:start_or_end:timestamp. For example, `"0:start:0"` means function 0 starts from the very beginning of time 0. `"0:end:0"` means function 0 ends to the very end of time 0.

Exclusive time of a function is defined as the time spent within this function, the time spent by calling other functions should not be considered as this function's exclusive time. You should return the exclusive time of each function sorted by their function id.

**Example 1:**

        Input:
        n = 2
        logs =
        ["0:start:0",
         "1:start:2",
         "1:end:5",
         "0:end:6"]
        Output:[3, 4]
Explanation:

Function 0 starts at time 0, then it executes 2 units of time and reaches the end of time 1.
Now function 0 calls function 1, function 1 starts at time 2, executes 4 units of time and end at time 5.
Function 0 is running again at time 6, and also end at the time 6, thus executes 1 unit of time.
So function 0 totally execute 2 + 1 = 3 units of time, and function 1 totally execute 4 units of time.

**Note:**

*  Input logs will be sorted by timestamp, NOT log id.
*  Your output should be sorted by function id, which means the 0th element of your output corresponds to the exclusive time of function 0.
*  Two functions won't start or end at the same time.
*  Functions could be called recursively, and will always end.
`1 <= n <= 100`

#### Basic Idea:
题目是要求每个task自身所消耗的时间，不包括它调用其他函数所消耗的时间。但是可以call 自身，也就是递归，而递归的时间不重复计算。即如果task 0 从 0 开始，5 结束，期间无论调用多少次自身，最终 task 0 的绝对时间都是 6 (5-0+1)。**另外**，log本身是按照end的时间排序。

基本思想是使用stack存放当前开始了还未结束的 task ID，按照读入的log逐个增加计算当前栈顶ID所对应的时间。
**例如：**
```
        "0:start:0",--------- 任务0开始，prevTime = 0，stack.push(0)

        "0:start:1",--------- 任务0开始，任务0时间+1=1，prevTime = 1, stack.push(0)

        "0:end:2",  --------- 任务0终止，任务0时间+2=3，prevTime = 3, stack.pop()

        "1:start:4",--------- 任务1开始，任务0时间+1=4，prevTime = 4，stack.push(1)

        "1:end:5",  --------- 任务1结束，任务1时间+2=2, prevTime = 6, stack.pop()

        "0:end:6"   --------- 任务0结束,任务0时间+1=5，prevTime = 7，stack.pop()

        return [5, 2],结束
```

#### Python Code:
```python
    class Solution(object):
        def exclusiveTime(self, n, logs):
            """
            :type n: int
            :type logs: List[str]
            :rtype: List[int]
            """
            def getInfo(log):
                lst = log.split(':')
                ret = [-1, False, -1]
                ret[0] = int(lst[0])
                ret[1] = True if lst[1] == 'start' else False
                ret[2] = int(lst[2])
                return ret

            res = [0] * n
            stack = []
            prevTime = 0
            for log in logs:
                currInfo = getInfo(log)
                # if current log is a start
                if currInfo[1]:
                    if stack:
                        # 加上prevTime到这次开始之前的时间，不包括这次开始
                        res[stack[-1]] += currInfo[2] - prevTime
                    prevTime = currInfo[2]
                    stack.append(currInfo[0])
                # if curr is end
                else:
                    # 加上prevTime到这次结束，包括这次结束，所以再 + 1
                    res[stack.pop()] += currInfo[2] - prevTime + 1
                    # 这次的时间只属于这次stack，之后的从下一秒开始
                    prevTime = currInfo[2] + 1
            return res
```

---
_update 2018-07-21 10:40:57_

#### Update Java Solution
基本思路就是利用一个stack存放当前尚未结束的function，然后遍历log，将每两个时间点之间的时间分配给合适的function。另外要注意，start时候是在该点的初始，end的时候是在该点的结尾，所以end的时候，nextTimePoint更新为 当前时间 + 1;

```java
class Solution {
    public int[] exclusiveTime(int n, List<String> logs) {
        int[] ret = new int[n];
        Deque<Integer> stack = new ArrayDeque<>();
        int lastTimePoint = 0;
        for (String log : logs) {
            int[] parsedLog = parse(log);
            if (parsedLog[1] == 0) {
                if(! stack.isEmpty()) ret[stack.peekLast()] += parsedLog[2] - lastTimePoint;
                lastTimePoint = parsedLog[2];
                stack.offerLast(parsedLog[0]);
            } else {
                ret[stack.peekLast()] += parsedLog[2] - lastTimePoint + 1;
                lastTimePoint = parsedLog[2] + 1;
                stack.pollLast();
            }
        }
        return ret;
    }

    // [id, (0 or 1), time], 1 表示 start，0 表示 end
    private int[] parse(String log) {
        String[] arr = log.split(":");   
        int[] ret = new int[3];
        ret[0] = Integer.parseInt(arr[0]);
        ret[1] = arr[1].equals("start") ? 0 : 1;
        ret[2] = Integer.parseInt(arr[2]);
        return ret;
    }
}
```
