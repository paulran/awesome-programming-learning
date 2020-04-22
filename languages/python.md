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
1. 多线程
    - python多线程间的通讯方式：
        1. 使用全局变量来做判断
        2. Queue模块：queue是线程安全，不加锁，效率高，因为queue用了python中的deque() 双端队列，而deque（）则是线程安全的，在字节码的级别上就已经达到了线程安全。
        3. threading.Event、hreading.Lock()、threading.Rlock()、threading.Condition()
2. 多进程
    - python多进程间的通讯方式：
        1. multiprocessing.Queue()
        2. multiprocessing.Pipe()
        3. socket



