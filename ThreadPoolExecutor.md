# 线程

1、线程是cpu调度的最小操作单位，线程模型分为KLT模型和ULT模型，jvm使用的是KLT模型

2、线程的状态：new,runnable,blocked,terminated

### 线程池

#### 1、线程池解决的两大核心问题：

在执行大量异步运算的时候，线程池会优化系统性能，减少线程的反复创建所带来的系统开销

提供一种限制和管理资源的方法

#### 2、线程的7大核心参数

1、corePoolSize：线程池中的核心线程数，当提交一个任务时，线程池创建一个新线程执行任务，知道当前线程等于corePoolSize，如果当前线程数为corePoolSize，继续提交的任务保存到阻塞队列中，等待被执行，如果执行了线程池的prestartAllCoreThread()方法，线程会提前创建并启动所有的核心线程。

2、maxmunPoolSize：线程中允许的最大线程，如果当前阻塞队列满了，且继续提交任务，则创建新的线程执行任务，前提是当前线程小于maxmunPoolSize

3、keepAliveTime：线程池维护线程所允许的空闲时间，当线程池中的线程数量大corePoolSize的时候，如果这时候没有新的任务提交，核心线程外的线程不会立即销毁，而是会等待，知道等待的时间超过keepAliveTime

4、unit：keepAliveTime的单位

5、

workQueue：用来保存等待执行的任务的阻塞队列，且任务必须实现Runnable接口，jdk中提供了如下阻塞队列

ArrayBlickingQueue：基于数组结构的有界阻塞队列，按FIFO排序任务；

LinkBlockingQuene:基于链表结构的阻塞队列，按FIFO排序任务，吞吐量通常要高于ArrayBlockingQuene

SynchronousQuene一个不存储元素的阻塞队列，每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常要高于LinkedBlockingQuene

priorityBlockingQuene：具有优先级的无界队列

#### 3、线程池的生命周期状态

new

runnable

wating

blocking

timed_wating

terminated

#### 4、线程池的重要性：ctl

1、ctl是对线程池的运行状态和线程池中有效线程的数量进行控制的一个字段，它包含两部分：线程池的运行状态(runState)和线程池有效线程的数量(workerCount)这里可以看到，使用了Integer类型保存，高3位保存runState，低29位保存workerCount的上限值，大约是5亿

2、runState主要提供线程池的生命周期的控制，主要包括：

Runnable

​	（1）状态说明：线程池处于Running状态时，能够接收新任务，以及对已添加的任务进行处理。

​	(2)状态切换：线程池的初始化状态是Running。换句话说，线程池一旦被创建，就处于running状态，并且线程池中的任务为0

Shutdown

​	(1)状态说明：线程池处于shutdown状态，不接收新任务，但能处理已添加的任务

​	(2)状态切换：调用线程池的shutdown()接口是，线程池由running->shutdown

Stop

​	(1)状态说明：线程池处于stop状态时，不接收先任务，不处理已添加的任务，并且会中断正在处理的任务

​	(2)状态切换：调用线程池的shutdownNow()接口是，线程池由(running or shutdwn)->stop

Tidying

​	(1)状态说明：当所有的任务已终止，ctl记录的任务数量为0，线程池会变为Tidying状态，当线程池变为Tidying状态时，会执行钩子函数terminated()。terminated在ThreadPoolExecutor类中是空的若用户想在线程池变为Tidying时，进行相应的处理，可以通过重载terminated()函数来实现。

​	(2)状态切换：当线程池在shutdown状态下，阻塞队列为空并且线程池中执行的任务也为空时，就会由shutdown->tidying。当线程池在stop状态下，线程中执行的任务为空时，就会由stop->tidying

#### 5、线程池的行为

execute(Runnable command):执行Runnable类型任务

submit(task)可用来提交callable或runnable任务，并返回代表此任务的future对象

shutdown()：在完成提交的任务后封闭办事，不在接管新任务

shutdownNow()：停止所有正在履行的任务并封闭办事

isTerminated()：测试是否所有的任务都履行完毕了

isShutdown()：测试是否该ExecutorService已被关闭

#### 6、常用的线程池的具体实现

ThreadPoolExecutor默认线程池

ScheduledThreadPoolExecutor定时线程池

#### 7、线程监控API

public long getTaskCount() //线程池已执行与未执行的任务总数

public long getCompletedTaskCount() //已完成的任务数

public int getPoolSize() //线程池当前的线程数

public int getActiveCount() //线程池中正在执行任务的线程数



