# 95 Percentile
_update Mar 17, 2018  19:57_

---
Given a list of integers representing the lengths of urls, find the 95 percentile of all lengths (`95%` of the urls have lengths `<=` returned length).

**Assumptions**

The maximum length of valid url is 4096

The list is not null and is not empty and does not contain null

**Examples:**

    [1, 2, 3, ..., 95, 96, 97, 98, 99, 100], 
    95 percentile of all lengths is 95.
    
<br>

## Basic Idea:
利用 Bucket Sort 的思路，先为所有 distinct 的 length 建一个 buckets array，然后统计每个长度对应的 url 的个数。最后从左到右 count url 的个数，直到超过 `0.95 size`，返回当前长度。

* ### Java Code:
```java
  public class Solution {
    public int percentile95(List<Integer> lengths) {
      int min = lengths.get(0), max = lengths.get(0);
      for (int len : lengths) {
        if (len < min) {
          min = len;
        } else if (len > max) {
          max = len;
        }
      }
      
      // create buckets for each distinct length
      // buckets 从 min 开始
      int[] buckets = new int[max - min + 1];
      for (int len : lengths) {
        buckets[len - min]++;
      }
      
      // scan buckets from left to right, 
      // count number until larger or equal to 0.95 * size
      int count = 0;
      for (int i = 0; i < buckets.length; ++i) {
        count += buckets[i];
        if (count >= 0.95 * lengths.size()) {
          return i + min;
        }
      }
      return -1;
    }
  }
```

