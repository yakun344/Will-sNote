# Design Log Storage System

_update Jan 27, 2018 15:25_

[LeetCode](https://leetcode.com/problems/design-log-storage-system/description/)

You are given several logs that each log contains a unique id and timestamp. Timestamp is a string that has the following format: `Year:Month:Day:Hour:Minute:Second`, for example, `2017:01:01:23:59:59`. All domains are zero-padded decimal numbers.

Design a log storage system to implement the following functions:

`void Put(int id, string timestamp)`: Given a log's unique id and timestamp, store the log in your storage system.

`int[] Retrieve`\(String start, String end, String granularity\): Return the id of logs whose timestamps are within the range from start to end. Start and end all have the same format as timestamp. However, granularity means the time level for consideration. For example, `start = "2017:01:01:23:59:59"`, `end = "2017:01:02:23:59:59"`, `granularity = "Day"`, it means that we need to find the logs within the range from Jan. 1st 2017 to Jan. 2nd 2017.

**Example 1:**

```java
put(1, "2017:01:01:23:59:59");
put(2, "2017:01:01:22:59:59");
put(3, "2016:01:01:00:00:00");
retrieve("2016:01:01:01:01:01","2017:01:01:23:00:00","Year"); // return [1,2,3], because you need to return all logs within 2016 and 2017.
retrieve("2016:01:01:01:01:01","2017:01:01:23:00:00","Hour"); // return [1,2], because you need to return all logs start from 2016:01:01:01 to 2017:01:01:23, where log 3 is left outside the range.
```

**Note:**

1. There will be at most 300 operations of Put or Retrieve.
2. Year ranges from \[2000,2017\]. Hour ranges from \[00,23\].
3. Output for Retrieve has no order required.

## Basic Idea:

其实设计一个这样的 system 并非易事，而对于这道题我们其实可以进行简化。题目只是要求我们记录每条 log，并且可以根据不同的精确度和时间界线返回某一段时间范围内的 log。

具体地，我们在这里只是用一个 `List<Stirng[]>` 来存储每条log，在 retrieve 的时候我们扫描一遍 list，拿出符合要求的log。在比较的时候如何处理不同的精确度要求呢？其实我们只要限制参与比较的字符串长度就好，例如如果精确度为 Year，我们只需要比较前四位长度的 string 就可以了。

* **Java Code:**

  ```java
    class LogSystem {
        private List<String[]> list = new ArrayList<>();
        private Map<String, Integer> indexMap = new HashMap<>();
        public LogSystem() {
            indexMap.put("Year", 4);
            indexMap.put("Month", 7);
            indexMap.put("Day", 10);
            indexMap.put("Hour", 13);
            indexMap.put("Minute", 16);
            indexMap.put("Second", 19);
        }

        public void put(int id, String timestamp) {
            list.add(new String[]{Integer.toString(id), timestamp});
        }

        public List<Integer> retrieve(String s, String e, String gra) {
            List<Integer> res = new ArrayList<>();
            int index = indexMap.get(gra);
            for (String[] log : list) {
                String partToCompare = log[1].substring(0, index);
                if ((partToCompare.compareTo(s.substring(0, index)) >= 0) && 
                    (partToCompare.compareTo(e.substring(0, index))) <= 0) {
                    res.add(Integer.valueOf(log[0]));
                }
            }
            return res;
        }
    }
  ```

