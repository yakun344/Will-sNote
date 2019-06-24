# CPP 多线程
_update Jun 21, 2019_

---
## Creation and Identification
1. C++ 中的 thread 就是 `std::thread`, 它的constructor接收三种参数："function pointer", "function object" and "lambda functions"。
2. new thread 在创建之后就会立即开始执行传入的 callback。
3. 使用 `std::thread::get_id()` 来获取thread的id。对于当前thread的id，使用`std::this_thread::get_id()`。

## Lifecycle
1. thread 新建之后就会开始运行，但是它结束的时候需要被回收。在cpp中，一个thread被create之后一定要call它的 `std::thread::join()` 或者 `std::thread::detach()`，否则会导致整个program to terminate。前者会让parent thread等待 child 结束之后再继续。而后者会让child thread变成 deamon/background thread。
2. 需要注意的是如果一个thread object已经没有associated thread，call join() 或者 detach() 会导致程序terminate。
3. 判断一个thread obj能否join或者detach，使用 `std::thread::joinable()`
4. 实际使用中应该用 RAII，即在 destructor 中call join

## Pass Arguments to Threads
* 我们可以往callback里面传参数，特别需要注意的是如果向里面传递的是引用，需要使用`std::ref()`来wrap。例如：
  ```cpp
        int main() {
            auto callback = [](int& a){
                a = 2;
                cout << "called" << endl;
            };
            int num = 10;
            thread t = thread(bind(callback, ref(num)));
            t.join();
            cout << num << endl;

            return 0;
        }
  ```
  output:<br/>

        called
        2

## Condition Variables
* Condition Variable 是一种event signal机制，当一个thread在执行到某一步的时候我们可能需要等待某一个变量的状态，如果这个状态不满足要求，那么就令该thread进入blocked状态等待，直到另一个thread更新这个变量状态的时候将其唤醒。

  在CPP中，可以使用`condition_variable`来解决上述问题，例如：
  ```cpp
    #include <iostream>
    #include <thread>
    #include <mutex>
    #include <condition_variable>

    using namespace std;

    struct C1 {
      bool state = false;
      condition_variable cv;
      mutex mt;

      void setState() {
        cout << "enter setState" << endl;
        sleep(2);
        unique_lock<mutex> ul(mt);
        cout << "setting state" << endl;
        this->state = true;
        cv.notify_one();
      }

      void waitState() {
        cout << "enter waitState" << endl;
        unique_lock<mutex> ul(mt);
        cv.wait(ul, [=](){return this->state;});
        cout << "set state done" << endl;
      }
    };

    int main() {
      C1 c;
      thread t1{bind(&C1::setState, &c)};
      thread t2{bind(&C1::waitState, &c)};

      t1.join();
      t2.join();
      exit(0);
    }
  ```
  Output:
  ```
    enter waitState
    enter setState
    setting state
    set state done
  ```
  在如上例子中，waitState 会等待 mt 变为true之后再继续执行。

  其实现原理大概是相当于为每个 condition_variable 维持一下 kernel 中的queue用以存放 waiting for it 的 threads，当 `notify_one` or `notify_all` 被call的时候进入kernel mode，选择相应 thread 将其唤醒放入 run queue。唤醒之后的thread仍然需要重新检查state是否已经改变，因为有可能是被错误唤醒(superious wakeup).