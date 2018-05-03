## 细节
_update May 3,2018  3:33_

---
### `emplace_back` vs `push_back`
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
    
    
    return 0;
  }
  
```

从上面的例子可以看出，通过直接传入构造参数的方法调用 `emplace_back()`，只需要调用一次 constructor，不需要调用 move constructor，这是一个优势。