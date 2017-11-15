# Virtual Memory
_update 2017-11-15 11:00:15_

---
### Memory fragmentation:
只有一部分 heap is “used”。process中每个内存块都需要比实际数据大，没有利用到的内部的空间叫做 "internal fragmentation"。process 之间的在 global memory map 中没利用到的空间叫做 external fragmentation。

Fragmentation 可以由于 fixed page size, buddy system, 和 reclamation（process exit，heap 释放，留下fragment） 造成。我们没有办法 eliminate fragments，因为我们不知道 pointers 在哪里。

### Memory mapping:
* A frame is a unit of memory in the OS;
* A page is a unit of memory  in the process;
* Memory mapping associates (os) frames with (process) pages;
* The OS has to maintain data on what gets mapped where;

* What frame goes with what process/page?
* Special handling instructions for pages: read-only,shared, private,...
<img src="/assets/Screen Shot 2017-11-15 at 11.42.56 AM.png" width="700" height="580" /><br>
<img src="/assets/Screen Shot 2017-11-15 at 12.13.57 PM.png" width="600" height="580" /><br>
<img src="/assets/Screen Shot 2017-11-15 at 12.17.55 PM.png" width="630" height="580" /><br>
<img src="/assets/Screen Shot 2017-11-15 at 12.20.26 PM.png" width="750" height="550" />

### Virtual memory:
![](/assets/Screen Shot 2017-11-15 at 12.26.19 PM.png)

* Process run 的时候只有一部分 pages resident；
* 如果一个page is needed and isn't resident:
  1. block the process
  2. swap in the required page
  3. unblock the process after the page is resident(it returns to the run queue)

如果map时，有 unused frame，则将需要的contents从disk中读到这里，然后map到process page。否则，就将一个已经使用的frame unmap，按需要将其存入disk，然后用这个frame存新的。

**The dirty bit:**  如果memory page 和 disk 中不同，则需要 flush to disk before reuse。

**LRU：**  
Optimal if memory access is linear, and increasing;
**LFU:**  
Optimal for loops;
















