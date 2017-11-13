# Dead Lock
_update 2017-11-13 11:25:03_

---
### Common forms  
#### 产生原因
1. **Circular dependencies:** processes waiting for one another;
2. **Resource saturation/starvation:** not enough resource for any one to continue; 

#### 两种情形:
1. Deadlock of threads in a process;
2. Deadlock of processes in an operation system;

#### Deadlock schedule:
A sequence of events in time that create a deadlock. Deadlock depends upon **what happens when**, it is often a **chance occurrence**.

#### Completion schedule:
A sequence of events in time that demonstrates that a situation is not a deadlock. A situation **is not a deadlock** if there is any schedule of events under which computation can complete.

#### 基本假设和deadlock
**Assumption:**  
1. 进程所申请的资源都是其需要的资源；
2. 当进程获得所有需要的资源后，他一定会完成并且释放资源，没有 outside influences.

**Deadlock when:**  
进程需要资源，而它们在无 outside intervention(ctrl-d) 时不会结束时。 

#### Deadlock 的产生
**circular dependency**

1. If every process ask for everything it needs all at ones, **deadlock cannot occur.**
2. 产生deadlock的原因是： processes **waits for some resources but already holds others.**

另一种是 **Resource starvation**：

是 circular dependency 的一种特殊情况，对于resource的需求供不应求。多在 incremental allocation 中产生 deadlock。 例如：  
<br>
<img src="/assets/Screen Shot 2017-11-13 at 12.26.26 PM.png" width="410" height="300" />
<br>

#### Important to remember
When a process awaits resources, it not ready or runnable. 因为它被block，它无法在runtime 纠正自己，所有的纠正 has to come from outside.

#### Resource allocation graph
<br>
<img src="/assets/Screen Shot 2017-11-13 at 2.04.32 PM.png" width="410" height="300" />
<br>
<img src="/assets/Screen Shot 2017-11-13 at 2.05.00 PM.png" width="410" height="200" />
<br>

#### Keys to deadlock prevention: Atomicity of allocation
1. Request resources all at once;
2. Avoid resource allocation races;
3. 这就是允许同时做多个semaphore操作的深层原因。














