## Robin Karp Algorithm
_update Jul 23, 2017 23:39_

---
#### Basic Idea:
利用计算字符串的HashCode进行线性时间匹配。

**具体地：**

HashCode 计算方法：
例如对于字符串“abc”，其hashcode即为 `(31^2 * a + 31^1 * b + 21^0 * c) % 1e6`，其中 1e6 为 BASE，31 为 magic number。

我们先计算 target 的 hashcode，然后用 source 中相同长短子字符串的hashcode与其相比，既可以过滤掉绝大多数不匹配的情况。具体实现中，可以想象用一个长度等于 len(target) 的 window 从左向右移动，每次加入右边字符，减去左边字符，然后和 targetCode比较。


#### Python Code:
```python
    class Solution:
        # @param {string} source a source string
        # @param {string} target a target string
        # @return {int} an integer as index
        
        # Robin Karp
        def strStr2(self, source, target):
            if source is None or target is None:
                return -1
            if not target:
                return 0
            
            m = len(target)
            BASE = 1e6
            # 计算右移window后左边需要减去的hashCode的值的系数
            power = 1
            for i in range(m):
                power = (31 * power) % BASE
            
            # 计算target的hashCode
            targetCode = 0
            for c in target:
                targetCode = (targetCode * 31 + ord(c)) % BASE
                
            # 对source从左到右计算hashCode并匹配
            sourceCode = 0
            for i in range(len(source)):
                sourceCode = (sourceCode * 31 + ord(source[i])) % BASE
                if i <  len(target) - 1:
                    continue
                if i >= len(target):
                    # 此时需要去掉最左边字符的hashCode
                    sourceCode = sourceCode - (power * ord(source[i - m])) % BASE
                    if sourceCode < 0:
                        sourceCode += BASE
                # 判断
                if sourceCode == targetCode:
                    if source[i - m + 1 : i + 1] == target:
                        return i - m + 1
            return -1    
```