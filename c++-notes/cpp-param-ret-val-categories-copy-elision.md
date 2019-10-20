# Cpp Note6: Val Categories, 函数参数与返回值, Copy Elision

_update Jul 22, 2019_

## Value Categories [_Document_](https://en.cppreference.com/w/cpp/language/value_category#cite_note-6)

* lvalue

  `lvalue` 基本上是指可以作为left-hand side of an assignment expression 的值。

* prvalue

  `prvalue = pure rvalue`, 基本上是指可以位于 right-hand side of an assignment expression 的值，例如一些literal（不包括string literal）。

* xvalue

  `xvalue = eXpiring value`， 基本上是一些非rvalue的，但是其resources可以被reuse的。例如 return value 是 rvalue reference 的function call，或者 `a[i]` \(a is an rvalue\)。注意，`std::move()` return的值是一个xvalue。

另外还有glvale\(generalized lvalue\)，所有的value可以被分成两类，即`glvalue` 以及 `prvalue`。几种categories关系如下：

```text
                expression
                /        \
               /          \
           glvalue      rvalue
            /    \       /  \
           /      \     /    \
        lvalue    xvalue     prvalue
```

**C++11 中的性质**

为了处理move semantics，引入了两个expressions的性质：

* has identity: 

  > it's possible to determine whether the expression refers to the same entity as another expression, such as by comparing addresses of the objects or the functions they identify \(obtained directly or indirectly\);

* can be moved from: 

  > move constructor, move assignment operator, or another function overload that implements move semantics can bind to the expression.

在C++11中，有如下性质：

* have identity and cannot be moved from are called lvalue;
* have identity and can be moved from are called xvalue;
* do not have identity and can be moved from are called prvalue
* do not have identity and cannot be moved from are not used
* 有identity的都叫做 grvalue
* 能被move的都是rvalue

## Storage Class / Storage Duration / Linkage

[Document](https://en.cppreference.com/w/cpp/language/storage_duration)

**Storage Duration:**

* **automatic**:

  > 生命周期在一个code block中，所有local objects都是这个duration，除了decalred static, extern or thread\_local;

* **static**:

  > 从程序开始到程序结束。包括所有declared at namesapce scope，global namespace scope，以及 static 和 extern；

* **thread**:

  > 随thread开始而开始，结束而结束。only declared with thread\_local have this storage duration.

* **dynamic**:

  > 使用 new 创建的 object;

**Linkage:**

Translation unit 就是一个 cpp file 加上它included 的所有 header file。

* **no linkage:**

  > The name can be referred to only from the scope it is in. 例如block scope中的变量，class，typedef 等等。

* **internal linkage:**

  > Can be referred to from all scopes in the current translation unit. 主要是指declared in namespace scope的static的变量和函数, const var。需要注意unnamed namespace中declared的一定只有internal linkage。

* **external linkage:**

  > Can be referred to from the scopes in the other translation units. 主要是指namespace scope中declare的non static的变量和函数。另外以及extern的，enumerations，name of classes，member functions, static data members, nested classes and enums 等等。

[不错的文章关于linkage和一些基本概念](http://www.goldsborough.me/c/c++/linker/2016/03/30/19-34-25-internal_and_external_linkage_in_c++/)

A translation unit refers to an implementation \(.c/.cpp\) file and all header \(.h/.hpp\) files it includes. If an object or function inside such a translation unit has internal linkage, then that specific symbol is only visible to the linker within that translation unit. If an object or function has external linkage, the linker can also see it when processing other translation units. The static keyword, when used in the global namespace, forces a symbol to have internal linkage. The extern keyword results in a symbol having external linkage.

The compiler defaults the linkage of symbols such that:

Non-const global variables have external linkage by default Const global variables have internal linkage by default Functions have external linkage by default

基本上是说要注意One definition rule，以及不同变量和函数的linkage。对于int之类的，声明要加extern，否则会被当作是定义。

## Copy Elision

[官方文档](https://en.cppreference.com/w/cpp/language/copy_elision#Explanation)

[非常好的解释](https://www.fluentcpp.com/2016/11/28/return-value-optimizations/)

Copy Elision 是一种用于消除不必要copy的编译器优化。其结果往往可以令copy-initialization具有和direct-initialization同等的开销。

**Note:**

> 在RVO和NRVO中，c++11 标准规定如果一个variable is eligible for copy elision，但是编译器无法进行copy elision优化（例如被disable），那么compiler会尝试进行move，即使那个object是一个lvalue。
>
> 如果return之前使用move，则不会被NRVO或RVO，因为 `std::move()` return一个xvalue，非lvalue或者prvalue.

1. Return value optimization（RVO）\(only works with prvalue\)

   是指通过编译器优化消除当function返回temporary object时的copy操作，即使copy constructor 有其他side effect。基本原理是通过编译器优化，直接使用函数外的空间来构建object，例如下面的例子中，直接用`obj`的空间来构建被`F()`return的`C`的object。

   ```cpp
        #include <iostream>

        struct C {
            C() = default;
            C(const C&) { std::cout << "A copy was made.\n"; }
        };

        C F() {
            return C();
        }

        int main() {
            std::cout << "Hello World!\n";
            C obj = F();
        }
   ```

   经过编译器优化，原本在F返回和obj赋值的时候会发生两次复制，但是由于编译器的优化，最终输出结果可能是：

   ```text
    Hello World!
   ```

   **Note:** 即使一个function有多个return statement，只要return的object是在return statement中创建的，RVO依然可以work。

2. NamedReturnValueOptimization \(NRVO\) \(only works with lvalue with automatic storage duration\)

   编译器也可以对function中之前create的object做出优化，但是需要注意在所有的return path中，只能有一个returnable object，否则无法应用NRVO。例如，如下Code 1 work，2 不work。

   ```cpp
    // code 1
    C F() {
        C obj;
        return obj;
    }

    // code 2
    C F() {
        C obj1;
        C obj2;
        if (...) {
            return obj1;
        } else {
            return obj2;
        }
    }
   ```

3. Copy a temporary object to same class type

   ```cpp
      int main(void) {
        X x{X{}};
      }
   ```

   The above code will not call copy constructor or move constructor.

## 关于Copy Elision 的官方文档中的例子

```cpp
    #include <iostream>
    #include <vector>

    struct Noisy {
        Noisy() { std::cout << "constructed\n"; }
        Noisy(const Noisy&) { std::cout << "copy-constructed\n"; }
        Noisy(Noisy&&) { std::cout << "move-constructed\n"; }
        ~Noisy() { std::cout << "destructed\n"; }
    };

    std::vector<Noisy> f() {
        std::vector<Noisy> v = std::vector<Noisy>(3); // copy elision when initializing v
                                                    // from a temporary (until C++17)
                                                    // from a prvalue (since C++17)
        return v; // NRVO from v to the result object (not guaranteed, even in C++17)
    }             // if optimization is disabled, the move constructor is called

    void g(std::vector<Noisy> arg) {
        std::cout << "arg.size() = " << arg.size() << '\n';
    }

    int main() {
        std::vector<Noisy> v = f(); // copy elision in initialization of v
                                    // from the temporary returned by f() (until C++17)
                                    // from the prvalue f() (since C++17)
        g(f());                     // copy elision in initialization of the parameter of g()
                                    // from the temporary returned by f() (until C++17)
                                    // from the prvalue f() (since C++17)
    }
```

output:

```text
    constructed
    constructed
    constructed
    constructed
    constructed
    constructed
    arg.size() = 3
    destructed
    destructed
    destructed
    destructed
    destructed
    destructed
```

