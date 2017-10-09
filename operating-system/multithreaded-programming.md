## Multithreaded Programming
_update 2017-10-09 19:25:34_

---
#### 基本思路
1.  Identify **critical section** in which undesirable schedules exist;
2.  Surround critical section with **mutual exclusive locks**;
3.  **Minimize** the time spent locked in critical sections;

判断是否 critical section 不是经验性的而是确定性的，根据 assembly language 的程序结构。

