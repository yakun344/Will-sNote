## Cpp Note1: 右值引用于移动语意
_update May,1, 2018 22:38_

---
虽然上Data Structure课的时候就是用C++，但我深知那时候自己用到的c++的内容只是这个博大精深语言的极少一部分。最近重新开始学习 C++，因为之前接触过Pyhton和Java，对Java尤其比较熟悉，所以首先想到的就是从C++和Java之间的不同入手。

以下列出一些在学习过程中觉得重要的内容，记录一些找到的不错的资料，也可能会写一点我自己学习过程中的感悟，希望可以加深记忆。

### 右值引用（rvalue reference）与移动语意（move semantic）
关于这部分，[这篇文章](https://codinfox.github.io/dev/2014/06/03/move-semantic-perfect-forward/) 讲的非常好，下面写一些关于这部分的笔记或摘要：

##### 左值和右值

1. 有名字的，可以被取地址的就是左值，其他的是右值；
2. 一般认为右值是可以被安全移动的，无需深拷贝做副本，例如：
```cpp
    Test createTestObj() {
        return Test();
    }
```
这种情况中，函数中已经生成了临时变量，一个 Test object，在返回的时候仍然会做一次深拷贝，然后销毁掉这个生成的临时变量（如果不考虑编译器优化），相当于在函数调用：`Test t = createTestObj();` 的过程中，会调用一次 default constructor 和 一次 copy constructor 加一次 destructor，显然是不必要的。有了右值引用之后，我们可以利用右值引用重载一个 move constructor, 在其中将heap中的资源拿出来，将原本右值中的相应指针置零，这样就节省了很多操作。

##### 右值引用（Type&&）和通用引用 （universal reference）

1. 右值引用和 `const Type&` 两者不同，可以用来区分重载，实践中用这种方法可以重载 copy constructor 和 move constructor，其中 move constructor 不需要做深拷贝，只是将 other 的资源拿过来，并将 other 中相应指针置零。
2. 右值引用只能接受右值，例如 `foo(int&& a);` 传入 `6` 可以，传入 `a = 6; foo(a);` 不可以。  
3. `const Type& ` 类型可以传入左值或者右值，比较常用。  
4. 如果 `foo(T&& t)` 中的 T 是通过推导得出的类型，则此时的 `&&` 为通用引用，可以接收左值或者右值，如果是右值，好处见下一条；

##### 完美转发（forward()）和 move()
完美转发：
```cpp
    template<typename T>
    void bar(T t) {
        ...
    }

    template<typename T> 
    void foo(T&& t) {
        // 这里的 t 被认为是左值，即使传入的是右值，因为它一定可以被取地址。
        
        // 这里就出现问题了，如果传入的是右值，我们希望传入bar的 t 调用 move constructor, 但是因为
        // 无论t是否为右值，这里都被当做左值，所以这里会调用 copy constructor。
        bar(t); 
        
        // 为了解决这个问题，需要用到 forward(t), 这个函数可以将原本是左值的t保留左值，将原本是右值的t转为右值，
        // 只要这样，就可以实现完美转发，即右值的 t 进入foo，然后在 foo 中被传入 bar 仍然保持右值；
        bar(forward(t));
    }
```
Move：
`move()` 函数可以强行将变量转为右值，之后就可以触发相应的 move constructor。

#### 一点个人感悟
和Java中操作变量全部是引用的世界观不同，c++中操作变量有三种方式，指针、引用和变量本身。Java中如果我们想要在参数中传入一个object或者在函数中返回一个object，我们实际上按值专递了这个object的引用。而在C++中，如果我们以指针或引用的方式将一个object传入或者返回，和Java中是类似的，但是如果我们以变量名直接传入或者返回，就会造成调用该object的 copy constructor，然后实际传入或返回的是该object的副本。有些时候我们确实需要进行这样的操作，例如在Java中，如果我想将一个 `List<Integer> path` 放入一个 `List<List<Integer>> res` 中，而且我希望保存当前 path 的状态，之后继续对 path 进行操作，我就需要手动对 path 进行 copy：`res.add(new ArrayList<>(path));`。那么在C++中，就不需要这一步了，对于 `vector<vector<int>> res`，将 `vector<int> path` 加入其中：`res.push_back(path);`，这一步会自动将 path 的副本放入 res，之后对 path 进行任何操作都不再影响 res 中 path 的副本。而 java 中的 `res.add(path)` 更像是c++中在res中存放指针： `res.push_back(&path);`。






