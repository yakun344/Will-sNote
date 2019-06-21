# High Five

_update Aug 22, 2017 13:12_

[LintCode](http://www.lintcode.com/en/problem/high-five/)

There are two properties in the node student id and scores, to ensure that each student will have at least 5 points, find the average of 5 highest scores for each person.

**Example**

```text
    Given results = [[1,91],[1,92],[2,93],[2,99],[2,98],[2,97],[1,60],[1,58],[2,100],[1,61]]

    Return  1: 72.40
            2: 97.40
```

## Basic Idea:

首先想到的应该就是使用 priority queue。具体地，为每个 id 维持一个 minheap pq，限定其大小不超过 5。当pq大小达到5时，如果仍有新的 score 读入，则和队列 min 对比，如果比min大，则pop掉当前min，heappush 新的score。最终为每个id算出队列中分数的平均值，存入HashMap；

## Python Code:

```python
    '''
    Definition for a Record
    class Record:
        def __init__(self, id, score):
            self.id = id
            self.score = score
    '''

    class Solution:
        # @param {Record[]} results a list of <student_id, score>
        # @return {dict(id, average)} find the average of 5 highest scores for each person
        # <key, value> (student_id, average_score)
        def highFive(self, results):
            import heapq
            table = collections.defaultdict(list)
            for rec in results:
                id = rec.id
                score = rec.score
                pq = table[id]
                if len(pq) >= 5:
                    if score > pq[0]:
                        heapq.heappop(pq)
                        heapq.heappush(pq, score)
                else:
                    heapq.heappush(pq, score)

            res = {}
            for id in table:
                pq = table[id]
                i = 0
                avg = 0
                while pq and i < 5:
                    avg += heapq.heappop(pq)
                    i += 1
                res[id] = (avg + 0.0) / i
            return res
```

## Java Code:

```java
    /**
     * Definition for a Record
     * class Record {
     *     public int id, score;
     *     public Record(int id, int score){
     *         this.id = id;
     *         this.score = score;
     *     }
     * }
     */
    public class Solution {
        /**
         * @param results a list of <student_id, score>
         * @return find the average of 5 highest scores for each person
         * Map<Integer, Double> (student_id, average_score)
         */
        public Map<Integer, Double> highFive(Record[] results) {
            Map<Integer, PriorityQueue<Integer>> map = new HashMap<>();
            for (Record rec : results) {
                int id = rec.id;
                int score = rec.score;
                if (! map.containsKey(id)) map.put(id, new PriorityQueue<Integer>());
                PriorityQueue<Integer> pq = map.get(id);
                if (pq.size() >= 5) {
                    if (pq.peek() < score) {
                        pq.poll();
                        pq.offer(score);
                    }
                } else {
                    pq.offer(score);
                }
            }
            Map<Integer, Double> res = new HashMap<>();
            for (int id : map.keySet()) {
                int i = 0;
                double avg = 0;
                PriorityQueue<Integer> pq = map.get(id);
                while (! pq.isEmpty()) {
                    avg += pq.poll();
                    i += 1;
                }
                res.put(id, avg / i);
            }
            return res;
        }
    }
```

