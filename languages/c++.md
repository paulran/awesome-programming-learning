# C++
## Contents
* [Reference](#Reference)
* [C++基础](#C基础)
* [C++11（新特性总结）](#c11新特性总结)
* [Boost](#Boost)


## Reference
1. https://isocpp.org/get-started
2. http://www.cplusplus.com/reference/
3. https://zh.cppreference.com/w/%E9%A6%96%E9%A1%B5
4. MinGW: https://nuwen.net/mingw.html, http://www.mingw-w64.org/


## C++基础
1. 容器（Containers）
    1. std::array
    2. std::bitset
    3. std::deque
    4. std::forward_list
    5. std::list
    6. std::map, std::multimap
    7. std::queue, std::priority_queue
    8. std::set, std::multiset
    9. std::stack
    10. std::unordered_map, std::unordered_multimap
    11. std::unordered_set, unordered_multiset
    12. std::vector, std::vector<bool>
    13. std::tupe
    14. 
2. 多线程
    1. 锁：
        1. 互斥锁：mutex, lock_guard (在构造函数里加锁，在析构函数里解锁）, unique_lock（自动加锁、解锁） <=> 只有mutex（也称为互斥量），lock_guard和unique_lock只是对mutex的不同使用方式。
        2. 读写锁：shared_lock
    2. 线程间的通信方式（同步方式）：
        1. 原子变量：std::atomic<T>, std::atomic_flag
        2. 全局变量（要对该变量加关键字volatile）or 共享数据 + 锁
        3. 条件变量：std::condition_variable + 锁
        4. std::future + std::promise
        5. 信号量（semaphore）：c++11中未实现？ Linux API:sem_init/sem_wait/sem_post/sem_destory
        6. 事件（event）：Linux上没有？
        7. 临界区（critical section）：Linux上没有？
3. 多进程
    2. 进程间的通信方式：
        1. 共享内存
        2. pipe
        3. socket


## C++11（新特性总结）
1. 类型与变量相关
    1. nullptr
    2. auto
    3. constexpr
    4. using取代typedef
    5. std::chrono
    6. std::regex
    7. for循环语法（:）
2. 容器
    1. std::array
    2. std::forward_list
    3. std::unordered_map（STL在内部实现上，std::map使用的数据结构为二叉树，而std::unordered_map内部是哈希表的实现方式，哈希map理论上查找效率为O(1)。但在存储效率上，哈希map需要增加哈希表的内存开销。）
    4. std::unordered_set（std::unordered_set的数据存储结构也是哈希表的方式结构，除此之外，std::unordered_set在插入时不会自动排序，这都是std::set表现不同的地方。）
    5. tuple多元数组
3. 对于类
    1. 构造函数
        1. 控制构造函数
            1. default关键字生成默认构造函数和析构函数
            2. delete关键字禁止拷贝构造、禁止赋值构造、禁止自定义参数的构造函数
    2. 委托构造函数
    3. 移动构造函数：std::move函数可以以非常简单的方式将左值引用转换为右值引用。std::move是将对象的状态或者所有权从一个对象转移到另一个对象，只是转移，没有内存的搬迁或者内存拷贝所以可以提高利用效率,改善性能。
    4. override和final:作用于虚函数，更多的作用是显式的标识是否应该多态继承或不应该。子类的虚函数多态实现要加override显式的表明，不让子类多态实现的虚函数也要记得加入final。
4. 函数和回调（lambda、bind、function）
    1. 直接lambda表达式
    2. c++11风格的函数指针std::function & std::bind
5. 智能指针
    1. std::unique_ptr（功能基本对应boost的scoped_ptr，或之前stl的auto_ptr，生命周期随构造者，reset自动析构再重新构造，get判断是否有效、支持放在容器内）
    2. std::shared_ptr（功能对应于boost的shared_ptr，可以有多个持有者的共享指针，即所谓引用计数型指针，直到最后一个持有者delete释放时，其指向的资源才会真正被释放。适合用于管理“全局动态资源”。std::make_shared封装了new方法。）
    3. std::weak_ptr（为了解决std::shared_ptr在相互引用的情况下出现的问题而存在的，与std::shared_ptr最大的差别是在赋值时，不会引起智能指针计数增加。lock返回一个指向共享对象的shared_ptr。与任何其它shared_ptr类似，只要此shared_ptr存在，它所指向的底层对象也就会一直存在。）
6. 多线程与互斥同步
    1. std::thread
    2. 原子变量：std::atomic<XXX>
    3. std::condition_variable

## Boost
* 常用功能库：
    - 关于时间的 chrono库， 已被加入C++11标准。
    - 关于随机数的random库，已被加入C++11标准。
    - 关于正则表达式的regex库，已被加入C++11标准。
* 内存管理：
    - 包括智能指针：
        + scoped_ptr, 对应C++11中的unique_ptr。
        + shared_ptr， 已被加入C++11标准。
        + weak_ptr，已被加入C++11标准。
    - scoped_array, shared_array：scoped_array/shared_array是scoped_ptr/shared_ptr对动态数组的扩展，它们为动态数组提供了可自动删除的代理，shared_array比scoped_array有更多的用途，但我们应该使用vector和shared_ptr，除非程序对性能有非常苛刻的要求。
* 使用工具：
    - noncopyable，允许程序轻松实现一个禁止拷贝的类。它将拷贝构造函数和拷贝赋值函数设置为private，禁止进行拷贝和赋值。将默认构造函数设置为protected，禁止直接产出无意义的noncopyable对象。
    - ignore_unused，使用可变参数模板，可以支持任意数量、任意类型的变量，把它们作为函数的参数“使用”了一下，达到了与(void)var完全相同的效果。但它的命名更清晰，写法更简单，而且由于是inline函数，完全没有运行时的效率损失。
    - uuid， 用来生成全局唯一的UUID。
* 容器与数据结构：
    - 环形缓冲区circular_buffer。
    - any，用来容纳任何类型的元素。 已被加入C++17标准。
* 函数与回调：
    - ref库，定义了一个很小很简单的引用类型的包装器，可以用来进行引用传递。 已被加入C++11标准。
    - bind库，是对C++98标准中函数适配器bind1st/bind2nd的泛化和增强，可以适配任意的可调用对象，包括函数指针，函数引用，成员函数指针和函数对象。已被加入到C++11标准。
    - function是一个函数对象的“容器”，概念上像是C/C++中函数指针类型的泛化，是一种“智能函数指针”。它以对象的形式封装了原始的函数指针或函数对象，能够容纳任意符合函数签名的可调用对象。因此可以被用于回调机制，暂时保管函数或函数对象，在之后需要的时机再调用，使回调机制拥有更多的弹性。 已被加入到C++11标准。
* 并发编程:
    - atomic实现原子操作。它封装了不同计算机硬件的底层操作原语，提供了跨平台的原子操作功能，让我们完全摆脱并发编程读写变量的困扰。
    - thread库实现了操作系统里的线程表示，赋值启动和管理线程对象。
    - asio库基于前摄器模式封装了操作系统的select、kqueue、poll/epoll、overlapped I/O 等机制，实现了异步IO模型。
