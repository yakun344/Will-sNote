# Multithreaded Programming

_update 2017-10-09 19:25:34_

## 基本思路

1. Identify **critical section** in which undesirable schedules exist;
2. Surround critical section with **mutual exclusive locks**;
3. **Minimize** the time spent locked in critical sections;

判断是否 critical section 不是经验性的而是确定性的，根据 assembly language 的程序结构。

使用 mutex 一定会让程序变慢，但是会增加 determinism。

## 关于 Heisenbugs

"Heisenbug" is a bug in a program that is exceedingly difficult to reproduce.

这种bug无法再debug中观察，只能理论分析code。

