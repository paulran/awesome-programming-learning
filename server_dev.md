

## Socket
* TCP与UDP的发送缓冲区：
    * 每个TCP套接口有一个发送缓冲区，可以用SO_SNDBUF套接口选项来改变这一缓冲区的大小。当应用进程调用write往套接口写数据时，内核从应用进程缓冲区中拷贝所有数据到套接口的发送缓冲区，如果套接口发送缓冲区容不下应用程序的所有数据，或者是应用进程的缓冲区大于套接口的发送缓冲区，或者是套接口的发送缓冲区中有别的数据，应用进程将被挂起。内核将不从write返回。直到应用进程缓冲区中的所有数据都拷贝到套接口发送缓冲区。所以，从写一个TCP套接口的write调用成功返回仅仅表示我们可以重新使用应用进程缓冲区，它并不是告诉我们对方收到数据。TCP发给对方的数据，对方在收到数据时必须给矛确认，只有在收到对方的确认时，本方TCP才会把TCP发送缓冲区中的数据删除。
    * UDP因为是不可靠连接，不必保存应用进程的数据拷贝，应用进程中的数据在沿协议栈向下传递时，以某种形式拷贝到内核缓冲区，当数据链路层把数据传出后就把内核缓冲区中数据拷贝删除。因此它不需要一个发送缓冲区。写UDP套接口的write返回表示应用程序的数据或数据分片已经进入链路层的输出队列，如果输出队列没有足够的空间存放数据，将返回错误ENOBUFS。
    * 阻塞和非阻塞的send和recv：这是因为对应的socket被设置为了阻塞的（~O_NONBLOCK）或者非阻塞的（O_NONBLOCK）。 在select、epoll等模型中使用时，一般需要把socket设置为O_NONBLOCK。
        * 用以下方法将socket设置为非阻塞方式：
            - int flags = fcntl(socket, F_GETFL, 0);
            - fcntl(socket, F_SETFL, flags | O_NONBLOCK);
        * 将非阻塞的设置回阻塞可以用：
            - int flags = fcntl(socket, F_GETFL, 0);
            - fcntl(socket, F_SETFL, flags & ~O_NONBLOCK);
* TCP连接断开的检测：
    * 客户端通过send的返回值检测，服务端通过recv的返回值检测（断开连接可作为一个 READ 事件被处理）。
* Nonblocking，event demultiplexer。 一般地,I/O多路复用机制都依赖于一个事件多路分离器(Event Demultiplexer)。
* select：
    * takes three sets of fds (implemented as bit arrays): one for reading, one for writing, and one for "exceptions".
    * scales terribly when the number of sockets is high. 
    * generating and reading the bit arrays can be made to take time proportional to the number of fds that you provided for select().
    * Linux select实现的TCP echo： http://blog.csdn.net/god2469/article/details/8761346
* poll
    * not 0(1)
* epoll
    * give O(1) performance for adding a socket, removing a socket, and for noticing that a socket is ready for IO.
    * When the remote host closes the socket, epoll() reports both a HUP and an EPOLLIN for the file descriptor.
    * 两种模式：
        1. 水平触发(Level Triggered)模式：
            1. LT是默认的模式。LT模式下，只要这个fd还有数据可读，每次epoll_wait都会返回它的事件，提醒用户程序去操作。
        2. 边缘触发(Edge Triggered)模式：
            1. ET是“高速”模式。ET模式中，它只会提示一次，直到下次再有数据流入之前都不会再提示了，无论fd中是否还有数据可读。所以在ET模式下，read一个fd的时候一定要把它的buffer读光，也就是说一直读到read的返回值小于请求值，或者遇到EAGAIN错误。
    * events
        1. EPOLLIN: The associated file is available for read(2) operations.
        2. EPOLLOUT: The associated file is available for write(2) operations.
        3. EPOLLRDHUP (since Linux 2.6.17): Stream socket peer closed connection, or shut down writing half of connection.  (This flag is especially
useful for writing simple code to detect peer shutdown when using Edge Triggered monitoring.)
        4. EPOLLPRI: There is urgent data available for read(2) operations.
        5. EPOLLERR: Error condition happened on the associated file descriptor.  epoll_wait(2) will  always  wait  for  this
event; it is not necessary to set it in events.
        6. EPOLLHUP: Hang  up  happened on the associated file descriptor.  epoll_wait(2) will always wait for this event; it
is not necessary to set it in events.
        7. EPOLLET: Sets the Edge Triggered behavior for the associated file descriptor.  The default behavior for epoll  is
Level  Triggered.   See epoll(7) for more detailed information about Edge and Level Triggered event dis-
tribution architectures.
        8. EPOLLONESHOT (since Linux 2.6.2): Sets the one-shot behavior for the associated file descriptor.  This means that after an event is pulled
out with epoll_wait(2) the associated file descriptor is internally disabled and no other events will be
reported by the epoll interface.  The user must call epoll_ctl() with EPOLL_CTL_MOD to re-arm  the  file
descriptor with a new event mask.
    * epoll的各个事件触发条件测试
        1. listen fd，有新连接请求，触发EPOLLIN。
        2. 对端发送普通数据，触发EPOLLIN。
        3. 带外数据，只触发EPOLLPRI。
        4. 对端正常关闭（程序里close()，shell下kill或ctr+c），触发EPOLLIN和EPOLLRDHUP，但是不触发EPOLLERR和EPOLLHUP。关于这点，以前一直以为会触发EPOLLERR或者EPOLLHUP。 再man epoll_ctl看下后两个事件的说明，这两个应该是本端（server端）出错才触发的。
        5. 对端异常断开连接（只测了拔网线），没触发任何事件。 这个时候增加一个超时检查——给这个关闭的socket发送心动包，会触发EPOLLERR。 即对一个已经关闭的socket执行读写操作会触发EPOLLERR。
        6. 当socket的一端认为对方发来了一个不存在的4元组请求的时候，会回复一个RST响应，在epoll上会响应为EPOLLHUP事件,目前我已知的两种情况会发响应RST：
            1. 当客户端向一个没有在listen的服务器端口发送的connect的时候 服务器会返回一个RST 因为服务器根本不知道这个4元组的存在。
            2. 当已经建立好连接的一对客户端和服务器，客户端突然操作系统崩溃，或者拔掉电源导致操作系统重新启动(kill pid或者正常关机不行的,因为操作系统会发送FIN给对方)，这时服务器在原有的4元组上发送数据，会收到客户端返回的RST，因为客户端根本不知道之前这个4元组的存在。
    * 问题小计：
        * epoll_wait返回-1，errno为9，Bad file descriptor。EBADF epfd is not a valid file descriptor.  在epoll_ctl EPOLL_CTL_ADD listenfd之后调用的epoll_wait，另外程序创建了200个子线程。
* IOCP
    * IOCP does not alert your program when a socket is ready for an operation that your program then has to perform. Instead, the program tells the Windows networking stack to start a network operation, and IOCP tells the program when the operation has finished.
* 网络连接由谁来关闭？ 服务器端还是客户端？
    * 如果由客户端来关闭，那么当客户端出问题了，没有正常关闭时，服务器端应该怎么处理？ 心跳检查吗？
* 网络编程：高性能I/O设计模式Reactor和Proactor
* 《网络编程：Reactor与Proactor的概念》： http://www.cnblogs.com/dawen/archive/2011/05/18/2050358.html。
    * 系统I/O方式可分为阻塞，非阻塞同步（Reactor）和非阻塞异步（Proactor）三类，三种方式中，非阻塞异步模式的扩展性和性能最好。
    * Reactor模式采用同步IO，而Proactor采用异步IO。
* 《高性能I/O设计模式Reactor和Proactor》： http://blog.csdn.net/roger_77/article/details/1555170。
    * 实现现状：ACE Proactor和ACE Reactor，Boost.Asio库实现的异步IO（Proactor模式）。
* Reactor通过某种变形，可以将其改装为Proactor，在某些不支持异步IO的系统上，也可以隐藏底层的实现，利于编写跨平台代码。我们只需要在dispatch(也就是demultiplexor)中封装同步IO操作的代码，在上层，用户提交自己的缓冲区到这一层，这一层检查到设备可操作时，不像原来立即回调handler，而是开始IO操作，然后将操作结果放到用户缓冲区(读)，然后再回调handler。这样，对于上层handler而言，就像是proactor一样。 => ACE Proactor在windows平台上具有更为优异的性能表现，因为windows在操作系统提供了高效的异步API支持（IOCompletion）。 在Linux上，Reactor模式和Proactor模式的性能差不多，因为Linux上的Proactor模式是由Reactor模式改装的。
* Linux AIO: http://www.cnblogs.com/evlon/archive/2007/08/13/853145.html。


## 分布式系统
* 定义：一个硬件或软件组件分布在网络计算机上，仅仅通过消息传递进行通信和动作协调的系统。
* CAP定理：又称CAP原则，指的是在一个分布式系统中，Consistency（一致性）、 Availability（可用性）、Partition tolerance（分区容错性），最多只能同时三个特性中的两个，三者不可兼得。
* 集群、分布式、微服务概念和区别
    - 分布式是指将不同的业务分布在不同的地方。而集群指的是将几台服务器集中在一起，实现同一业务。
    - 分布式是以缩短单个任务的执行时间来提升效率的，而集群则是通过提高单位时间内执行的任务数来提升效率。
    - 简单的说，微服务是架构设计方式，分布式是系统部署方式，两者概念不同。
    - 微服务相比分布式服务来说，它的粒度更小，服务之间耦合度更低，由于每个微服务都由独立的小团队负责，因此它敏捷性更高，分布式服务最后都会向微服务架构演化，这是一种趋势， 不过服务微服务化后带来的挑战也是显而易见的，例如服务粒度小，数量大，后期运维将会很难。
* 拜占庭将军问题：n 个将军被分隔在不同的地方，忠诚的将军希望通过某种协议达成某个命令的一致（比如一起进攻或者一起后退）。但其中一些背叛的将军会通过发送错误的消息阻挠忠诚的将军达成命令上的一致。Lamport 证明了在将军总数大于3m ，背叛者为m 或者更少时，忠诚的将军可以达成命令上的一致。
* 分布式文件系统主要设计问题包括：
    1. 有效使用客户缓存
    2. 当更新文件时，维持多个副本的一致性
    3. 出错后的恢复
    4. 读写不同数据量的文件的高吞吐量
    5. 可伸缩性
* 分布式互斥服务有3种算法：
    1. 中央服务器算法
    2. 基于环的算法
    3. 基于组播的使用逻辑时钟的算法
* 并发控制协议基于串行相等的标准，起源于用于解决操作间冲突的规则，主要有以下3种方法：
    1. 锁用于在多个事务访问同一个对象时，根据这些操作访问同一个对象的先后次序给事务排序。
    2. 乐观并发控制不会阻塞事务运行，只是在提交时通过检查来确定已执行的操作是否存在冲突。
    3. 时间戳排序利用时间戳将访问同一个对象的事务根据其起始时间进行排序。
* 分布式事务处理策略（两阶段）：
    * 两阶段提交协议假设每个数据库点都存在一个write-ahead log，每一次的write请求都是先记log后才真正执行写入。
        * 第一阶段为提交请求阶段（Commit-request phase）：
            1. 事务管理器给所有数据库发query to commit消息请求，然后开始等待回应；
            2. 数据库如果可以提交属于自己的事务分支，则将自己在该事务分支中所做的操作固定记录下来（在undo log和redo log中各记一项）；
            3. 数据库都回应是否同意提交的应答。
        * 第二阶段为提交阶段（Commit phase）：
            - 如果事务管理器收到的所有回应都是agreement，
                1. 事务管理器记日志并给所有数据库发commit消息请求；
                2. 各个数据库执行操作，释放所有该事务相关的锁和资源；
                3. 各个数据库给事务管理器回复；
                4. 当收到所有回复，事务管理器结束当前事务
            - 如果事务管理器收到的任一回应是abort，
                1. 事务管理器记日志并给所有数据库发rollback消息请求；
                2. 各个数据库执行undo操作，释放所有该事务相关的锁和资源；
                3. 各个数据库给事务管理器回复；
                4. 当收到所有回复，事务管理器结束当前事务
    * 利用两阶段加锁方式的分布式事务会导致分布式死锁。分布式死锁检测的目的是在全局等待图中寻找环路。一旦某个环路被发现，一个或者多个事务被放弃来解除死锁。
* 在分布式系统中，复制是高可用和容错的关键技术。
* 定义线性化和顺序一致性作为容错服务的正确准则。
* 在被动（主备份）复制中，通过直接将所有的请求发送到一个选出的副本管理器，并在其出故障时选出一个备份代替它，可以获得容错。
* 在主动复制中，所有副本管理器独立地处理所有的请求。
* 通过组通信，可以方便地实现这两种复制形式。
* 多媒体应用程序实时地生成和消耗连续的数据流，因而需要新的系统机制。这些机制中最重要的是服务质量管理。
* QoS请求指定了多媒体数据流可接受的带宽，延迟和丢失率。
* 分布式数据访问层（数据库中间件）：
    - MySQL：OneProxy、Atlas、cobar、TDDL
* 分布式一致性算法：Paxos、Raft

## 高性能服务器编程 —— 低延迟，高并发。  单机维持350万长链接
### 设计
* 网络层的设计层面网络层设计主要考虑三个方面，一是连接方式，是使用TCP还是UDP；二是多路利用，选择合适的网络IO机制，如select,epoll等。 三是协议解析，协议的消息传递其实与网络层无关，只是数据打包，解包的过程，但通常也归纳在这。这是为了突出业务的重要性。
* 服务器运行：要满足更严格的质量属性要求，如可用性，鲁棒性，高性能，可配置性等。
* 常用技术线程：线程封装，使用线程池
* 网络IO策略： 如select, epoll, pull, BSD下的kqueue, solaris下的/dev/null, windows下的完成端口等，合适的IO策略对性能有较大的影响。
* 消息队列：常使用消息队列来延迟数据处理，如数据包接收的存储，请求任务的存储。
* 状态机：服务器的状态要经常监控。
* 内存池: 内存池的设计是为了循环使用内存资源，可根据不同场景设计不同的内存池模型。
* 其它： 如日志， XML处理，数据库操作等。
* 分布式、去中心化、无状态化、一致性哈希、……

### high-performance server architecture
+ The C10K problem
+ 从 C10K 到 C500K
+ 总结分析了影响服务器性能的四大杀手：
    + 数据拷贝（Data Copies）
        + 池式技术
            + Memory pool
            + Object pool
        + 读写技术
            + Prefetch
            + Delay write
            + Copy On Write
            + 空间换时间
            + 双缓冲技术
    + 环境切换（Context Switches）
    + 内存分配（Memory allocation）
    + 锁竞争（Lock contention）
        + 锁相关技术
            + 进程锁 vs 线程锁
            + 加锁的粒度：库级锁/表级锁/页级锁/行级锁
            + 各种锁系统开销：互斥锁/读写锁/意向锁/自旋锁
            + 分布式加锁算法：例如基于数据库实现，基于zookeeper实现，如果吞吐量还是不能满足，比较广泛的做法是用分布式缓存（Redis等）来实现。
            + 锁表的实现
            + Lock free
            + 免锁模型：环形缓冲区
            + 万有一失的无锁设计
+ Topics in High-Performance Messaging
    + 讨论了TCP延时和UDP缓冲，以及流量控制的一些话题：
        + TCP Latency
        + UDP Buffer
        + Multicast
+ 典型网络模型
    + Reactor-同步
    + Proactor-异步
    + 半同步半异步
    + 领导者/追随者
    + A Design Framework for Highly Concurrent Systems
        + 在tasks，queues，thread pools等设计元素的基础上提出了常用的高并发系统设计模式。
+ 性能参数（系统吞吐量）：
    + QPS（TPS）：每秒查询率(Query Per Second)。  10w+？
    + 并发数： 系统同时处理的request/事务数
    + 响应时间：  一般取平均响应时间
    + 日PV
    
### 高可用性（HA，High Availability）、高可运维性
* 可用性的计算公式：
    * %availability=（Total Elapsed Time－Sum of Inoperative Times）/ Total Elapsed Time
    * elapsed time为operating time+downtime。
* 在统计中表明，造成非计划的宕机因素并非都是硬件问题。硬件问题只占40%，软件问题占30%，人为因素占20%，环境因素占10%。您的高可用性系统应该能尽可能地考虑到上述所有因素。
* 导致计划内的停机因素有：周期性的备份、软件升级、硬件扩充或维修、系统配置更改、数据更改等。
* 导致计划外停机的因素有：硬件失败、文件系统满错误、内存溢出、备份失败、磁盘满、供电失败、网络失败、应用失败、自然灾害、操作或管理失误。
* 运维目标：快速的调查和修复错误。
* 如何做到：
    * 系统架构
        * 在UNIX系统上创建高可用性计算机系统，业界的通行做法，也是非常有效的做法，就是采用群集系统（Cluster），将各个主机系统通过网络或其他手段有机地组成一个群体，共同对外提供服务。创建群集系统，通过实现高可用性的软件将冗余的高可用性的硬件组件和软件组件组合起来，消除单点故障。
    * （IDC级的）容灾
        * 服务站点的负载均衡和异地容灾，为保证客户在任何时刻都能够访问到内容，要求解决国际网络接入链路和单个IDC的单点故障，保证在ISP服务提供商出现不可抗拒因素而中断服务时可以及时监控发现，并且报警显示；也保证任何一个IDC机房出现不可抗拒因素而中断服务时也可以及时监控发现并且报警显示，在得到行政命令后可以非常迅速的切换到可用的站点。
        * 服务器的应用状态监控，准确的根据服务器的实际服务状况提供报警信息，并提供服务器的负载均衡，要求在正常情况下两台或多台服务器的负载基本相同，在某台服务器停机的情况下透明的容错，保证关键服务的持续，提供特别的会话保持能力，可以根据不同应用的特点保证个别用户的访问定位在特定的服务器，只有在这台服务器出现故障时再将访问导向到其他服务器，并且和中间件服务器配合，避免用户访问会话的丢失。
        * 灵活的扩展空间，根据实际应用的需求灵活投资，提高整体服务能力。
    * 灰度发布
        * 线上系统分批（分用户分号段）发布、升级
        * 发布必须可回滚
        * 滚动升级：初始版本的集群 -> 混合版本的群集 -> 更高级版本的集群
    * 运营监控
        * 强监控，及时处理
    * 运维操作Web化（半自动化）、自动化
        * Ansible
            * 不需要安装 agent，只依赖 SSH
            * 安装脚本模块化，方便积累、分享
            * 与类 puppet 的区别？
* 要想实现高可用，需要做到这几点：入口层做心跳，业务层服务器无状态，缓存层减小粒度，数据库做一个主从模式。

### 负载均衡（LB，Load Balance）
* LVS（Linux Virtual Server，Linux虚拟服务器）是在自由软件Linux操作系统平台上研究开发的一个面向Web服务的集群系统，它是建立在由多个real server（实服务器）构成的集群系统之上的、提供实服务器之间负载平衡、具有高度可扩展和高可用特性的服务器集群系统。其结构对最终用户是透明的，最终用户看到的仅仅是一个单一的虚拟服务器。
* 负载均衡器（LVS, Nginx, HAProxy, ... 虚IP(VIP) ）的问题：
    * 单点失效
    * 单点性能瓶颈
* 域名负载的问题
    * 域名系统不可靠
    * 更新延迟大
* 静态分片（Uid % N）
    * 单点失效：计算值对应的服务可能失效
    * 后期维护成本高：扩容时 N 会变化，这个时候需要对原有数据进行转移
* Ticket Service，动态分配接入点
    * 有一个 Ticket Service，负责管理所有逻辑服务，客户端连接某个逻辑服务之前，先从 Ticket Service 获取一个逻辑服务的地址。
 
### keep-alive
* http keep-alive：
    * http keep-alive是为了让tcp活得更久一点，以便在同一个连接上传送多个http，提高socket的效率。
    * Httpd守护进程，一般都提供了keep-alive timeout时间设置参数。比如nginx的keepalive_timeout，和Apache的KeepAliveTimeout。这个 keepalive_timout时间值意味着：一个http产生的tcp连接在传送完最后一个响应后，还需要hold住 keepalive_timeout秒后，才开始关闭这个连接。当httpd守护进程发送完一个响应后，理应马上主动关闭相应的tcp连接，设置 keepalive_timeout后，httpd守护进程会想说：”再等等吧，看看浏览器还有没有请求过来”，这一等，便是 keepalive_timeout时间。如果守护进程在这个等待的时间里，一直没有收到浏览发过来http请求，则关闭这个http连接。 （服务端httpd守护进程发完响应后，在没有设置keepalive_timeout的情况下会立即主动关闭tcp连接。）
* tcp keep-alive：
    * tcp keep-alive是TCP的一种检测TCP连接状况的保鲜机制。
    * SO_KEEPALIVE的三个参数：
        * tcp_keepalive_intvl，保活探测消息的发送频率。默认值为75s。
发送频率tcp_keepalive_intvl乘以发送次数tcp_keepalive_probes，就得到了从开始探测直到放弃探测确定连接断开的时间，大约为11min。
        * tcp_keepalive_probes，TCP发送保活探测消息以确定连接是否已断开的次数。默认值为9（次）。注意：只有设置了SO_KEEPALIVE套接口选项后才会发送保活探测消息。
        * tcp_keepalive_time，在TCP保活打开的情况下，最后一次数据交换到TCP发送第一个保活探测消息的时间，即允许的持续空闲时间。默认值为7200s（2h）。
* Keepalived：
    * Keepalived is a routing software written in C. The main goal of this project is to provide simple and robust facilities for loadbalancing and high-availability to Linux system and Linux based infrastructures.
    * http://www.keepalived.org/
    * 主要用作 RealServer 的健康状态检查以及 LoadBalance 主机和 BackUP 主机之间 failover 的实现。 
    * nginx+keepalive主从双机热备+自动切换解决方案： http://www.linuxde.net/2013/04/13381.html




## 参考书
1. 《分布式系统：概念与设计》(Distributed Systems:Concepts and Design)
2. 《分布式算法》(DISTRIBUTED ALGORITHMS) Nancy A. Lynch


