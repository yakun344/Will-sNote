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


