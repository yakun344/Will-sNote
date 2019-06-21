# Reverse Vowels of a String （easy）

_update Jan 26,2018 14:54_

[LeetCode](https://leetcode.com/problems/reverse-vowels-of-a-string/description/)

Write a function that takes a string as input and reverse only the vowels of a string.

**Example 1:**

```text
Given s = "hello", return "holle".
```

**Example 2:**

```text
Given s = "leetcode", return "leotcede".
```

**Note:**  
The vowels does not include the letter "y".

## Basic Idea:

在一个HashSet中存所有元音字母，然后用双指针从两边向中间逼近，如果两指针都指向元音字母，就将其换位，如果其中任意一个指针所指不是元音字母，将其向中间移动；

* **Java Code:**

  ```java
  class Solution {
    public String reverseVowels(String s) {
        Character[] vowels = new Character[]{'a', 'e', 'i', 'o', 'u', 'A', 'E', 'I', 'O', 'U'};
        Set<Character> set = new HashSet(Arrays.asList(vowels));
        char[] arr = s.toCharArray();
        int i = 0, j = arr.length - 1;
        while (i < j) {
            if (set.contains(arr[i]) && set.contains(arr[j])) {
                swap(arr, i++, j--);
            } else {
                if (! set.contains(arr[i])) i++;
                if (! set.contains(arr[j])) j--;
            }
        }
        return new StringBuilder().append(arr).toString();
    }

    private void swap(char[] arr, int i, int j) {
        char t = arr[i];
        arr[i] = arr[j];
        arr[j] = t;
    }
  }
  ```

_update May 8,2018 1:09_

### C++ Code:

使用c++内置swap进行swap操作，无需另外写helper function；

```cpp
    class Solution {
    public:
        string reverseVowels(string s) {
            unordered_set<char> _set{'a', 'e', 'i', 'o', 'u', 'A', 'E', 'I', 'O', 'U'};
            int i = 0, j = s.size() - 1;
            while (i < j) {
                if (_set.count(s[i]) && _set.count(s[j])) {
                    swap(s[i++], s[j--]);
                } 
                if (! _set.count(s[i])) {
                    i++;
                } 
                if (! _set.count(s[j])) {
                    j--;
                }
            }
            return s;
        }
    };
```

