## Reverse Words in a String
_update Jan,11 2018  18:05_

---
Given an input string, reverse the string word by word.

**For example,**

    Given s = "the sky is blue",
    return "blue is sky the".
        
### Basic Idea
对于input我们可以先将其整个reverse，然后再对其中每个word 进行 reverse，技巧性很强。如下图所示：
```
        input:  "the sky is blue"
        
        1. reverse:           "eulb si yks eht";
        2. reverse each word: "blue is sky the";
```

#### Java Code:
