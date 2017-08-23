## Top K Frequent Words
_update Aug 23, 2017  16:33_

---
[LintCode](http://www.lintcode.com/en/problem/top-k-frequent-words/)

Given a list of words and an integer k, return the top k frequent words in the list.

**Notice**

>You should order the words by the frequency of them in the return list, the most frequent one comes first. If two words has the same frequency, the one with lower alphabetical order come first.


**Example**

    Given
    
    [
        "yes", "lint", "code",
        "yes", "code", "baby",
        "you", "baby", "chrome",
        "safari", "lint", "code",
        "body", "lint", "code"
    ]
for k = 3, return ["code", "lint", "baby"].

for k = 4, return ["code", "lint", "baby", "yes"],

**Challenge**
>Do it in O(nlogk) time and O(n) extra space.

>Extra points if you can do it in O(n) time with O(k) extra space approximation algorithms.

#### Basic Idea:
**思路1：bucket sort**
先统计词频，然后建一个数组，大小为 max-min，每个bucket存放该词频的单词，然后从右往左遍历。最终得到的list可能数量大于k，排序后取较大的k个返回即可。

>时间复杂度：如果忽略最后一次按照字母顺序的排序，时间复杂度为O(n);

**思路2：**
同样先用hashmap统计词频，然后维持一个size为k的最小堆，来存放最大词频的k个string。但是这种方法有局限，因为我们除了跟踪词频之外还需要考虑同样词频时按照字母顺序进行取舍。在Java中，我们自然可以通过传入一个comparator解决问题，但是在python中，我暂时还没想到好的办法，因为如果key是string，无法直接取反。

>时间复杂度：统计词频O(n) + 维持pq的操作O(nlogk) + 最终k次poll的O(klogk) = O(nlogk)；

#### Python Code （思路1）：
Bucket sort solution：
```python
    class Solution:
        """
        @param: words: an array of string
        @param: k: An integer
        @return: an array of string
        """
        def topKFrequentWords(self, words, k):
            counter = collections.defaultdict(int)
            for word in words:
                counter[word] += 1
            # 获取bucket范围
            minFreq, maxFreq = float('INF'), float('-INF')
            for word in counter:
                minFreq = min(minFreq, counter[word])
                maxFreq = max(maxFreq, counter[word])
            # 把words存入buckets
            buckets = [None] * (maxFreq - minFreq + 1)
            for word in counter:
                index = counter[word] - minFreq
                if buckets[index] is None:
                    buckets[index] = set()
                buckets[index].add(word)
            # 从右向左，拿k个words，最后一个bucket可能需要排序
            res = []
            for i in range(len(buckets) - 1, -1, -1):
                if buckets[i] is None: continue
                
                # 关键，对每组相同词频的words，按字母顺序进行排序再extend到res 
                
                temp = list(buckets[i])
                temp.sort()
                res.extend(temp)
                if len(res) > k:
                    break
            # 截取k个返回
            return res[:k]
```
#### Java Code (思路2):
Heap q solution:
```java
    public class Solution {
        /*
         * @param words: an array of string
         * @param k: An integer
         * @return: an array of string
         */
        private class QElement {
            int freq;
            String word;
            public QElement(String word, int freq) {
                this.word = word;
                this.freq = freq;
            }
        }
        public String[] topKFrequentWords(String[] words, int k) {
            if (k == 0) return new String[0];
            // 统计词频
            Map<String, QElement> counter = new HashMap<>();
            for (String word : words) {
                if (! counter.containsKey(word)) {
                    counter.put(word, new QElement(word, 0));
                }
                counter.get(word).freq++;
            }
            // 存入pq
            PriorityQueue<QElement> pq = new PriorityQueue<>(k, new Comparator<QElement>(){
                // 1st key 表示是对于freq的min heap
                // 2st key 则表示是对于string字母顺序的max heap（维持字母顺序最小的word的集合）
                @Override
                public int compare(QElement e1, QElement e2) {
                    if (e1.freq != e2.freq) return e1.freq - e2.freq;
                    return e2.word.compareTo(e1.word);
                }
            });
            for (String word : counter.keySet()) {
                pq.offer(counter.get(word));
                if (pq.size() > k) pq.poll();
            }
            // pq中已经是k个频率最大的，poll出来返回
            String[] res = new String[k];
            for (int i = 0; i < k; ++i) {
                res[k - i - 1] = pq.poll().word;
            }
            return res;
        }
    }
```