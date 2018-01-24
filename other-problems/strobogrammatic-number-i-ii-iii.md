# Strobogrammatic Number 系列
_udpate Jan 23,2018  19:16_

---
## Strobogrammatic Number I  
[LeetCode](https://leetcode.com/problems/strobogrammatic-number/description/)  
A strobogrammatic number is a number that looks the same when rotated 180 degrees (looked at upside down). Write a function to determine if a number is strobogrammatic. The number is represented as a string.  

**For example**   
the numbers "69", "88", and "818" are all strobogrammatic.
<br>
* ### Solution
符合条件的数一定只包含 `[0, 1, 6, 8, 9]` 这几个数，而且一定左右成对出现，可以先把每一对存入 HashMap，然后用双指针从左右向中间检查。
  * #### Java Code：
  ```java
    class Solution {
        public boolean isStrobogrammatic(String num) {
            Map<Character, Character> map = new HashMap<>();
            map.put('0', '0');
            map.put('1', '1');
            map.put('8', '8');
            map.put('6', '9');
            map.put('9', '6');
            int l = 0, r = num.length() - 1;
            while (l <= r) {
                Character value = map.get(num.charAt(l));
                if (value == null || num.charAt(r) != value) return false;
                l++;
                r--;
            }
            return true;
        }
    }
  ```
  
<br>

## Strobogrammatic Number II
[LeetCode](https://leetcode.com/problems/strobogrammatic-number-ii/description/)

A strobogrammatic number is a number that looks the same when rotated 180 degrees (looked at upside down).

Find all strobogrammatic numbers that are of length = n.

**For example,**  

    Given n = 2, return ["11","69","88","96"].
    
* ### Solution
&emsp; 有两种思路，在中间插入和两边插入。如果选择每次在中间插入，就要注意当 n 为奇数的时候，当每个数字长度到达n-1后，最后一次只插入一个字符，而且是 `[0,1,8]` 中的一个。而如果选择两遍插入，则是对于技术情况不是从 `""` 空字符串开始，而是从 `[0, 1,8]` 开始，每次两边插入一对组合。
<br>
&emsp; 具体实现的时候可以使用 iteration 和 recursion 两种实现方法。
  * #### Java Code (iteration):
  ```java
    class Solution {
        public List<String> findStrobogrammatic(int n) {
            List<String> res = new ArrayList<>();
            if (n == 0) {
                return res;
            }
            
            // 先生成偶数长度，如果n是奇数，之后要再在中间另外插入一个数
            res.add("");
            for (int i = 0; i < n / 2; ++i) {
                List<String> currLevel = new ArrayList<>();
                for (String s : res) {
                    for (String pair : new String[]{"00", "11", "88", "69", "96"}) {
                        if (i == 0 && pair.equals("00")) continue; // 首尾不能为 0
                        currLevel.add(insertPair(s, pair));
                    }
                }
                res = currLevel;
            }
            // 如果n是奇数，之后要再在中间另外插入一个数
            if ((n & 1) != 0) {
                List<String> newRes = new ArrayList<>();
                for (String s : res) {
                    for (String mid : new String[]{"0", "1", "8"}) {
                        newRes.add(insertPair(s, mid));
                    }
                }
                return newRes;
            } else {
                return res;
            }
        }
        
        // 在target正中间
        private String insertPair(String target, String pair) {
            StringBuilder sb = new StringBuilder();
            for (int i = 0; i < target.length() / 2; ++i) {
                sb.append(target.charAt(i));
            }
            sb.append(pair);
            for (int i = target.length() / 2; i < target.length(); ++i) {
                sb.append(target.charAt(i));
            }
            // System.out.println(sb.toString());
            return sb.toString();
        }
    }
    ```

















