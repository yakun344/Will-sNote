# Design Compressed String Iterator
_update May 12,2018 16:24_

---
[LeetCode](https://leetcode.com/problems/design-compressed-string-iterator/description/)

Design and implement a data structure for a compressed string iterator. It should support the following operations: next and hasNext.

The given compressed string will be in the form of each letter followed by a positive integer representing the number of this letter existing in the original uncompressed string.

**next()** - if the original string still has uncompressed characters, return the next letter; Otherwise return a white space.  
**hasNext()** - Judge whether there is any letter needs to be uncompressed.

**Note:**

Please remember to RESET your class variables declared in StringIterator, as static/class variables are persisted across multiple test cases. Please see here for more details.

**Example:**

    StringIterator iterator = new StringIterator("L1e2t1C1o1d1e1");
    
    iterator.next(); // return 'L'
    iterator.next(); // return 'e'
    iterator.next(); // return 'e'
    iterator.next(); // return 't'
    iterator.next(); // return 'C'
    iterator.next(); // return 'o'
    iterator.next(); // return 'd'
    iterator.hasNext(); // return true
    iterator.next(); // return 'e'
    iterator.hasNext(); // return false
    iterator.next(); // return ' '
    
<br>

### Basic Idea:
设计一个可以读取压缩字符串的 iterator，和 decompression 不同的地方就在于这里需要用 class variable 保存当前的状态。

* #### CPP Code：
```cpp
    class StringIterator {
        string str;
        char chr;
        int remain;
        int currIndex;
        
        int getInt(int start) {
            int i = start;
            int num = 0;
            while (i < str.size() && str[i] >= '0' && str[i] <= '9') {
                num = num * 10 + str[i] - '0';
                i++;
            }
            currIndex = i;
            return num;
        }
    public:
        StringIterator(string compressedString) {
            this->str = compressedString;
            chr = 0;
            remain = 0;
            currIndex = 0;
        }
        
        char next() {
            if (! hasNext()) return ' ';
            --remain;
            return chr;
        }
        
        bool hasNext() {
            if (remain > 0) return true;
            else if (currIndex == str.size()) return false;
            chr = str[currIndex];
            remain = getInt(currIndex + 1);
            return true;
        }
    };
```

* #### Java Code:
```java
    class StringIterator {
        private String str;
        private int currIndex;
        private int remain;
        private char chr;
            
        public StringIterator(String compressedString) {
            this.str = compressedString;
            this.currIndex = 0;
            this.remain = 0;
            this.chr = 0;
        }
        
        public char next() {
            if (! hasNext()) return ' ';
            remain--;
            return chr;
        }
        
        public boolean hasNext() {
            if (remain > 0) return true;
            else if (remain == 0 && currIndex == str.length()) return false;
            chr = str.charAt(currIndex);
            remain = getInt(currIndex + 1);
            return true;
        }
        
        private int getInt(int start) {
            int num = 0;
            while (start < str.length() && str.charAt(start) >= '0' && str.charAt(start) <= '9') {
                num = num * 10 + str.charAt(start) - '0';
                start++;
            }
            currIndex = start;
            return num;
        }
    }
```