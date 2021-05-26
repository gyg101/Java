## 并发编程的艺术

​	TARGET：

> ​		1、What——熟悉并掌握并发框架中各工具类(接口)的使用场景及如何应用；
>
> ​		2、How——了解线程池中的实现原理，并与其他相关接口进行对比
>
> ​		3、Why——了解并发工具类的实现原理、相关策略与算法
>

## Chapter 1： 并发编程的挑战

​	并发编程的目标：让程序运行得更快

​		面临的问题与挑战：上下文切换、死锁的问题、受限于硬件和软件的资源限制

### 一：上下文切换

多线程——》上下文切换——》solve: 1. 无锁并发编程；2.CAS算法；3. 使用最少线程和使用协程

> 1. 无锁并发编程：eg: 将数据的ID按照Hash算法取模分段，不同线程处理不同段的数据
> 2. CAS算法：利用java并发包中Atomic包使用CAS算法来更新数据，不需要锁
> 3. 使用最少线程：避免使用不需要的线程
> 4. 协程：在单线程中实现多任务的调度

### 二：线程死锁

> ​	线程之间相互等待对方释放锁对象——》死锁——》solve: 
>
> 1.避免一个线程内部获取多个锁对象
>
> 2.避免一个线程在锁内同时占用多个资源
>
> 3.尝试使用定时锁，tryLock(int timeout);
>
> 4.对于数据库锁，资源的获取(加锁)和释放(解锁)应在一个数据库连接里，否则出现解锁失败的情况。

### 三：资源限制

def: 

​	硬件资源的限制：带宽的上传和下载速度、硬盘读写速度、CPU的处理速度

​	软件资源的限制：数据库的连接数和Socket连接数

what 引发的问题：

​	串行执行——》并行执行——》受制于资源——》产生上下文切换和资源调度——》引起速度下降

how ：

​	使用集群并行执行程序，通过“数据ID % 机器数”得到的机器编号来指定对应的机器执行这笔数据

​	**summary: 使用JDK并发包中提供的并发容器和工具类来解决并发问题。**

## Chapter 2: 	java并发机制的使用原理

### 一： volatile 的应用

> ​	compare: 轻量级的Synchronized , 保证了共享变量的“可见性”
>
> ​	def: 确保共享变量能够被准确和一致的更新，线程应确保通过排它锁单独获取该变量。
>
> ​	eg: 声明为Volatile的字段，java线程内存模型确保所有线程看到这个变量的值是一致的。
>
> ​	实现原则：1. Lock前缀指令会引起处理器缓存写回内存；
>
> ​			2. 一个处理器的缓存写回内存会导致其他线程中的缓存失效。
>
> 使用优化：追加字节优化性能
>
> ​	why: 增加LinkedTransforeQueue优化入队和出队性能
>
> ​		追加到64字节的方式来填满高速缓存区中的缓存行，避免头结点和尾节点加载到同一个缓存行中，使头结点与未节点在修改时不互相锁定；对于缓存行非64字节宽的处理器，共享变量不会频繁的写。
>

### 二：synchronized的实现原理与应用

​	1.实现同步的基础：java中的每一个对象都可以作为锁

> ​		普通同步方法：锁是当前实例对象
>
> ​		静态同步方法：锁是当前类的Class对象
>
> ​		同步方法块：锁是Synchronized括号内配置的对象
>

​	diff: JVM基于进入和退出monitor对象来实现方法同步和代码块同步：但是实现细节不同；

​		后者是通过monitorenter和monitorexit指令实现的，前者也可以通过使用这两个指令实现。

#### 	java对象头：

​	synchronized用的锁是存储在对象头中的；

​		对象是数组类型：JVM用三个字宽存储对象头

​		对象是非数组类型：JVM用两个字宽存储对象头。

**Mark Word里默认存储对象的HashCode、分代年龄和锁标记位。**

2.锁的升级与对比：

​	reason: 锁标志位的变化——》锁对象头中存储的数据变化——》锁升级

锁的四种状态：无锁状态、偏向锁状态、轻量级锁状态、重量级锁状态

锁可以升级但不能降级。这样是为了提高获取锁和释放锁的效率。

①偏向锁：大多数情况下，锁不仅不存在多线程竞争，而且总是由同一个线程多次获得。此时为了让线程获得锁的代价更低引入偏向锁。

 process: 当一个线程访问同步块并获取锁时，会在对象头和栈帧中的锁记录里存储锁偏向的线程ID，以后在该线程进入和退出同步块时不需进行CAS操作来进行加锁和解锁，只需测试对象头中的Mark word中是否存储着当前线程的偏向锁。若测试成功，表示当前线程已获取锁；否则需判断是否设置，若没设置，使用CAS竞争锁；否则尝试使用CAS将对象头的偏向锁指向当前线程。

​	偏向锁的撤销与关闭

②轻量级锁：

​	轻量级锁加锁：自旋获取锁

​	轻量级锁解锁：使用原子的CAS操作将Displaced Mark Word替换回到对象头；成功表示无竞争；失败表示存在竞争，锁膨胀为重量级锁。

锁的优缺点对比：

| 锁           | 优点                                           | 缺点                                                      | 应用场景                               |
| ------------ | ---------------------------------------------- | --------------------------------------------------------- | -------------------------------------- |
| **偏向锁**   | **加锁和解锁不需要额外的消耗**                 | **若线程间存在竞争，则会消耗额外的资源进行锁撤销**        | **适用于只有一个线程访问同步块场景**   |
| **轻量级锁** | **线程间的竞争不会引起阻塞，提高程序响应速度** | **若得不到锁竞争的线程，会进行锁自旋获取锁，此时消耗cpu** | **追求相应时间，同步块执行速度非常块** |
| 重量级锁     | 线程间竞争不进行自旋，不消耗cpu                | 线程阻塞，响应时间缓慢                                    | 追求吞吐量，同步块执行速度较快         |

3.原子操作的实现原理

def: 不可被中断的一个或一系列操作

一些cpu术语：

​	缓存行cache line；比较并交换compare And Swap；cpu流水线cpu pipeline；内存顺序冲突memory order violation 

how: 实现原子操作

​	basis：①使用总线锁定确保原子性；eg: i++;

​		②使用缓存锁保证原子性；eg: 使用缓存锁定代替总线锁定进行优化，Lock前缀是修改内部的内存地址，并允许他的缓存一致性机制保证操作的原子性。即volatile内存语义。

注：不能使用缓存锁定的两种情况：操作的数据不能缓存在处理器内部；处理器不支持缓存锁定。

how: ①锁: **JVM偏爱于使用偏向锁，其余的都是循环CAS**

​	②循环CAS: 

​		可能存在的问题：ABA问题；循环时间长开销大；只能保证一个共享变量的原子操作 

​		**解决措施：使用版本号：java中提供AtomicStampedReference来解决ABA问题；支持处理器提供的pause指令；使用锁或者将多个共享变量合并为一个**

## Chapter 3: Java 内存模型JMM

重点：Java内存模型的基础；顺序一致性；三个同步原语；内存模型的设计

### 	一： 内存模型的基础

两个问题：线程之间如何通信与线程之间如何同步

#### 	通信机制：内存共享与消息传递

| 通信机制 | 通信                                     | 同步                                       |
| -------- | ---------------------------------------- | ------------------------------------------ |
| 内存共享 | 通过写-读内存中的公共状态，隐式通信      | 显式同步                                   |
| 消息传递 | 线程之间没有公共状态，线程间必须显式通信 | 消息的发送必须在接受之前；同步是隐式进行的 |

java内存模型的抽象结构：

​	**java堆内存包含：实例域、静态域和数组对象。即堆内存在线程之间共享。这三类是线程共享的对象，称之为共享变量。**

Java线程之间的共享有Java内存模型(JMM)控制，JMM决定一个线程对共享变量的写入何时对另一个线程可见。

**JMM定义了内存与线程之间的抽象关系：线程之间的共享变量存储在主内存中，每个线程都有一个私有的本地内存。**

从源代码到指令序列的重排序：

​	类型：编译器优化的重排序；指令集并行的重排序；内存系统的重排序。

前者属于编译器重排序；后两者属于处理器重排序。JMM属于语言级的内存模型。

happens-before: 

​	def: 若一个操作的执行结果需要对另一个操作可见，则这两个操作间必须存在happens-before关系

​	相关规则：

> ​		程序顺序规则：一个线程中的每个操作，happens-before 于该线程中的任意后续操作
>
> ​		监视器规则：解锁happens-before于加锁（针对同一锁）
>
> ​		volatile规则：一个volatile域的写happens-before于任意后续对这个volatile域的读
>
> ​		传递性：A happens-before B, B happens-before C, 那么A happens -before C 

注： 两个操作具有happens -before关系并不要求前一个操作必须先于后一个操作执行；

只是要求前一个操作对后一个操作可见，且前者按顺序排在后者之前。

### 二：重排序

数据依赖性：若数据a、b存在数据依赖性，单线程、多线程都不允许JMM进行重排序；

控制依赖性：若数据a、b存在控制依赖性，多线程不允许JMM进行重排序；

**单线程允许存在控制依赖性的操作进行重排序，这是由as-if-serial保证的。**func: 保证程序运行结果的同时，提高并行度

### 三：顺序一致性内存模型：

​	①一个线程中的所有操作必须按照程序顺序执行；

​	②所有线程都只能看到一个单一的操作执行顺序；每个操作都必须原子执行并立刻对所有线程可见。

| 是否是同步程序 | 顺序一致性模型(理论)                                         | JMM                                                          |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 同步程序       | 所有操作按程序顺序串行执行                                   | 临界区内的代码可以重排序                                     |
| 未同步程序     | 保证单线程内的操作按程序顺序执行；保证所有线程能看到一致的操作执行顺序 | 不保证单线程内的操作按程序顺序执行，as-if-serial；不保证所有线程能看到一致的操作执行顺序 |

### 四：volatile内存语义

volatile的特性：①可见性；②原子性

volatile写-读的内存语义：

> ​	写：当写一个volatile变量时，JMM会将该线程对应的本地内存中的共享变量值刷新到主内存。
>
> ​	读：当读一个volatile变量时，JMM会将该线程对应的本地内存置为无效，然后从主内存中读取该变量。
>

**summary: A线程写一个volatile变量，线程B读这个volatile变量，实质上是线程A通过主内存向线程B发送消息。**

内存语义的实现：

> ​	①当第二个操作是volatile写时，无论第一个操作是什么，都不能进行重排序；
>
> ​	②当第一个操作是volatile读时，无论第二个操作是什么，都不能进行重排序；
>
> ​	③当第一个操作是volatile写，第二个操作时volatile读时，不能重排序。

### 五：锁的内存语义

锁的功能：① 锁能让临界区互斥执行；② 锁的内存语义：与Volatile语义类似

​	线程释放锁时，JMM会将该线程对应的本地内存刷新到主内存中；

​	线程获取锁时，JMM会将该线程对应的本地内存置为无效，然后使被监视器保护的临界区代码必须从主内存中读取共享变量。

**summary: 释放锁的线程通过主内存向获取同一锁的线程发送消息**

锁内存语义的实现：

​	ReentrantLock: 

​		重入锁的CAS: 

​			加锁方法首先读volatile变量state ; 释放锁的最后写volatile变量state 

​			def: **compareAndSet(): 若当前状态值等于期望值，则以原子方式将当前状态修改为给定的更新值**；若不等，则不修改

| 是否是公平锁 | 获取锁                                            | 释放锁                      |
| ------------ | ------------------------------------------------- | --------------------------- |
| 公平锁       | 首先去读volatile变量                              | 最后一个写volatile变量state |
| 非公平锁     | 使用CAS策略更新volatile变量，具有volatile内存语义 | 最后一个写volatile变量state |

### 六：final域的内存语义

对final域的读和写更像是对普通的变量访问

在读一个对象的final域之前，一定要先读取包含这个final域的引用对象。

实现：

​		X86处理器没有final域中读/写的内存屏障；

​		只要正确构造(final域在构造函数中不能溢出)，则可以不使用同步(synchronized或lock)，就可以使所有线程可见在final域在构造函数中被初始化的结果。

### 七：happens-before

> ​	1) 若一个操作happens-before另一个操作，那么第一个操作的执行结果将对第二个操作可见，且第一个操作的执行顺序在第二个操作之前。——对程序员的承诺
>
> ​	2) 若两个操作间存在happens-before关系，并不要求java平台的具体实现要按照happens-before指定的顺序执行，可以在不改变执行结果的前提下，进行重排序。——对编译器和处理器的约束原则
>

​	

| 关系与语义     | 针对对象                                                     | 创造的幻境                                                 |
| -------------- | ------------------------------------------------------------ | ---------------------------------------------------------- |
| as-if-serial   | 保证单线程程序的执行结果不被改变                             | 单线程是按照程序顺序执行的                                 |
| happens-before | 保证正确同步的多线程程序的执行结果不被改变                   | 正确同步的多线程程序是按照happens-before指定的顺序来执行的 |
| 相同点         | 都是在不改变程序运行结果的前提下，尽可能地提高程序执行的并行度 |                                                            |

happens-before规则：

> ​	① 程序顺序规则：	
>
> ​	②监视器规则：
>
> ​	③volatile规则：
>
> ​	④传递性：
>
> ​	⑤start()规则：若线程A执行ThreadB.start()，那么A线程的ThreadB.start()操作happens-before与线程B中的任意操作。
>
> ​	⑥join()规则：若线程A执行操作ThreadB.join()并成功返回，那么线程B中的任意操作happens-before于线程A从ThreadB.join()操作成功返回。
>

### 八：双重锁定与延迟初始化

​	reason: 给对象引用指向内存空间；初始化对象；这两个操作之间可能存在重排序。

​	延迟初始化对象的方案：

​		① 基于volatile延迟的初始化方案；适用于对对象字段进行延迟初始化

​		② 基于类初始化的延迟方案：适用于静态字段(类字段)初始化

## Chapter 4: Java并发编程基础

​	pre: 线程是操作系统调度的最小单元，多个线程同时执行，这将显著提高程序性能。

​	review: 线程之间是如何通信和同步的；进程之间又是如何进行通信的呢？

| 系统调度的单位 | 通信                                               | 同步     |
| -------------- | -------------------------------------------------- | -------- |
| 线程           | 内存共享，隐式通信                                 | 显式同步 |
|                | 消息传递，显式通信                                 | 隐式同步 |
| 进程           | 管道流(pipeline)、FIFO、消息队列、共享内存、Socket |          |

### 一：线程简介

 	def: 现代操作系统调度的最小单元，也叫轻量级进程

​		eg: 启动一个Java程序，操作系统就会创建一个Java进程

​		一个Java程序的运行不仅仅是一个main函数的运行，而是main函数和多个其他线程的同时运行。

使用多线程的原因：更多的处理器核心；更快的响应时间；更好的编程模型；

线程优先级：setPriority(int rank);范围1-10默认5; 线程优先级不能作为程序正确性的依赖。

线程的状态：

> ​	New -> Runnable -> Terminated ; 
>
> ​	New -> Runnable -> Blocked -> Runnable -> Terminated 
>
> ​	New -> Runnable -> Waiting(Timed Waiting) -> Runnable -> Terminated 
>

Daemon线程：

> ​	def: 支持型线程
>
> ​	当一个Java虚拟机中不存在非Daemon线程时，Java虚拟机会退出；
>
> ​	注：Daemon属性需要在启动前设置，不能在启动线程后设置
>

**在构建Daemon线程时，不能依靠finally块中的内容来确保执行关闭或清理资源的逻辑。因finally块中的内容在Java虚拟机退出时，不一定会执行。**

### 二： 启动和终止线程

>  1.构建线程
>
> ​	Thread thread = new Thread(ThreadGroup parent,Runnable target,String name,long stackSize,AccessControContext acc);
>
> ​	2.启动线程：start(); 最好同时给线程取名
>
>  3. 线程中断：interrupt()方法表示当前线程与其他线程进行通信。
>
>     isInterrupted()方法与Thread.interrupt()方法都可用于判断线程是否中断，但若Thread终结或抛出异常后，isInterrupted()方法仍返回false;
>
>  4. 过期的suspend()、resume()、stop()方法：reason: 因资源释放问题
>
>  5. 正常终止线程：①使用interrupt(); ② 使用状态标识位

### 三：线程间通信

#### 1.volatile和synchronized关键字

​	程序在执行过程中，一个线程看到的变量并不一定是最新的。

​	synchronized可以修饰方法和同步块；确保在同一时刻，只能由一个线程处于方法或同步块中，保证了线程对访问变量的可见性和排他性。

![对象-监视器-等待队列](C:\Users\GYG\Desktop\ss_win_temp\对象-监视器-等待队列.jpg)

当访问Object的前驱(获得锁的线程)释放了锁，该释放操作将唤醒阻塞在同步队列中的线程，使其重新尝试对监视器的获取。

#### 2.等待/通知机制

Thread A: O.wait() ; 消费者

Thread B: O.notify() ;//notifyAll();	生产者

​	Attention: 

> ​		①使用wait()、notify()、notifyAll()方法时需先对调用对象加锁
>
> ​		② 调用wait() 方法后，线程的状态有running变为waiting,并将当前线程放置到对象的等待队列中；
>
> ​		③ notify() 或notifyAll() 方法调用后，等待线程并不会直接从wait()中返回，而是要等调用notify()或notifyAll()的线程释放锁后才返回
>
> ​		④ notify() 方法将等待队列中的一个等待线程从等待队列移向同步队列；而notifyAll()方法是将等待队列中的所有线程从等待队列移动到同步队列中，被移动的线程状态有Waiting变为Blocked;
>
> ​		⑤从wait()中返回的前提是该线程已获得调用对象的锁
>

**summary: 等待/唤醒机制依赖于同步机制，目的是为了从线程中返回时能够感知到通知线程对变量做出的改变。**

等待/唤醒机制的经典范式

| 等待方                                         | 唤醒方                       |
| ---------------------------------------------- | ---------------------------- |
| 获取锁对象                                     | 获取锁对象                   |
| 若条件不满足，执行wait()，被通知后仍要检查条件 | 改变条件                     |
| 条件满足则执行对应的逻辑                       | 通知(所有)等待在对象上的线程 |

#### 3.管道输入/输出流

> ​	func: 用于线程之间的数据传输，传输的媒介为内存
>
> 四种实现：PipedOutputStream、PipedInputStream、PipedWriter、PipedReader
>
> 输入流与输出流的连接：out.connect(in)
>

#### 4.Thead.join()的使用

​	func: 等待线程终止才返回

eg: thread.join() : 若线程A调用该方法，表示当前线程等待thread线程终止才从thread.join()返回。

​	thread.join(long millis);/join(long millis,int nannos); 线程thread在给定的超时时间里没有终止，将会从该超时方法中返回。

#### 5.ThreadLocal的使用

> ​	def: 线程变量，以ThreadLocal对象为键、任意对象为值的存储结构。
>
> ​	func: 可用于存储耗时统计上。get();set();
>

### 四：线程应用实例

#### 	1.等待超时模式

​		等待/唤醒机制 + 超时等待

#### 	2.数据库连接池

#### 	3.线程池技术（生产消费者模式）

> ​		func: 消除了频繁创建和消亡线程的系统资源开销；面对过量任务的提交能够平缓的劣化
>
> ​	本质： **使用了一个线程安全的工作队列连接工作者线程与客户端线程；客户端线程负责将任务放入工作队列中，然后返回；工作者线程不断从工作队列中取出工作并执行**，队列为空时，工作者线程等待在工作者队列上；当有客户端提交任务到任务队列中后，便会通知任意一个工作者线程，随着大量的任务被提交，更多的工作者线程会被唤醒。
>

#### 	4.利用线程池实现的Web服务器

## *Chapter 5: 锁

​	Targets: 

​		使用：JAVA并发包中与锁相关的API和组件

​		实现：通过分析源码剖析实现细节，正确使用并理解这些组件

### 一： Lock接口

| 同步方式     | 优点                                                         | 缺点                      |
| ------------ | ------------------------------------------------------------ | ------------------------- |
| Synchronized | 隐式地获取释放锁对象，简化同步的管理                         | 将锁的获取-释放固化       |
| Lock         | 显式地获取-释放锁，拥有锁获取-释放的操作性：中断获取锁、超时获取锁 | 需显式地对锁进行释放-获取 |

### 二：队列同步器

AbstractQueuedSynchronizer: 同步器

| 结构 | 面向对象   |                                                              |
| ---- | ---------- | ------------------------------------------------------------ |
| 锁   | 锁的使用者 | 定义了使用者与锁的接口                                       |
| 队列 | 锁的实现者 | 简化了锁的实现方式，屏蔽了同步状态管理/线程的排队/等待唤醒机制 |

> ​	1. 实现类：ReentrantLock , ReentrantReadWriteLock, CountDownLatch , Semaphore. 
>
> ​	2.重写的方法: tryAcquire(); tryRelease(); 
>
> ​	3.提供的模板方法：void acquire();void release(); void acquiredShared(); void releasedShared(); 
>
> ​	4.独占式同步状态的获取与释放：
>

​		process： **同步器将当前线程及等待状态等信息构成结点，将其加入队列，同时阻塞当前线程；当同步状态释放时，将首结点中的线程唤醒，使其再次尝试获取同步状态**

​		结点：保存获取同步状态失败的线程引用、等待状态、前驱、后继结点。

​		获取同步状态的自旋过程：满足的条件——》**前驱结点为头节点；能够获取同步状态**的判断条件；线程进入等待状态

​		tryAcquire(int arg): 

5.共享式同步状态的获取与释放：

​	读操作的同时进行；写操作要求对资源的独占式访问。

| 同步状态的获取与释放方式   | 区别                                        | eg:    |
| -------------------------- | ------------------------------------------- | ------ |
| 共享式同步状态的获取与释放 | 必须确保同步状态安全释放，通过循环和CAS保证 | 读操作 |
| 独占式同步状态的获取与释放 |                                             | 写操作 |

### 	三：重入锁

| 锁                    | 同                   | 异                                               |
| --------------------- | -------------------- | ------------------------------------------------ |
| Mutex(排他锁)         |                      | 不支持重进入的锁                                 |
| Synchronized          | 支持对资源的重复加锁 | 隐式支持重进入                                   |
| ReentrantLock(排他锁) | 支持对资源的重复加锁 | 显式支持重进入；同时支持获取锁的非公平性与公平性 |

支持重进入：

​	1)线程再次获取锁；2) 锁的最终释放

​	nonfairTryAcquire

| 重入锁的公平性 | 优                                                 | 缺                       |
| -------------- | -------------------------------------------------- | ------------------------ |
| 公平锁         | FIFO,保证了请求的绝对时间顺序                      | 线程切换，引起的开销过大 |
| 非公平锁       | 同一线程多次重复获取锁对象概率更大，增加程序吞吐量 | 可能造成某些线程“饥饿”   |

方法增加再次获取同步状态的逻辑：通过判断当前线程是否为获取锁的线程来决定操作是否成功，若是获取所得线程的再次操作，将同步状态值增加并返回true, 表示获取同步状态成功。

### 四：读写锁

在同一时刻只允许一个线程进行访问：Mutex / ReentrantLock

在同一时刻可以允许多个读线程同时访问：分离读锁和写锁; 能够提供比排他锁更好的并发性与吞吐量。

### 五：*LockSupport工具

1.methods: 

​	park();

​	unpark(Thread thread);

### 六： *Condition

​	依赖与Lock接口：

​	采用有界队列进行入队和出队，rear head结点的更新

|           | methods 及是否支持中断响应                                   | 队列个数     |
| --------- | ------------------------------------------------------------ | ------------ |
| Object    | wait(); notify();  不支持中断响应；不支持当前线程等待到某一刻 | 一个等待队列 |
| Condition | await(); signal();   支持中断响应；支持当前线程等待到某一刻  | 多个等待队列 |

## Chapter 6： Java并发容器和框架

### 	 一： ConcurrentHashMap的实现原理与实现

#### 		1.为什么使用ConcurrentHashMap ? 

| Map的实现类       | 安全性                                                       | 效率                                                         |
| ----------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ConcurrentHashMap | ReentrantLock(),Segment,HashEntry                            | 锁分段技术                                                   |
| HashMap           | 无，**多线程情况下进行put操作会导致死循环**(因Entry链表形成环形数据结构) | 高                                                           |
| Hashtable         | Synchronized                                                 | 低，当线程A访问hashtable中的同步方法时，其他线程处于轮询或阻塞状态(eg:线程A put时，线程B/C get()、put()都阻塞) |

#### 2.ConcurrentHashMap的结构：

​	包括Segment数组结构和HashEntry数组结构: 每个Segment元素都含有一个HashEntry[] , Segment与HashMap类似，是数组加链表的结构，每个HashEntry元素都是一个链表结构的数据。当对HashEntry数组中的数据进行修改时，需要先获取对应的Segment锁。

#### 3.ConcurrentHashMap的初始化：

​	初始化Segment数组：长度为2^n

​	初始化SegmentShift和SegmentMask: 偏移量与掩码

​	初始化每个Segment: initialCapacity 、loadFactor, 在构造方法中使用这两个参数来初始化数组中的每个Segment元素

#### 4.Segment的定位

​	分段锁的实现：

​		**在插入和获取元素时，必须先通过散列算法定位到Segment：即对元素的hashCode进行一次再散列**，目的是减少散列冲突，使元素能够均匀的分布在不同的Segment上，从而提高容器的存取效率。

#### 5.ConcurrentHashMap的操作

​	**get操作**： 先进行一次再散列，由散列值通过散列定位算法定位到Segment, 再通过散列算法定位到元素。

get()操作的高效之处在于：整个get()操作过程中不需要加锁，除非读取到的是空值，才会加锁重读。

​	**原因在于使用了volatile替代锁**

​	**put操作**：在操作共享变量时必须加锁；put()方法首先定位到Segment，然后再Segment里进行插入操作。

> step 1:判断是否需要扩容？——》插入前判断Segment中的HashEntry是否超出Threshold 
>
> step 2. 如何扩容? ——》创建一个容量是原来的两倍的数组，然后将原数组中的元素进行再散列后插入到新的数组里。
>

​	**size操作**：不是简单地将每个Segment中的count相加，这样可能得到的size是旧值。

​		**采用modCount变量**，在put、remove、clean方法里操作元素前对modCount进行+1，然后在计算size前后比较modCount是否发生改变，从而知道容器的大小是否发生变化。

### 二：ConcurrentLinkedQueue

实现线程安全的队列的两种方式：

> ​	1.使用阻塞算法：可以用一个锁或两个锁实现
>
> ​	2.使用非阻塞算法：循环CAS的方式实现

线程安全队列ConcurrentLinkedQueue是一个基于链接结点的无界线程安全队列。采用先进先出的规则对结点进行排序。

#### 1.入队列

> ​	分两步：
>
> ​		①将入队结点设置成当前队列尾结点的下一个结点；
>
> ​		②然后更新tail结点，**如果tail结点的next结点不为空，则入队结点设置为tail结点；**
>
> ​			**否则将入队结点设置为tail结点的next结点，所以tail结点不总是为尾结点，提高入队效率**

#### 2.定位尾结点

​	每次入队都需要通过tail结点来找尾结点，尾结点可能是tail结点，也可能是tail结点的next结点。

​	**设置入队结点为尾结点：p.casNext(null ,n):** 

​		func: 用于将入队结点设置为当前队列尾结点的next结点；

若p是null, 则表示入队结点是当前队列的尾结点；若不为null ,则表示其他线程更新了尾结点。

入队列的返回值始终是true，不能通过返回值来判断结点是否入队成功。

#### 3.出队列

​	head结点的更新：并不是每次出队列时都更新head结点

若head结点有元素时，直接弹出head结点中的元素，而不会更新head结点；若head结点中没有元素时，出队列才会更新head结点

### 三：Java中的阻塞队列

#### 阻塞队列：一个支持两个附加操作的队列

​	1) 支持阻塞的插入方法：队列满时阻塞插入数据的线程，直到队列不满

​	2) 支持阻塞的移除方法：队列为空时获取数据的线程会等待队列非空

支持两个附加操作的四种处理方式：

> ​	1) 抛出异常
>
> ​	2) 返回特殊值: true / null
>
> ​	3) 一直阻塞
>
> ​	4) 超时退出
>

#### Java中的阻塞队列

​	**ArrayBlockingQueue: 数组结构组成的有界阻塞队列**

​		可以是公平性，也可以是非公平性的。前者由ReentrantLock实现

​	**LinkedBlockingQueue:  链表结构组成的有界阻塞队列**

​		FIFO

​	**PriorityBlockingQueue: 优先级排序的无界阻塞队列**

​		默认采用自然顺序升序排列；也可以通过自定义类重写compareTo()方法实现；也可通过在构造方法中执行构造参数 Compartor实现自定义排序

​	**DelayQueue: 使用优先级队列实现的无界阻塞队列**

​		应用场景：

​			**①缓存系统：** 使用DelayQueue保存缓存元素的有效期，使用一个线程循环查询DelayQueue，一旦能够从DelayQueue中获取到元素，则表示缓存有效期到了。

​			**②定时任务调度：**使用DelayQueue保存当天将会执行的任务和执行时间，一旦从DelayQueue中获取到任务就开始执行。eg: ScheduleThreadPoolExcutor;

> ​	如何实现Delay接口：1. 对象创建时初始化对象；2. 实现getDelay()方法；3.实现compareTo()方法指定元素的顺序。
>
> ​	如何实现延时阻塞队列：当消费者从队列中获取元素时，若元素没有到达延时时间，就阻塞当前线程。

​	**SynchronousQueue: 不存储元素的阻塞队列**

​		每一个put操作必须等待一个take操作，否则不能访问元素。

​		**适用于传递场景。负责将生产者处理的数据直接传递给消费者**

​		支持公平访问和非公平访问队列(默认)。

​	**LinkedTransferQueue:  由链表结构组成的无界阻塞队列**

> ​		transform()方法：试图把存放当前元素的结点s作为tail结点；让cpu自旋等待消费者消费元素。
>
> ​		tryTransform()方法：试探生产者传入的数据能否直接传给消费者。与transform方法的区别是tryTransform方法无论传入的数据能否被消费者接受，都立即返回；而前者是一直等待直到消费者消费了才返回。
>

​	**LinkedBlockingDeque:  由链表结构组成的双向阻塞队列**

​		多了一个操作队列的入口，在多线程同时入队时，减少了一般的竞争。

#### 阻塞队列的实现

​	使用通知模式实现：LockSupport.park() 来阻塞队列；

​	park()方法返回的条件：满足其一即可

> ​		①与park对应的unpark()执行或已经执行
>
> ​		②线程被中断
>
> ​		③等待完time参数指定的毫秒数
>
> ​		④异常现象发生时，这个异常现象没有任何原因。

### 四：Fork/Join框架

#### 	Fork/Join框架：

> Fork: 把一个大任务切割为若干子任务并行执行
>
> Join: 合并子任务的执行结果

#### **工作窃取算法：** 

​	def: 线程与任务一一对应，干完活的线程去其他线程的队列中窃取一个任务来执行，此时有多个线程访问一个队列。故使用双端队列。

优点： 充分利用线程进行并行计算，减少了线程之间的竞争；

缺点：当队列中只有一个任务时，还是存在竞争；该算法会消耗更多的系统资源：eg: 创建更多线程与双端队列。

#### Fork/Join框架的设计

> ​	step 1: 分割任务	
>
> ​	step 2: 执行任务并合并结果
>
> 使用的类：
>
> ​	ForkJoinTask: 提供在任务中执行fork()和join()操作的机制；实现类：RecursiveAction、RecursiveTask 
>
> ​	ForkJoinPool: ForkJoinTask需要通过ForkJoinPool执行。
>
> ForkJoinTask: 需要实现compare()方法；
>

#### 异常处理：

​	检查方法：isCompletedAbnormally();

​	获取异常信息：getException();

Fork/Join框架的实现原理：

​	fork()方法实现原理：pushTask()方法异步执行任务；再调用ForkJoinPool中的signalWork()方法唤醒或创建一个工作线程来执行任务。

​	join() 方法实现原理：func: 阻塞当前线程并等待获取结果；doJoin()得到当前任务的状态：NORMAL;CANCELLED; SIGNAL;EXCEPTIONAL;

## Chapter 7:  Java中的13个原子操作类

### 一：原子更新基本类型类：

​	AtomicInteger /AtomicBoolean/AtomicLong 	char double float

​	methods: 

> ​		①int addAndGet(int delta);
>
> ​		②boolean compareAndSet(int except,int update);  
>
> ​		③void lazySet(int newValue); 
>
> ​		③int getAndSet(int newValue); 
>

### 二：原子更新数组：

> ​	AtomicArray: 将当前数组复制一份
>
> ​	AtomicLongArray: 
>
> ​	AtomicReferenceArray: 
>

### 三：原子更新引用：

> ​	AtomicReference: 
>
> ​	AtomicReferenceFieldUpdater: 原子更新引用类型中的字段
>
> ​	AtomicMarkableReference: 原子更新带标记位的引用类型
>

### 四：原子更新字段：

> ​	AtomicIntegerFieldUpdater: 
>
> ​	AtomicLongFieldUpdater: 
>
> ​	AtomicStampedReference: 原子更新带版本号的引用类型
>



## Chapter 8: Java中的并发工具类

​	并发包中常用的并发工具类：

| 并发工具类     | Function                           | note(difference)              | methods                                                |
| -------------- | ---------------------------------- | ----------------------------- | ------------------------------------------------------ |
| CountDownLatch | 一个或多个线程等待其他线程完成操作 | 计数器只能使用一次            | countDown()；其中await()方法会阻塞当前线程，直到N变成0 |
| CyclicBarries  | (可循环使用的)同步屏障             | 计数器可以使用reset()方法重置 | wait()阻塞当前线程，直到最后一个线程到达屏障           |
| Semaphore      | 控制流量的信号量                   | 信号量                        | acquire() ; release() ; tryAcquire() ;                 |
| Exchanger      | 线程间协作；线程间数据交换         | 交换数据                      | exchange() ;                                           |

### 一： 等待线程CountDownLatch: 

需求： 主线程等待所有线程完成sheet的解析操作，

> ​	①可以使用join()方法——》不停检查join线程是存活，若存活则当前线程一直等待；直到join线程中止，线程中的this.notifyAll()会被调用。
>
> ​	②也可以使用CountDownLatch——》调用CountDownLatch中的CountDown()方法时，N会减一，此时await()方法会阻塞当前线程。
>
> 注： <font color = red>CountDownLatch不能重新初始化或者修改其内部计数器的值</font>
>

### 二：同步屏障CyclicBarrier: 

​	func: 让一组线程到达一个屏障(同步点)时被阻塞，直到最后一个线程到达，屏障才会开门，

所有被屏障拦截的线程才会继续运行，

> ​	默认构造函数: CyclicBarrier(int parties)： 表示阻塞线程的数量。
>
> ​	高级构造函数：CyclicBarrier(int parties, Runnable barrierAction); 用于在线程到达屏障时，优先执行barrierAction
>

#### **每个线程调用await()方法告诉CyclicBarrier我已到达屏障，然后当前线程被阻塞。**

应用场景：用于多线程计算数据，最后合并计算结果的场景。

其他方法: CyclicBarrier的其他方法：

> ​	getNumberWaiting() : 获取CyclicBarrier阻塞线程的数量；
>
> ​	isBlocken(): 用于了解阻塞的线程是否被中断。
>

### 三：控制并发线程数的Semaphore

​	应用场景：流量控制：eg: 数据库连接

> ​	方法：acquire(): 该方法获取一个许可证；
>
> ​		release(): 归还许可证
>
> ​		tryAcquire() : 尝试获取许可证
>
> ​		int availablePermits(); 返回此信号量中当前可用的许可证数
>
> ​		int getQueueLength()  返回正在等待获取许可证的线程数
>
> ​		boolean hasQueueThreads() : 是否有线程正在等待获取许可证
>
> ​		Collection getQueueThreads(): 返回所有等待许可证的线程集合

### 四：线程间交换数据的Exchanger

> ​	应用场景： 
>
> ​		1.遗传算法
>
> ​		2. 校对工作
>
> 若两个线程有一个没有执行exchange()方法，则会一直等待；
>
> 可以使用exchange(V x, long timeout,TimeUnit unit);设置最大等待时长。
>

## Chapter 9 : Java中的线程池

***使用线程池的好处：**

> ​	1. 降低资源消耗
>
>  	2. 提高响应速度
>  	3. 提高线程的可管理性：同一分配、调优、监控

### 一： *线程池的实现原理

​	实现步骤：

> ​	1）判断核心线程是否都在执行任务；若不是，则线程池创建一个新的工作线程来执行任务；若是则转2）		
>
> ​	2）判断工作队列是否已满：若未满，则将提交的任务存储在这个队列里；若工作队列已满，转3）
>
> ​	3）判断当前线程是否都处于工作状态，若不是，则创建新的工作线程来执行任务；若是，则交给饱和策略来处理任务。

ThreadPoolExcuter执行excute方法的四种情况：

> ​	1）当前运行的线程数小于corePoolSize; 则创建一个新的工作线程来执行任务(此步骤需获取全局锁)
>
> ​	2）若运行的线程数量大于等于corePoolSIze, ， 则将任务加入BlockingQueue;
>
> ​	3) 若无法将任务加入BlockingQueue,(队列已满) 则将创建新的线程来处理任务（此步骤需要获取全局锁）
>
> ​	4）若创建新线程将使当前线程数超过maximumPoolSize , 任务将被拒绝。并调用RejectedExcutionHandler.rejectedExcution()方法。

线程池中的线程执行任务分为两种情况：

​	1）在excute()方法中创建一个线程时，会让该线程执行当前任务。

​	2）这个线程执行完excute()方法后，反复从BlockingQueue中获取任务来执行。

### 二：*线程池的使用

#### 1.线程池的创建：需要输入的几个参数：

> ​	corePoolSize; 线程池的基本大小
>
> ​	runnableTaskQueue ; 任务队列 
>
> ​		实现方式：ArrayBlockingQueue ; LinkedBlockingQueue ; SynchronizedQueue ; PriorityBlockingQueue ;
>
> ​	maximumPoolSize ; 线程池最大线程数
>

​	ThreadFactory; 用于设置创建线程的工厂： 设置更有意义的名字。

​	RejectedExcutionHandler;

> ​		AbortPolicy: 抛出异常
>
> ​		CallerRunsPolicy: 只用调用者所在线程执行当前任务
>
> ​		DiscardOldestPolicy: 丢弃队列里最近的一个任务，并执行当前任务
>
> ​		DiscardPolicy: 丢弃不处理

#### 2.向线程池提交任务

> execute(): 用于提交不需要返回值的任务
>
> submit(): 用于提交需要返回值的任务；通过该方法，线程池返回一个future类型的对象，可通过其get()方法来获取返回值，get()方法会阻塞当前线程直到任务结束。
>

#### 3.线程池的关闭

线程池的工作原理：遍历线程池中的工作线程，然后逐个调用线程的interrupt()方法来中的线程。

| 线程池关闭方法 | 原理                                   | 区别                                                     |
| -------------- | -------------------------------------- | -------------------------------------------------------- |
| shutdown()     | 将线程池的状态设置为SHUTDOWN状态       | 中断所有没有正在执行任务的线程，不暂停正在执行任务的线程 |
| shutdownNow()  | 尝试停止所有的正在执行或暂停任务的线程 | 任务不一定执行完                                         |

#### 4.合理地配置线程池

​		考虑的角度： 

> ​			任务的性质：cpu密集型、IO密集型、混合型
>
> ​			任务的优先级：高、中、低
>
> ​			任务的执行时间：长、中、短
>
> ​			任务的依赖性：是否依赖于其他系统资源：eg: 数据库连接
>

| 任务的性质 | 线程池的线程数                                               |
| ---------- | ------------------------------------------------------------ |
| CPU密集型  | Ncpu + 1                                                     |
| IO密集型   | 2*Ncpu                                                       |
| 混合型     | 若可以拆分，则将任务分为CPU密集型和IO密集型；若两个任务执行的时间相差不大，则没必要进行分解 |

获取当前设备的cpu数量：Runtime.getRuntime().availableProcessors()

**若一直有优先级高的任务提交到队列里，则优先级低的任务很可能永远不能执行。**

对于依赖于数据库连接的任务，设置的线程数越大，才能更好的利用cpu.

**建议使用有界队列**——》不至于因为任务过多一直插入队列，使得整个系统崩溃

#### 5.线程池的监控

​	使用以下属性：

> ​	taskCount: 任务数量
>
> ​	completedTaskCount: 线程池在执行过程中已经完成任务的数量
>
> ​	largestPoolSize: 线程池里曾经创建过最大线程数量
>
> ​	getPoolSize: 线程池的线程数量
>

**通过扩展线程池进行监控**：通过继承线程池来自定义线程池，然后重写线程池中的beforeExecute()、aferExecute()、terminated()方法来监控

## Chapter 10：Executor框架

**pre: Java中，使用线程来异步执行任务**

> ​	若每一个任务创建一个新线程来执行，这些线程的创建与销毁将消耗大量的计算资源。
>
> ​	Java线程既是工作单元，也是执行机制。java5之后, 就把工作单元与执行机制分离开来。
>
> ​	工作单元包括Runnable和Callable, 而执行机制包括Executor框架提供。
>

### 一： Executor框架简介

在Hotspot VM的线程模型中，Java线程被一一映射为本地操作系统线程。

> 上层：Java多线程程序将**应用分解为多个任务**，然后由用户级的调度器(**Executor框架)将这些任务映射到固定数量的线程；**
>
> 底层： **操作系统内核**将这些线程映射到硬件处理器上。
>

#### 1.Executor框架的结构：

​	任务：包括被执行任务所需实现的接口：Runnable接口或Callable接口

​	任务的执行：包括任务执行机制的核心接口Executor，及继承于Executor的接口ExecutorService。Executor框架有两个关键类实现了ExecutorService接口：**ThreadPoolExecutor、ScheduledThreadPoolExecutor.**

​	异步结果的处理: 包括接口Futrue和实现Future的类FutureTask

​	Executor分配任务并执行的流程：

> ​		1）首先创建实现了Runnable或Callable接口的任务对象。
>
> ​		2）将Runnable对象直接提交给ExecutorService执行executor(); 或者将Runnable对象或Callable对象提交给ExecutorService执行submit().
>
> ​		3）若执行的是Executor.submit(...), 将返回一个实现Future接口的类FutureTask对象。且FutureTask也实现了Runnable接口，故可以创建Future类，交给ExecutorService执行. 
>
> ​	 	4)最后，主线程可以执行FutureTask的get()方法来等待任务执行完成；或执行FutureTask.cancel()来取消此任务的执行。

#### 2.Executor框架的成员

1) ThreadPoolExecutor: 通常通过Executors工厂类来创建

> ​	FixedThreadPool : 固定线程数的FixedThreadPool ； 适用于负载比较重的服务器
>
> ​	SingleThreadExecutor : 创建使用单个线程SingleThreadExecutor ;适用于需保证顺序地执行各个任务；且不会有多个线程是活动的应用场景。
>
> ​	CachedThreadPool : 创建一个会根据需要创建新线程的CachedThreadPool ;适用于执行很多短期异步任务的小程序，或者是负载比较轻的服务器。
>

2) ScheduledThreadPoolExecutor: 通常通过Executors工厂类来创建

> ​	ScheduledThreadPoolExecutor: 包含一个线程的ScheduledThreadPoolExecutor. 适用于**需要多个后台线程执行周期任务**；同时为了满足管理的需求需要**限制后台线程数量**的应用场景。
>
> ​	SingleThreadScheduledExecutor: 包含一个线程的ScheduledThreadPoolExecutor. 适用于**单个后台线程执行周期任务**，同时需要**保证顺序执行各个任务的**应用场景。
>

3) Future接口

> ​	Future接口和实现该接口的FutureTask类来标识异步计算的结果。在未来的JDK实现中，返回的可能不一定是FutrueTask。
>

4) Runnable接口和Callable接口

> ​	Runnable接口和Callable接口的实现类，都可以提交给ThreadPoolExecutor或ScheduledThreadPoolExecutor执行。
>
> ​	注：除了可以自己创建实现Callable的接口的对象外，还可以使用工厂类Executor来把一个Runnable包装为一个Callable。此外还有把一个Runnable和一个待返回的结果包装秤一个Callable的API: static <T> Callable<T> callable(Runnable task, T result);
>

### 二：ThreadPoolExecutor详解

​	ThreadPoolExecutor类的四个组件：

> ​		corePool: 核心线程池的大小
>
> ​		maximumPool: 作答线程池的大小
>
> ​		BolckingQueue: 用于保存任务的工作队列
>
> ​		RejectedExecutionHandler: 当ThreadPoolExecutor已经关闭或ThreadPoolExecutor已经饱和，executor()方法将要调用的Handler.
>

​	**FixedThreadPool: 可重用固定线程数的线程池**

> ​		①当前运行的线程数< corePoolSize  —— 》 创建新线程
>
> ​		②线程池完成预热后(当前运行的线程数 = corePoolSize) , 将任务加入LinkedBlockingQueue
>
> ​		③线程执行完①中的任务后，会循环反复从LinkedBlockingQueue中获取任务来执行。
>
> ​		FixedThreadPool采用无界队列LinkedBlockingQueue作为工作队列，故maximumPoolSize <= corePoolSize; 运行中的FixedThreadPool不会拒绝任务。
>

​	**SingleThreadExecutor: 使用单个线程的Executor.**

> ​		步骤同FixedThreadPool , 且corePoolSize = maximumPoolSize = 1
>

​	**CachedThreadPool: 会根据需要创建新线程的线程池**

> ​		corePoolSize = 0; maximumPoolSize = Integer.MAX_VALUE,即maximumPoolSize是无界的；keepAliveTime设置为60L;意味着空闲线程超过60s将会被终止。
>
> ​		采用的是没有容量的SynchronousQueue作为线程池的工作线程；当主线程提交任务的速度高于maximumThreadPool中线程处理任务的速度时，CachedThreaPool会不断创建线程。
>
> ​	由于synchronousQueue是无容量的阻塞队列，故每个offer操作必定等待另一个线程对应poll操作。
>

### 三： ScheduledThreadPoolExecutor的详解

​	ScheduledThreadPoolExecutor继承自ThreadPoolExecutor

​		func: 用于在给定延迟之后运行任务，或定期执行任务。

​	对比Timer对应的是单个后台线程；ScheduledThreadPoolExecutor可以在构造函数中指定多个对应的后台线程数。

​	1.ScheduledThreadPoolExecutor的运行机制：

> ​		1） 当ScheduledThreadPoolExecutor调用scheduleAtFixedRate()或scheduledWithDelay方法时, 会向DelayQueue中添加一个实现类RunnableScheduledFuture接口的ScheduledFutureTask.
>
> ​		2) 线程池中的线程从DelayQueue中获取ScheduledFutureTask任务来执行。
>

​	2.ScheduledThreadPoolExecutor的实现

> ScheduledFutureTask的三个成员变量：time,sequenceNumber , period 
>

![image-20210518221414616](C:\Users\GYG\AppData\Roaming\Typora\typora-user-images\image-20210518221414616.png)

DelayQueue.获取任务的步骤：

> ​	获取锁；获取周期任务；释放锁
>

添加任务的步骤： 

> ​	获取锁；添加任务；释放锁。
>

### 四： FutureTask详解

#### 1.FutureTask简介：

三种状态：未启动；已启动；已完成

![image-20210518221845498](C:\Users\GYG\AppData\Roaming\Typora\typora-user-images\image-20210518221845498.png)

#### 2.FutureTask的使用

> ​	①FutureTask交给Executor使用
>
> ​	②可以通过ExecutorService.submit()方法返回FutureTask,然后执行get() 方法或cancel()方法
>
> ​	③ 单独使用FutureTask. func: 一个线程需要等待另一个线程把某个任务执行完后，才能继续执行。
>

#### 3.FutureTask的实现

> ​	使用AQS队列：基于AQS实现的同步器有：ReentrantLock, Semephore ,ReentrantReadWriteLock, CountDownLatch 和FutureTask.
>
> ​	至少一个acqurie操作；get();
>
> ​	至少一个release操作: 包括run()和cancel()方法
