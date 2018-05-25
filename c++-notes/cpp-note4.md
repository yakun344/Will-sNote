# 细节
_update May 3,2018  3:33_

---

## 1. `emplace_back` vs `push_back`
#### 官方文档
首先看官方文档：[cplusplus.com](http://www.cplusplus.com/reference/vector/vector/emplace_back/)  
摘要：
> Inserts a new element at the end of the vector, right after its current last element. This new element is constructed in place using args as the arguments for its constructor.  
> The element is constructed in-place by calling `allocator_traits::construct` with args forwarded. A similar member function exists, `push_back`, which either copies or moves an existing object into the container.

可以看出，`emplace` 用于直接传入constructor 需要的参数，inplace地构造对象。而 `push_back` 则需要将对象 copy or move 入容器。

[这里](https://stackoverflow.com/questions/10890653/why-would-i-ever-use-push-back-instead-of-emplace-back?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa) 有一个stackoverflow的讨论，感觉讲得不错。
[这篇文章](http://blog.guorongfei.com/2016/03/16/cppx-stdlib-empalce/) 讲了 `emplace` 和 `insert` ，感觉讲的很好。

**Example:**
```cpp
  class Test {
  public:
    int a;

    explicit Test(int a) {
      this->a = a;
      cout << "constructor: a = " << a << endl;
    }

    Test(const Test& that) {
      cout << "copy constructor, a = " << that.a << endl;
      this->a = that.a;
    }

    Test(Test&& that) noexcept {
      cout << "move constructor, a = " << that.a << endl;
      this->a = that.a;
    }
  };

  int main() {
    Test t1{3};

    vector<Test> v;
    v.emplace_back(2); // 即使构造函数是 explicit，仍然可以隐式调用，只call一次constructor, no copy，no move

    v.emplace_back(Test(2)); // call 一次 constructor，一次 move

    v.push_back(2); // 如果构造函数 Test(int a) 声明为 explicit，则报错，因为无法隐式调用

    v.push_back(Test(2)); // call 一次 constructor，一次move

    v.emplace_back(2.5); // no warning, create object of Test with a == 2

    v.push_back(2.5); // 如果构造函数非explicit，会有warning，"implicit conversion from 'double' to 'int'"
                      // 如果构造函数explicit，同样会报错。

    return 0;
  }

```

从上面的例子可以看出，通过直接传入构造参数的方法调用 `emplace_back()`，只需要调用一次 constructor，不需要调用 move constructor，这是一个优势。但同时，当我们向 int 参数传入 float 的时候，`emplace_back()` 并不会报warning，不如`push_back()` 安全。

<br>

---

## 2. Integer Literal Type
#### 问题产生
在Java中，如果我判断 `if (a < 0x80000000)`，this is equivalent to `if (a < Integer.MIN_VALUE)` and `if (a < -2147483648)`。但在 c++ 中，`0x80000000` 却被当做了 usigned int，等于 `2147483648`，如果需要实现和Java中一样的效果，需要手动转换为int：`(int)0x80000000`。

#### 问题解释
[官方解释](http://en.cppreference.com/w/cpp/language/integer_literal)，摘要如下：

1. 十进制，十六进制，八进制，二进制的 integer literal 所自动fit的type是从int开始顺序匹配的，例如这里出现的问题就是因为 `0x80000000` 被 fit 到了 ul 类型.
2. 在 Integer Literal 中使用 `负号 -` 的时候需要特别注意，可能会带来 implicit type conversions.

#### 解决
使用 `(int)0x80000000` 或者 `int(0x80000000)` 进行 type conversion，这是 c++ 中两种 type conversion 的格式。

<br>

---

### 3. HashSet in Java and unordered_set in C++
Java HashSet 中的 `add()` 对应了 C++ 中 unordered_set 的 `insert()`。

它们的返回值也有相似的设计思路。`add()` 返回一个boolean，如果成功插入，则返回 true，如果所插入的key之前已经存在，则返回 false；

而 `insert()` 中的返回值是一个 `pair<iterator, bool>`，其第二个返回值有着和Java中一样的规则。

---

### 4. Copy Assignment (=) and Move Constructor
* #### 问题
如果自定义了 move constructor, 默认 copy assignment 就会被禁用，导致 = 赋值的失败：
```cpp
  Test t = Test(1);
  t = Test(2); // 报错
```
* #### 解决方法
需要自定义 copy assignment，或者将其定义为 `Test& operator=(Test&)=default;`. 详见官方文档，讲的还比较清楚：  
[https://en.cppreference.com/w/cpp/language/copy_assignment](https://en.cppreference.com/w/cpp/language/copy_assignment)

---

### 5. 函数返回对象，内容为乱码
* #### 问题
```cpp
    Test& f1() {
      Test t = *(new Test(2));
      return t; // 原因就是这里，返回了一个临时变量的引用
    }
    ...
  in main:
    ...
      Test t = f1();
      cout << t.a << endl; // 打印出为乱码
```
* #### 解决
不要返回临时变量的引用，需要将 `Test t = *(new Test(2));` 改为 `Test& t = *(new Test(2));` 即可。

这里有一个知乎上的讨论，感觉有帮助：[https://www.zhihu.com/question/41137408](https://www.zhihu.com/question/41137408)，在实际使用中，尽量避免返回引用。

---

### 6. C++ 如何split string
这篇文章中介绍了几种方法：[http://ysonggit.github.io/coding/2014/12/16/split-a-string-using-c.html](http://ysonggit.github.io/coding/2014/12/16/split-a-string-using-c.html)，个人认为第一种利用 stringstream 的最容易记忆和应用。

**For Example:**
```cpp
// Signature istream& getline (istream&& is, string& str, char delim)

vector<string> split(const string &s, char delim) {
    stringstream ss(s);
    string buffer;
    vector<string> tokens;
    while (getline(ss, buffer, delim)) {
        tokens.push_back(buffer);
    }
    return tokens;
}
```

---

### 7. Java 和 C++ 中的 static
**共同点：** 在 class 中的static都表示该变量由所有instance共享，属于class。需要注意c++中的static class variable 要在类外面显式定义，而且调用的时候 C++ 要用 `className::varName`，Java 只要 `className.varName`;

---

### 8. Priority Queue 使用对比
Java 中的 PriorityQueue 默认为 min heap，而 c++ 中的 priority_queue 默认为 max heap。

* #### Initialize 方法对比
**Java**  
```java
  PriorityQueue<Integer> pq = new PriorityQueue<>((x, y) -> y - x);
  // or
  PriorityQueue<Integer> pq = new PriorityQueue<>(new Comparator<Integer>(){
    @Override
    public int compare(Integer a, Integer b) {
      return Integer.compare(b, a);
    }
  });
```
**C++**  
```cpp
  auto comp = [](int a, int b){ return b - a; };
  priority_queue<int, vector<int>, decltype(comp)> pq(comp);
```

* #### Access 方法对比
**Java**  
* `boolean offer(E e)`
* `E peek()`
* `E poll()`
**C++**
* `const_reference top() const`
* `void push (const value_type& val);` `void push (value_type&& val);`
* `void pop()`
