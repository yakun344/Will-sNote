# Top K Trade Value Stock

_update Nov 22, 2018_

K most traded stocks。设计一个class, 有两个函数。函数一logger，记录下输入的股票代码和交易额。函数二，返回交易额最大的k支股票。

**思路：**

使用如下数据结构：

```java
Map<StockId, Val> idToValMap;
TreeMap<val, Set<stockId>> valToIdTreeMap;
```

可以做到insert每次都是 `log(#val)` 的时间复杂度，查找k个当前最大时也是 `log(#val)`;

## Java Code:

```java
// input: stock id: int id; trade val: int val;
// output: k largest val stock id

class Logger {    
    private Map<Integer, Integer> idToValMap;
    private TreeMap<Integer, Set<Integer>> valToIdTreeMap;
    public Logger() {
        idToValMap = new HashMap<>();
        valToIdTreeMap = new TreeMap<>((a, b)->Integer.compare(b, a));
    }

    public void updateStockVal(int stockId, int val) {
        Integer prevVal = idToValMap.get(stockId);
        if (prevVal == null) {
            idToValMap.put(stockId, val);
            addToValToIdMap(stockId, val);
        } else {
            idToValMap.put(stockId, val);
            Set<Integer> set = valToIdTreeMap.get(prevVal);
            set.remove(stockId);
            if (set.isEmpty()) valToIdTreeMap.remove(prevVal);
            addToValToIdMap(stockId, val);
        }
    }

    private void addToValToIdMap(int stockId, int val) {
        Set<Integer> set = valToIdTreeMap.get(val);
        if (set == null) {
            Set<Integer> temp = new HashSet<>();
            temp.add(stockId);
            valToIdTreeMap.put(val, temp);
        } else {
            set.add(stockId);
        }
    }

    public List<Integer> getK(int k) {
        List<Integer> res = new ArrayList<>();
        Iterator<Map.Entry<Integer, Set<Integer>>> it = valToIdTreeMap.entrySet().iterator();
        Label:
        while (true) {
            if (! it.hasNext()) break;
            Set<Integer> set = it.next().getValue();
            for (int id : set) {
                if (res.size() == k) break Label;
                res.add(id);
            }
        }
        return res;
    }
}

// "static void main" must be defined in a public class.
public class Main {
    public static void main(String[] args) {
        Logger logger = new Logger();
        logger.updateStockVal(1, 10);
        logger.updateStockVal(2, 13);
        logger.updateStockVal(1, 15);
        logger.updateStockVal(2, 12);
        logger.updateStockVal(4, 12);
        logger.updateStockVal(2, 14);
        logger.updateStockVal(6, 11);
        logger.updateStockVal(3, 19);
        logger.updateStockVal(1, 18);
        logger.updateStockVal(4, 10);
        List<Integer> lst = logger.getK(3);
        for (int id : lst) System.out.print(id + " ");
    }
}
```

