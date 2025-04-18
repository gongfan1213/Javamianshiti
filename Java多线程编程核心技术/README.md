### 目录
- **前言**
  
- **第1章 Java多线程技能**
  
    1.1 进程和线程的定义及多线程的优点
  
    1.2 使用多线程
  
        1.2.1 继承Thread类
  
        1.2.2 使用常见的3个命令分析线程的信息
  
        1.2.3 线程随机性的展现
  
        1.2.4 执行start()的顺序不代表执行run()的顺序
  
        1.2.5 实现Runnable接口
  
        1.2.6 使用Runnable接口实现多线程的优点
  
        1.2.7 public Thread(Runnable target)中的target参数
  
        1.2.8 实例变量共享导致的“非线程安全”问题与相应的解决方案
  
        1.2.9 Servlet技术也会引起“非线程安全”问题
  
        1.2.10 留意i++与System.out.println()出现的“非线程安全”问题
  
        1.2.11 方法run()被JVM所调用
  
    1.3 方法currentThread()
  
    1.4 方法isAlive()
  
    1.5 方法sleep(long millis)
  
    1.6 方法sleep(long millis, int nanos)
  
    1.7 方法StackTraceElement[] getStackTrace()
  
    1.8 方法static void dumpStack()
  
    1.9 方法Map<Thread, StackTraceElement[]> getAllStackTraces()
  
    1.10 方法getId()
  
    1.11 停止线程
  
        1.11.1 停止不了的线程
  
        1.11.2 判断线程是不是停止状态
  
        1.11.3 清除中断状态的使用场景
  
        1.11.4 能停止的线程——异常法
  
        1.11.5 在sleep状态下停止
  
  
        1.11.6 使用stop()暴力停止线程
  
        1.11.7 方法stop()与java.lang.ThreadDeath异常
  
        1.11.8 使用stop()释放锁导致数据结果不一致
  
        1.11.9 使用return;语句停止线程的缺点及相应的解决方案
  
    1.12 暂停线程
  
        1.12.1 方法suspend()与resume()的使用
  
        1.12.2 方法suspend()与resume()的缺点——独占
  
        1.12.3 方法suspend()与resume()的缺点——数据不完整
  
        1.12.4 使用LockSupport类实现线程暂停与恢复
  
    1.13 方法yield()
  
    1.14 线程的优先级
  
        1.14.1 线程优先级的继承特性
  
        1.14.2 线程优先级的规律性
  
        1.14.3 线程优先级的随机性
  
        1.14.4 看谁跑得快
  
    1.15 守护线程
  
    1.16 并发与并行
  
    1.17 同步与异步
  
    1.18 多核CPU不一定比单核CPU运行快
  
    1.19 本章小结
  
- **第2章 对象及变量的并发访问**
  
    2.1 synchronized同步方法
  
        2.1.1 方法内的变量是线程安全的
  
        2.1.2 实例变量“非线程安全”问题及解决方案
  
        2.1.3 同步synchronized在字节码指令中的原理
  
        2.1.4 多个对象多个锁
  
        2.1.5 synchronized方法将对象作为锁
  
        2.1.6 脏读与解决
  
        2.1.7 synchronized锁重入
  
        2.1.8 继承环境下的锁重入

  
        2.1.9 出现异常，锁自动释放
  
        2.1.10 非同步方法；不使用synchronized重写方法
  
    2.2 synchronized同步语句块
  
        2.2.1 synchronized方法的弊端
  
        2.2.2 synchronized同步代码块的使用
  
        2.2.3 用同步代码块解决同步方法的弊端
  
        2.2.4 一半异步，一半同步
  
        2.2.5 synchronized代码块间的同步性
  
        2.2.6 方法println()也是同步的
  
        2.2.7 验证synchronized(this)同步代码块是锁定当前对象的
  
        2.2.8 将任意对象作为锁
  
        2.2.9 多个锁就是异步执行
  
        2.2.10 验证方法被调用是随机的
  
        2.2.11 不同步导致的逻辑错误与解决方案
  
        2.2.12 细化验证3个结论
  
        2.2.13 类对象的单例性
  
        2.2.14 静态同步：synchronized方法与synchronized(class)代码块
  
        2.2.15 同步synchronized方法可以对类的所有对象实例起作用
  
        2.2.16 同步synchronized(class)代码块可以对类的所有对象实例起作用
  
        2.2.17 String常量池特性与同步问题
  
        2.2.18 synchronized方法无限等待问题与解决方案
  
        2.2.19 多线程的死锁
  
        2.2.20 内置类与静态内置类
  
        2.2.21 内置类与同步：实验1
  
        2.2.22 内置类与同步：实验2
  
        2.2.23 锁对象改变导致异步执行
  
        2.2.24 锁对象不改变依然是同步执行
  
        2.2.25 同步写法案例比较
  
        2.2.26 方法holdsLock(Object obj)的使用
  
        2.2.27 临界区
  
    2.3 volatile关键字
  
        2.3.1 可见性的测试
  
        2.3.2 原子性与非原子性的测试
  
        2.3.3 禁止代码重排序的测试
  
    2.4 本章小结
  
- **第3章 线程间通信**
  
    3.1 wait/notify机制
  
        3.1.1 不使用wait/notify机制进行通信的缺点
  
        3.1.2 什么是wait/notify机制
  
        3.1.3 wait/notify机制的原理
  
        3.1.4 方法wait()的基本用法
  
        3.1.5 使用代码完整实现wait/notify机制
  
        3.1.6 使用wait/notify机制实现线程销毁
  
        3.1.7 对业务代码进行封装
  
        3.1.8 线程状态的切换
  
        3.1.9 方法wait()导致锁立即释放
  
        3.1.10 方法sleep()不释放锁
  
        3.1.11 方法notify()不立即释放锁
  
        3.1.12 方法interrupt()遇到方法wait()
  
        3.1.13 方法notify()只通知一个线程
  
        3.1.14 方法notifyAll()通知所有线程
  
        3.1.15 方法wait(long)的基本用法
  
        3.1.16 方法wait(long)自动向下运行的条件
  
        3.1.17 通知过早与相应的解决方案
  
        3.1.18 等待条件发生变化
  
        3.1.19 生产者/消费者模式
  
        3.1.20 在管道中传递字节流
  
        3.1.21 在管道中传递字符流
  
        3.1.22 利用wait/notify机制实现交叉备份
  
        3.1.23 方法sleep()和wait()的区别
  
    3.2 方法join()的使用
  
        3.2.1 学习方法join()前的铺垫
  
        3.2.2 用方法join()解决问题
  
        3.2.3 方法join()和interrupt()出现异常
  
        3.2.4 方法join(long)的使用
  
        3.2.5 方法join(long)与sleep(long)的区别
  
        3.2.6 方法join()后的代码提前运行
  
        3.2.7 方法join(long millis, int nanos)的使用
  
    3.3 类ThreadLocal的使用
  
        3.3.1 方法get()与null
  
        3.3.2 类ThreadLocal存取数据流程分析
  
        3.3.3 验证线程变量的隔离性
  
        3.3.4 解决get()返回null的问题
  
        3.3.5 验证重写initialValue()方法的隔离性
  
        3.3.6 使用remove()方法的必要性
  
    3.4 类InheritableThreadLocal的使用
  
        3.4.1 类ThreadLocal不能实现值继承
  
        3.4.2 使用InheritableThreadLocal体现值继承特性
  
        3.4.3 值继承特性在源代码中的执行流程
  
        3.4.4 父线程有最新的值，子线程还是旧值：不可变类型
  
        3.4.5 子线程有最新的值，父线程还是旧值：不可变类型
  
        3.4.6 子线程可以感应对象属性值的变化：可变类型
  
        3.4.7 重写childValue方法实现对继承值的加工
  
    3.5 本章小结
  
- **第4章 锁的使用**
  
    4.1 使用ReentrantLock类
  
        4.1.1 使用ReentrantLock实现同步
  
        4.1.2 验证多代码块间的同步性
  
        4.1.3 方法await()的错误用法与相应的解决方案
  
        4.1.4 使用方法await()和方法signal()实现wait/notify
  
        4.1.5 方法await()暂停的原理
  
        4.1.6 通知部分线程：错误用法
  
        4.1.7 通知部分线程：正确用法
  
        4.1.8 实现生产者/消费者模式一对一交替打印
  
        4.1.9 实现生产者/消费者模式多对多交替打印
  
        4.1.10 公平锁与非公平锁
  
        4.1.11 方法getHoldCount()的使用
  
  
        4.1.12 方法getQueueLength()的使用
  
        4.1.13 方法getWaitQueueLength(Condition condition)的使用
  
        4.1.14 方法hasQueuedThread(Thread thread)的使用
  
        4.1.15 方法hasQueuedThreads()的使用
  
        4.1.16 方法hasWaiters(Condition condition)的使用
  
        4.1.17 方法isFair()的使用
  
        4.1.18 方法isHeldByCurrentThread()的使用
  
        4.1.19 方法isLocked()的使用
  
        4.1.20 方法lockInterruptibly()的使用
  
        4.1.21 方法tryLock()的使用
  
        4.1.22 方法tryLock(long timeout, TimeUnit unit)的使用
  
        4.1.23 方法await(long time, TimeUnit unit)的使用
  
        4.1.24 方法awaitNanos(long nanosTimeout)的使用
  
        4.1.25 方法awaitUntil(Date deadline)的使用
  
        4.1.26 方法awaitUninterruptibly()的使用
  
        4.1.27 实现线程按顺序执行业务
  
    4.2 使用ReentrantReadWriteLock类
  
        4.2.1 类ReentrantLock的缺点
  
        4.2.2 读读共享
  
        4.2.3 读写互斥
  
        4.2.4 写读互斥
  
        4.2.5 写写互斥
  
    4.3 本章小结
  
- **第5章 定时器**
- 
    5.1 定时器的使用
  
        5.1.1 方法schedule(TimerTask task, Date time)的测试
  
        5.1.2 方法schedule(TimerTask task, Date firstTime, long period)的测试
  
        5.1.3 方法schedule(TimerTask task, long delay)的测试
  
        5.1.4 方法schedule(TimerTask task, long delay, long period)的测试
  
        5.1.5 方法scheduleAtFixedRate(TimerTask task, Date firstTime, long period)的测试
  
    5.2 本章小结
  
- **第6章 单例模式与多线程**
  
    6.1 单例模式与多线程
  
        6.1.1 立即加载/饿汉模式
  
        6.1.2 延迟加载/懒汉模式
  
        6.1.3 使用静态内置类实现单例模式
  
        6.1.4 序列化与反序列化的单例模式实现
  
        6.1.5 使用static代码块实现单例模式
  
        6.1.6 使用enum枚举数据类型实现单例模式
  
        6.1.7 完善使用enum枚举实现单例模式
  
    6.2 本章小结
  
- **第7章 拾遗增补**
  
    7.1 线程的状态
  
        7.1.1 验证NEW、RUNNABLE和TERMINATED
  
        7.1.2 验证TIMED_WAITING
  
        7.1.3 验证BLOCKED
  
        7.1.4 验证WAITING
  
    7.2 线程组
  
        7.2.1 线程对象关联线程组：一级关联
  
        7.2.2 线程对象关联线程组：多级关联
  
        7.2.3 线程组自动归属特性
  
        7.2.4 获取根线程组
  
        7.2.5 线程组内加线程组
  
        7.2.6 组内的线程批量停止
  
        7.2.7 递归取得与非递归取得组内对象
  
    7.3 Thread.activeCount()方法的使用
  
    7.4 Thread.enumerate(Thread tarray[])方法的使用
  
    7.5 再次验证线程执行有序性
  
    7.6 类SimpleDateFormat非线程安全
  
        7.6.1 出现异常
  
        7.6.2 解决方法1
  
        7.6.3 解决方法2
  
    7.7 线程中出现异常的处理
  
        7.7.1 线程出现异常的默认行为
  
        7.7.2 使用setUncaughtExceptionHandler()方法进行异常处理
  
        7.7.3 使用setDefaultUncaughtExceptionHandler()方法进行异常处理
  
    7.8 线程组内处理异常
  
    7.9 线程异常处理的优先级
  
    7.10 本章小结
  
- **第8章 并发集合框架**
  
    8.1 集合框架结构
  
        8.1.1 接口Iterable
  
        8.1.2 接口Collection
  
        8.1.3 接口List
  
        8.1.4 接口Set
  
        8.1.5 接口Queue
  
        8.1.6 接口Deque
  
    8.2 非阻塞队列
  
        8.2.1 类ConcurrentHashMap的使用
  
        8.2.2 类ConcurrentSkipListMap的使用
  
        8.2.3 类ConcurrentSkipListSet的使用
  
        8.2.4 类ConcurrentLinkedQueue的使用
  
        8.2.5 类ConcurrentLinkedDeque的使用
  
        8.2.6 类CopyOnWriteArrayList的使用
  
        8.2.7 类CopyOnWriteArraySet的使用
  
    8.3 阻塞队列
  
        8.3.1 类ArrayBlockingQueue的使用与公平/非公平锁
  
        8.3.2 类PriorityBlockingQueue的使用
  
        8.3.3 类LinkedBlockingQueue的使用
  
        8.3.4 类LinkedBlockingDeque的使用
  
        8.3.5 类SynchronousQueue的使用
  
        8.3.6 类DelayQueue的使用
  
        8.3.7 类LinkedTransferQueue的使用
  
    8.4 本章小结
  
- **第9章 线程池类ThreadPoolExecutor的使用**
- 
    9.1 Executor接口介绍
  
    9.2 使用Executors工厂类创建线程池
  
        9.2.1 使用newCachedThreadPool()方法创建无界线程池
  
        9.2.2 验证newCachedThreadPool()方法创建线程池和线程复用特性
  
        9.2.3 使用newCachedThreadPool(ThreadFactory)方法定制线程工厂
  
        9.2.4 使用newCachedThreadPool()方法创建无界线程池的缺点
  
        9.2.5 使用newFixedThreadPool(int)方法创建有界线程池
  
        9.2.6 使用newSingleThreadExecutor()方法创建单一线程池
  
    9.3 ThreadPoolExecutor类的使用
  
        9.3.1 队列LinkedBlockingQueue、ArrayBlockingQueue和SynchronousQueue的基本使用
  
        9.3.2 构造方法参数详解
  
        9.3.3 方法shutdown()和shutdownNow()
  
        9.3.4 方法List<Runnable>
