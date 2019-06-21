# Reverse Words in a String

_update Jan,11 2018 18:05_

Given an input string, reverse the string word by word.

**For example,**

```text
Given s = "the sky is blue",
return "blue is sky the".
```

## Basic Idea

对于input我们可以先将其整个reverse，然后再对其中每个word 进行 reverse，技巧性很强。如下图所示：

```text
        input:  "the sky is blue"

        1. reverse:           "eulb si yks eht";
        2. reverse each word: "blue is sky the";
```

### Java Code:

```java
public class Solution {
    public String reverseWords(String s) {
        char[] arr = trim(s);
        // reverse the whole string
        reverse(arr, 0, arr.length - 1);
        // reverse each word
        int start = -1;
        for (int end = 0; end < arr.length; ++end) {
            if (arr[end] != ' ' && start == -1) {
                start = end;
            }
            if ((end == arr.length - 1 || arr[end + 1] == ' ') && start != -1) {
                reverse(arr, start, end);
                start = -1;
            }
        }
        return new StringBuilder().append(arr).toString();   
    }

    // delete leading, trailing spaces and more than 1 spaces between words.
    private char[] trim(String s) {
        StringBuilder sb = new StringBuilder();
        boolean start = true, newWord = true;
        for (int i = 0; i < s.length(); ++i) {
            if (s.charAt(i) != ' ') {
                if (newWord) {
                    if (! start) {
                        sb.append(" ");
                    } else {
                        start = false;
                    }
                } 
                sb.append(s.charAt(i));
                newWord = false;
            } else {
                newWord = true;
            }
        }
        return sb.toString().toCharArray();
    }

    private void reverse(char[] arr, int start, int end) {
        while (start < end) {
            char t = arr[start];
            arr[start++] = arr[end];
            arr[end--] = t;
        }
    }
}
```

