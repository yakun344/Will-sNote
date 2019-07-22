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
  output:

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
  在如上例子中，waitState 会等待 state 变为true之后再继续执行。

  其实现原理大概是相当于为每个 condition_variable 维持一个 kernel 中的queue用以存放 waiting for it 的 threads，当 `notify_one` or `notify_all` 被call的时候进入kernel mode，选择相应 thread 将其唤醒放入 run queue。唤醒之后的thread仍然需要重新检查state是否已经改变，因为有可能是被错误唤醒(superious wakeup).

  每次当thread被唤醒，wait() function 返回之前会重新lock传入的锁，如果获得锁失败，std::terminate() 会被call。

## std::future, std::promise and Return Value of threads
* 有时候我们需要一些比较耗时的操作，同时在等待这个操作返回结果的时候我们想继续做一些其他工作，做完这些其他工作之后再等待该耗时操作的结果。这就是一个典型的use case，我们可以为该耗时操作新建一个thread，同时在原来的thread中做其他事情，之后等待这个thread完成之后再继续。

  上面的 use case 如果使用 condition variable 实现，我们就需要向新thread中传入一个指针，在新thread中完成工作后将结果写入这个指针，然后在原来的thread中用condition variable wait。那么为了获得一个结果值，我们使用了一个 condition variable，一个 mutex，以及一个指针。三个变量只做了一件事情，这是非常繁琐的。为了解决这一问题，我们可以使用 `std::future` 以及 `std::promise`。

* **Basic about `std::future` and `std::promise`**
  1. `std::future` internally stores a value that will be assigned in future;
  2. `std::future` has a `get()` method, call it will block the thread if the value has not been set;
  3. Each `std::promise` object has an associated `std::future` object, it shares data with that `std::future` object;

* **Usage**
  1. Create promise: `std::promise<int> promiseObj;`
     > Thread 1 create this `promiseObj` and pass it to thread 2, then thread 1 can use the `future` object assoiated with `promiseObj` to get the value;
  2. Get future: `std::future<int> futureObj = promiseObj.get_future();`
  3. Get value: `int val = futureObj.get();`
     > Thread 1 will be blocked until thread 2 sets the value in `promiseObj`;
  4. Set value: `promiseObj.set_value(45);`
     > SetValue之后，thread 1 就会得到返回值，继续执行。

  ![Diagram](https://thispointer.com/wp-content/uploads/2015/06/promise.png)

* **Complete Example**
  ```cpp
    #include <iostream>
    #include <thread>
    #include <future>

    void initiazer(std::promise<int> * promObj)
    {
        std::cout<<"Inside Thread"<<std::endl;
        promObj->set_value(35);
    }

    int main()
    {
        std::promise<int> promiseObj;
        std::future<int> futureObj = promiseObj.get_future();
        std::thread th(initiazer, &promiseObj);
        std::cout<<futureObj.get()<<std::endl;
        th.join();
        return 0;
    }
  ```
  Output:
  ```
    Inside Thread
    35
  ```

## std::async()
* **What is std::async()**  
  这是一个function template, 传入一个callback，return一个future object。其内部实现会创建一个新thread，传入一个promise，然后返回其对应的future。
  ```cpp
    template <class Fn, class... Args>
    future<typename result_of<Fn(Args...)>::type> async (launch policy, Fn&& fn, Args&&... args);
  ```
  `launch policy` 有三种选择：  
  * `std::launch::async`
    > Create a new seperate thread
  * `std::launch::deferred`
    > Non async, function will be called when `std::future::get()` called;
  * `std::launch::async | std::launch::deferred`
    > Default behavior, depending on load on system and implementation, we cannot control it.
  
* **Usecase of std::async()**
  例如我们需要两组数据，一组从DB中获取，另一组从 file-system 中获取，如果每个操作耗时5s，如果我们单线程执行，就需要耗时十秒。于是，我们需要在不同的线程中进行操作获取数据。
  
  我们有两种方法来做这件事情：
  * 直接使用 `std::promise` 和 `std::future`:
  
    我们就需要手动创建一个 `promise`，`get_future`，再创建新的thread，call `future::get`，这样是比较麻烦的。
  * 使用 `std::async`:
    ```cpp
      #include <iostream>
      #include <string>
      #include <chrono>
      #include <thread>
      #include <future>
      
      using namespace std::chrono;
      
      std::string fetchDataFromDB(std::string recvdData)
      {
        // Make sure that function takes 5 seconds to complete
        std::this_thread::sleep_for(seconds(5));
      
        //Do stuff like creating DB Connection and fetching Data
        return "DB_" + recvdData;
      }
      
      std::string fetchDataFromFile(std::string recvdData)
      {
        // Make sure that function takes 5 seconds to complete
        std::this_thread::sleep_for(seconds(5));
      
        //Do stuff like fetching Data File
        return "File_" + recvdData;
      }
      
      int main()
      {
        // Get Start Time
        system_clock::time_point start = system_clock::now();
      
        std::future<std::string> resultFromDB = std::async(std::launch::async, fetchDataFromDB, "Data");
      
        //Fetch Data from File
        std::string fileData = fetchDataFromFile("Data");
      
        //Fetch Data from DB
        // Will block till data is available in future<std::string> object.
        std::string dbData = resultFromDB.get();
      
        // Get End Time
        auto end = system_clock::now();
      
        auto diff = duration_cast < std::chrono::seconds > (end - start).count();
        std::cout << "Total Time Taken = " << diff << " Seconds" << std::endl;
      
        //Combine The Data
        std::string data = dbData + " :: " + fileData;
      
        //Printing the combined Data
        std::cout << "Data = " << data << std::endl;
      
        return 0;
      }
    ```
    output:
    ```
    Total Time Taken = 5 Seconds
    Data = DB_Data :: File_Data
    ```
    **`std::async()` does following things:**
    * Automatically creates a thread (or picks from internal thread pool) and a promise object;
    * Passes the `std::promise` object to thread function and returns the `std::future` object;
