# Python
## Contents
* [Reference](#Reference)
* [Basic](#Basic)

## Reference
* https://docs.python.org/
* http://pandas.pydata.org/
* http://www.numpy.org/
* asyncio:
    - https://github.com/python/cpython/tree/master/Lib/asyncio
    - https://github.com/python/cpython/tree/3.6/Lib/asyncio/
    - https://docs.python.org/3/library/asyncio.html
* https://code.activestate.com/recipes/langs/python/
* https://www.anaconda.com/download/
* 《利用Python进行数据分析，中文版（扫描版）》
* 《Python数据分析基础教程：NumPy学习指南（第2版）》
* 《Python Web开发实战》

#### 视频课程
1. [《Python 成长之路_基础篇视频课程_共20课时-51CTO学院》](https://edu.51cto.com/course/3469.html)  [更多](https://edu.51cto.com/courselist/78/zh5.html#listContent)


## Basic
1. Python解析器：CPython，Jython，IronPython，PyPy，Psyco
2. GIL
    - 参考：https://wiki.python.org/moin/GlobalInterpreterLock
    - In **CPython**, the global interpreter lock, or GIL, is a mutex that prevents multiple native threads from executing Python bytecodes at once. This lock is necessary mainly because CPython's memory management is not thread-safe. (However, since the GIL exists, other features have grown to depend on the guarantees that it enforces.)
    - 一个防止多线程并发执行机器码的Mutex。每个线程在执行的时候都需要先获取GIL，保证同一时刻只有一个线程可以执行代码，即同一时刻只有一个线程使用CPU，也就是说多线程并不是真正意义上的同时执行。
    - 解决GIL锁问题的办法：
        1. 更换CPython为Jython（不建议）
        2. 使用多进程完成多线程的任务
        3. 在使用多线程时可以使用C语言去实现 
    - IO密集型用多线程，如socket，爬虫，web；计算密集型用多进程，如金融分析。
3. 闭包（closure）：引用了自有变量的函数，这个函数保存了执行的上下文，可以脱离原本的作用域独立存在。
4. 装饰器（Decorator）：
    * 类装饰器：
    * 函数装饰器：可以是一个函数，也可以是一个类，其参数都是一个函数。
        - 装饰器函数：他们是修改其他函数的功能的函数，其参数是一个函数（被装饰的），返回值也是一个函数。带参数的装饰器：是因为在函数中嵌入了装饰器，在这个嵌套的装饰器里使用了函数作为参数。
        - 装饰器类：
    * python提供的装饰器
        - @functools.wraps(func)，它能把原函数的元信息拷贝到装饰器里面的 func 函数中。函数的元信息包括docstring、name、参数列表等等。
5. 多线程
    - python多线程间的通讯方式：
        1. 使用全局变量来做判断
        2. Queue模块：queue是线程安全，不加锁，效率高，因为queue用了python中的deque() 双端队列，而deque（）则是线程安全的，在字节码的级别上就已经达到了线程安全。
        3. threading.Event、hreading.Lock()、threading.Rlock()、threading.Condition()
6. 多进程
    - python多进程间的通讯方式：
        1. multiprocessing.Queue()
        2. multiprocessing.Pipe()
        3. socket
7. 设计模式
    -  单例：
        1.  使用模块：因为模块在第一次导入时，会生成 .pyc 文件，当第二次导入时，就会直接加载 .pyc 文件，而不会再次执行模块代码。
        2.  使用装饰器
        3.  使用类：多线程时需要加锁
        4.  基于__new__方法实现（推荐使用，方便）
        5.  基于metaclass方式实现



