# Find Duplicate File in System

_update Jul 5, 2017 21:53_

[leetcode](https://leetcode.com/problems/find-duplicate-file-in-system/#/solutions) Given a list of directory info including directory path, and all the files with contents in this directory, you need to find out all the groups of duplicate files in the file system in terms of their paths.

A group of duplicate files consists of at least two files that have exactly the same content.

A single directory info string in the input list has the following format:

```text
"root/d1/d2/.../dm f1.txt(f1_content) f2.txt(f2_content) ... fn.txt(fn_content)"
```

It means there are n files \(f1.txt, f2.txt ... fn.txt with content f1\_content, f2\_content ... fn\_content, respectively\) in directory root/d1/d2/.../dm. Note that n &gt;= 1 and m &gt;= 0. If m = 0, it means the directory is just the root directory.

The output is a list of group of duplicate file paths. For each group, it contains all the file paths of the files that have the same content. A file path is a string that has the following format:

```text
"directory_path/file_name.txt"

Example 1:
Input:
["root/a 1.txt(abcd) 2.txt(efgh)", "root/c 3.txt(abcd)", "root/c/d 4.txt(efgh)", "root 4.txt(efgh)"]
Output:  
[["root/a/2.txt","root/c/d/4.txt","root/4.txt"],["root/a/1.txt","root/c/3.txt"]]
```

**Note:** No order is required for the final output.

You may assume the directory name, file name and file content only has letters and digits, and the length of file content is in the range of \[1,50\].

The number of files given is in the range of \[1,20000\].

You may assume no files or directories share the same name in the same directory.

You may assume each given directory info represents a unique directory.

Directory path and file info are separated by a single blank space.

**Follow-up beyond contest:**

Imagine you are given a real file system, how will you search files? DFS or BFS?

If the file content is very large \(GB level\), how will you modify your solution?

If you can only read the file by 1kb each time, how will you modify your solution?

What is the time complexity of your modified solution? What is the most time-consuming part and memory consuming part of it? How to optimize?

How to make sure the duplicated files you find are not false positive?

## Basic Idea:

基本思路就是parsing 然后用hashmap。关键是处理字符串的技巧。

## Python Code：

```python
    class Solution(object):
        def findDuplicate(self, paths):
            """
            :type paths: List[str]
            :rtype: List[List[str]]
            """
            table = collections.defaultdict(list)
            matcher = re.compile('(.*)\\((.*)\\)');
            for line in paths:
                data = line.split()
                pwd = data[0]
                for fil in data[1:]:
                    mObj = matcher.search(fil)
                    name = mObj.group(1)
                    content = mObj.group(2)
                    table[content].append(pwd + '/' + name)
            return [x for x in table.values() if len(x) > 1]
```

在python中，操作流程是

```python
    matcher = re.compile(regex)
    mObj = matcher.search(String)
    s1 = mObj.group(1)
    ...
```

## Java Code:

```java
    // java solution using regex and new feature in java 8
    import java.util.regex.*;
    public class Solution {
        public List<List<String>> findDuplicate(String[] paths) {
            Map<String, List<String>> table = new HashMap<>();
            Pattern ptn = Pattern.compile("(.+)\\((.+)\\)");
            for (String line : paths) {
                String[] path = line.split("\\s+");
                String pwd = path[0];
                for (int i = 1; i < path.length; ++i) {
                    Matcher matcher = ptn.matcher(path[i]);
                    if (matcher.find()) {
                        String name = matcher.group(1);
                        String content = matcher.group(2);
                        String value = pwd + "/" + name;
                        if (table.containsKey(content)) table.get(content).add(value);
                        else {
                            List<String> lst = new ArrayList<>();
                            lst.add(value);
                            table.put(content, lst);
                        }
                    }
                }
            }
            return table.values().stream().filter(lst -> lst.size() > 1).collect(Collectors.toList());
        }
    }
```

在java中，流程是：

```java
    Pattern p = Pattern.compile(regex)
    Matcher m = p.matcher(string)
    if / while (m.find()) {
        m.group(1)
        ....
    }
```

另外注意，使用stream时要用 someCollection.stream\(\) instead of stream.of\(\);

_update Aug 17, 2017 22:01_

**更新：**这种写法更为简单。java 的 split\(regex\) 和 python 的 re.split\(regex, str\) 这两个函数非常重要。

```java
    public class Solution {
        public List<List<String>> findDuplicate(String[] paths) {
            Map<String, List<String>> map = new HashMap<>();
            for (String path : paths) {
                String[] arr = path.split("\\s+");
                String pwd = arr[0];
                for (int i = 1; i < arr.length; ++i) {
                    String[] file = arr[i].split("[()]");
                    String file_name = file[0];
                    String file_content = file[1];
                    if (! map.containsKey(file_content)) map.put(file_content, new ArrayList<String>());
                    map.get(file_content).add(pwd + "/" + file_name);
                }
            }
            return map.values().stream().filter(a -> a.size() > 1).collect(Collectors.toList());
        }
    }
```

