# Memory Management
_2017-11-14 23:18:54_

---
### Memory 的作用
1. **Internal tables:**  
  1. **File descriptors:** Describe how to communicate with devices;
  2. **Process/thread descriptors:** Describe how to run a process;
  3. **Semaphores and mutexes:** Describe the locations of shared memory;
  4. **File cache:** Describe what is on disk;
2. **Application memory:**
  1. **private:** for this application only;
  2. **shared:** (e.g., between threads);
  3. **copy on write:** when forking;
  
### Tiers of memory:
<img src="/assets/Screen Shot 2017-11-15 at 12.41.53 AM.png" width="510" height="200" />
<br>

### 命名
<img src="/assets/Screen Shot 2017-11-15 at 12.59.43 AM.png" width="600" height="500" />
<br>
**Page:** page 是 process's version of the memory;
**Frame:** frame 是 os's view of the memory;






  
  
