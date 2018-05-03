## Function Pointer, Function Object, Lambda
_update May 3,2018  2:38_

---
### Function Pointer
[这篇文章](https://stackoverflow.com/questions/840501/how-do-function-pointers-in-c-work?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa) 解释了关于 **Function Pointer** 的问题，简要描述如下：

1. 一个 function 的函数名可以被认为是它的指针；
2. 函数指针的声明方式如下：
```cpp
    int func(int a, int b) {
        return a + b;
    }
    
    int main() {
        // 定义function pointer
        int (*f)(int,int) = func;
        
        // 使用 function pointer
        int res = f(1, 2); // res == 3
        
        return 0;
    }
```

### Function Object
如在note2中所说，function object 通过重载 `operator()` 实现类似于一个函数的功能，所以可以被做为function传递。

[这篇文章](https://stackoverflow.com/questions/356950/c-functors-and-their-uses?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa) 详细讲了有关内容。另外需要注意，function object 有一个好处，可以通过 struct 中的 field 实现类似闭包的作用。

example:
```cpp
struct add_x {
  add_x(int x) : x(x) {}
  int operator()(int y) const { return x + y; }

private:
  int x;
};
```


### Lambda Expression
[这篇文章](https://stackoverflow.com/questions/7627098/what-is-a-lambda-expression-in-c11?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa) 讲的很好。

1. 基本形式： `[]() {}`
2. example:
```cpp
void func3(std::vector<int>& v) {
  std::for_each(v.begin(), v.end(), [](int) { /* do something here*/ });
}
```