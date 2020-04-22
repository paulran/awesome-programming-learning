# Go
## Contents
* [Reference](#Reference)
* [开发环境](#开发环境)

## Reference
1. https://golang.org/
2. https://go-zh.org/doc/
3. https://www.zhihu.com/question/30461290?sort=created
4. https://blog.51cto.com/cyberpunkzym/1915406?utm_source=oschina-app
5. 《Go语言编程》本书作者背景极强，许式伟为原金山WPS首席架构师、曾是盛大创新院研究员，目前是国内Go语言实践圈子公认的Go语言专家。参与本书写作的几位作者都是实际用Go语言开发的项目的开发人员，有较强的实战经验。本书以介绍Go语言特性为主，示例则尽量采用作者开发团队平常的实践，内容涉及内存管理（堆和栈）、错误处理、OOP、并发编程等关键话题。 这本书面向的读者是所有打算用Go语言的开发者，主要包括目前使用C、C++、Java、C#的开发人员，甚至一些Python、PHP开发人员也可能转为 Go 程序员。


## 开发环境
### Go语言Eclipse开发环境搭建
参考：https://blog.csdn.net/weixin_43103088/article/details/82285167
1. 安装JDK or JRE。
2. 安装Eclipse：采用Eclipse开发GO语言时，Eclipse必须安装CDT（CDT是eclipse C/C++开发IDE插件），所以我们直接安装eclipse的C/C++ IDE 编辑器即可。
3. 安装GoClipse插件。
4. 安装Go模块下的tools三个小插件：
    1. gocode是代码自动补全程序：
        1. 打开CMD命令行，进入GO安装目录的src文件夹下，运行以下命令
            * 获取gocode: go get -u github.com/nsf/gocode
            * 编译gocode: go build github.com/nsf/gocode
        2. 将gocode.exe拷贝到Go安装目录的bin文件下，我的目录是安装在C盘，所以直接拷贝到C:\Go\bin
    2. guru代码导航程序:
        1. 下载guru源码https://github.com/golang/tools，把从github上下载下来的tools文件夹移位到go/src/vendor/golang.org/x/ 文件夹下（没有可自建）
        2. 再命令行中进入src目录，然后输入命令开始编译: go install golang.org/x/tools/cmd/guru
        3. 检查是否编译成功，直接去bin目录下查看即可，编译成功会出现一个guru.exe的文件。
    3. godef代码跳转程序（ctrl+鼠标左击，查看定义的代码）：
        1. 下载godef源码https://github.com/rogpeppe/godef，同上解压文件，接着在src目录下新建文件夹如下go/src/github.com/rogpeppe/godef,随后将解压的文件内容拷贝到新建的文件夹下，最后在命令行进入src目录，然后输入命令开始编译，大功告成。 go install github.com/rogpeppe/godef
    4. gdb64支持debug的程序。

## 基础
1. 协程
    * 协程是编译器来保证的，线程是操作系统来调度的；协程能更好的保证流程，而线程需要仔细处理公共数据资源；协程本质是单线程的，能提高单个CPU core的性能，适合计算密集型的应用；协程切换非常轻量，所以能大量的使用，线程切换成本较高，一般要控制线程数。
    * 协程的概念和原理明白了后，其实有点类似于异步事件驱动，可以看看libevent/libuv等经典的C语言库，发现大量使用了异步事件回调，有一个event_loop来循环检查要处理的事件，不过就是代码相对协程恶心了一些，协程能让用户以类似同步的方式来处理异步流程，更加便于理解。
    * 不过协程也是有缺点的，无法利用现代多核机器的性能，所以还需要配合多进程来提高性能；有阻塞操作的时候依然会阻塞整个进程，需要用其他方式来解决。
    * 还有一个东西是actor，这个是更符合人类思维的方式，属于类似消息通知的方式来处理异步事件，可惜现在没有非常好用的actor cpp库，其他语言有类似的库，例如Java的akka, .net的Akka.net, Orleans/Service fabric等。
    * 在 Go 出现之前，协程其实并不是一个新鲜事物，操作系统早就提供底层的 API 支持，如 UNIX 下的 getcontext/makecontext/swapcontext 就是用来切换协程的上下文的，只是没有引人们的足够重视而已。其实 Go 语言的作者之一 Russ Cox 早在 05 年就用 C 语言实现了一个协程的模型库：libtask，里面实现了协程调度、协程锁、协程管道等功能。这些功能点在 Go 语言也能看到一些影子。在参考了众多 C/C++ 的协程库实现后，决定改造 libtask，形成一套真正独立的协程库，可方便程序员方便快捷地编写网络协程程序。






