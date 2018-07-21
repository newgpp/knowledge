# Thread

> https://en.wikipedia.org/wiki/Thread_(computing)#Threads_vs._processes

- a thread of execution is the smallest sequence of programmed instructions that can be managed independently by a scheduler, which is typically a part of the operating system.
- At the kernel level, a process contains one or more kernel threads, which share the process's resources, such as memory, file handles, sockets, device handles, windows, and a process control block. **a process is a unit of resources, while a thread is a unit of scheduling and execution.**
- 线程是程序执行的一个路径，每一个线程都有自己的局部变量表、程序计数器（指向正在执行的指令指针）以及各自的生命周期，现代操作系统中一般不止一个线程在运行，当启动了一个Java虚拟机（JVM）时，从操作系统开始就会创建一个新的进程（JVM进程），JVM进程中将会派生或者创建很多线程。

## 一、Thread 生命周期

### 1. 线程状态

- NEW
- RUNNABLE
- RUNNING
- BLOCKED
- TERMINATED

### 2. 线程状态流转

#### 2.1 线程的NEW状态

new创建一个对象，未调用start方法，该线程根本不存在，此时标识为NEW状态。

- NEW状态通过start方法进入RUNNABLE状态。

#### 2.2 线程的RUNNABLE状态

线程的运行与否和进程一样都要听令于CPU的调度，那么我们把这个中间状态称为可执行状态（RUNNABLE）。
RUNNABLE状态的线程只存在以下两种状态变化:

1. 意外终止
2. 进入RUNNING状态

#### 2.3 线程的RUNNING状态

一旦CPU通过轮询或者其他方式从任务可执行队列中选中了线程，那么才能真正的运行。
RUNNING状态的线程可以发生如下的几种状态变化:

1. 直接进入TERMINATED状态，比如调用jdk不推荐的stop方法。
2. 进入BLOCKED状态，比如调动了sleep，或者wait方法而加入了waitSet中。
3. 进行某个阻塞的IO操作，比如因网络数据的读写进入了BLOCKED状态。
4. 获取某个锁资源从而加入到该锁的阻塞队列中而进入了BLOCKED状态。
5. 由于CPU的调度器轮询使该线程放弃执行，进入RUNNABLE状态。
6. 线程主动调用yield方法，放弃CPU执行权，进入RUNNABLE状态。

#### 2.4 线程的BLOCKED状态

BLOCKED状态的线程可以发生如下的几种状态变化:

1. 直接进入TERMINATED状态，比如调用jdk不推荐的stop方法或者意外死亡（JVM Crash）。
2. 线程阻塞的操作结束，比如读取了想要的数据字节进入到RUNNABLE状态。
3. Wait中的线程被其他线程notify/notifyall唤醒，进入RUNNABLE状态。
4. 线程获取到了某个锁资源，进入RUNNABLE状态。
5. 线程在阻塞过程中被打断，比如其他线程调用了interrupt方法，进入RUNNABLE状态。

#### 2.5 线程的TERMINATED状态

TERMINATED是一个线程的最终状态，在该状态中线程将不会切换到其他任何状态，线程进入TERMINATED状态，意味着该线程的整个生命周期都结束了，下列这些情况将会使线程进入TERMINATED状态。

1. 线程正常运行结束，结束生命周期。
2. 线程运行出错意外结束。
3. JVM Crash，导致所有的线程结束。

## 二、Thread API

### 1. 线程sleep

sleep会使当前线程进入指定毫秒数的休眠，进入block状态，暂停执行。不会放弃monitor锁的所有权。

    Thread.sleep(long millis)
    TimeUnit.SECONDS.sleep();

### 2. 线程yield

yield提醒调度器我愿意放弃当前的CPU资源，会使 **当前线程从RUNNING状态切换到RUNNABLE状态。**

    Thread.yield();

### 3. 获取当前线程

用于返回 **当前执行线程的引用。**

    Thread.currentThread()

### 4. 线程interrupt

如下方法的调用会使当前线程进入阻塞状态。

- Object的wait方法。
- Thread的sleep方法。
- thread的join方法
- InterruptibleChannel的io操作。
- Selector的wakeup方法。

调用阻塞线程的interrupt方法，则会打断这种阻塞，因此这种方法有时会被称为可中断方法，打断一个线程
并不等于该线程的生命周期结束，仅仅是打断了当前线程的阻塞状态。
线程阻塞被打断，都会跑出一个称为InterruptedException的异常，这个异常就像一个signal一样通知当前线程被打断了。

     public void interrupt()

    * <p> If this thread is blocked in an invocation of the {@link
     * Object#wait() wait()}, {@link Object#wait(long) wait(long)}, or {@link
     * Object#wait(long, int) wait(long, int)} methods of the {@link Object}
     * class, or of the {@link #join()}, {@link #join(long)}, {@link
     * #join(long, int)}, {@link #sleep(long)}, or {@link #sleep(long, int)},
     * methods of this class, then its interrupt status will be cleared and it
     * will receive an {@link InterruptedException}.
     *
     * <p> If this thread is blocked in an I/O operation upon an {@link
     * java.nio.channels.InterruptibleChannel InterruptibleChannel}
     * then the channel will be closed, the thread's interrupt
     * status will be set, and the thread will receive a {@link
     * java.nio.channels.ClosedByInterruptException}.
     *
     * <p> If this thread is blocked in a {@link java.nio.channels.Selector}
     * then the thread's interrupt status will be set and it will return
     * immediately from the selection operation, possibly with a non-zero
     * value, just as if the selector's {@link
     * java.nio.channels.Selector#wakeup wakeup} method were invoked.

    public static boolean interrupted() {
        return currentThread().isInterrupted(true);
    }

    public boolean isInterrupted() {
        return isInterrupted(false);
    }

    private native boolean isInterrupted(boolean ClearInterrupted);

### 5. 线程join

调用A线程的join方法会使当前线程B进入block状态，直到线程A结束生命周期。

## 三、线程安全与数据同步

### 1. synchronized

synchronized 关键字提供了一种互斥机制，也就是说在同一时刻，只能有一个线程访问同步资源，很多资料、书籍将synchronized（mutex）称为锁，其实这种说法是不严谨的，准确地讲应该是某线程获取了与mutex关联的monitor锁。

### 2. 死锁

#### 2.1 交叉锁导致程序出现死锁

    private final Object MUTEX_ READ = new Object();
    private final Object MUTEX_ WRITE = new Object();
    public void read() {
        synchronized (MUTEX_ READ) {
            synchronized (MUTEX_ WRITE) {
                //...
            }
        }
    }
    public void write() {
        synchronized (MUTEX_ WRITE) {
            synchronized (MUTEX_ READ) {
                //...
            }
        }
    }

#### 2.2 数据库锁

无论是数据库表级别的锁，还是行级别的锁，比如某个线程执行for update语句退出了事务，其他线程访问该数据库时都将陷入死锁。

## 四、捕获程序的异常执行

### 1. UncaughtExceptionHandler

## 五、线程池

### 5.1 线程池标识

#### 5.1.1 runState

- **RUNNING**: Accept new tasks and process queued tasks
- **SHUTDOWN**: Don't accept new tasks, but process queued tasks
- **STOP**: Don't accept new tasks, don't process queued tasks, and interrupt in-progress tasks
- **TIDYING**: All tasks have terminated, workerCount is zero, the thread transitioning to state TIDYING will run the terminated() hook method

#### 5.1.2 lifycycle control of runState

- **RUNNING -> SHUTDOWN**: On invocation of shutdown(), perhaps implicitly in finalize()
- **(RUNNING or SHUTDOWN)**: -> STOP: On invocation of shutdownNow()
- **SHUTDOWN -> TIDYING**: When both queue and pool are empty
- **STOP -> TIDYING**: When pool is empty
- **TIDYING -> TERMINATED**: When the terminated() hook method has completed

#### 5.1.3 workerCount, the effective number of threads

    private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));
    private static final int COUNT_BITS = Integer.SIZE - 3;
    private static final int CAPACITY   = (1 << COUNT_BITS) - 1;

    // runState is stored in the high-order bits
    private static final int RUNNING    = -1 << COUNT_BITS;
    private static final int SHUTDOWN   =  0 << COUNT_BITS;
    private static final int STOP       =  1 << COUNT_BITS;
    private static final int TIDYING    =  2 << COUNT_BITS;
    private static final int TERMINATED =  3 << COUNT_BITS;

    // Packing and unpacking ctl
    private static int runStateOf(int c)     { return c & ~CAPACITY; }
    private static int workerCountOf(int c)  { return c & CAPACITY; }
    private static int ctlOf(int rs, int wc) { return rs | wc; }

- ctl前3个bit位用于标识runState
- crl后29个bit位用于标识workerCount

### 5.2 线程池执行

    public void execute(Runnable command) {
        if (command == null)
            throw new NullPointerException();
        /*
         * Proceed in 3 steps:
         *
         * 1. If fewer than corePoolSize threads are running, try to
         * start a new thread with the given command as its first
         * task.  The call to addWorker atomically checks runState and
         * workerCount, and so prevents false alarms that would add
         * threads when it shouldn't, by returning false.
         *
         * 2. If a task can be successfully queued, then we still need
         * to double-check whether we should have added a thread
         * (because existing ones died since last checking) or that
         * the pool shut down since entry into this method. So we
         * recheck state and if necessary roll back the enqueuing if
         * stopped, or start a new thread if there are none.
         *
         * 3. If we cannot queue task, then we try to add a new
         * thread.  If it fails, we know we are shut down or saturated
         * and so reject the task.
         */
        int c = ctl.get();
        if (workerCountOf(c) < corePoolSize) {
            if (addWorker(command, true))
                return;
            c = ctl.get();
        }
        if (isRunning(c) && workQueue.offer(command)) {
            int recheck = ctl.get();
            if (! isRunning(recheck) && remove(command))
                reject(command);
            else if (workerCountOf(recheck) == 0)
                addWorker(null, false);
        }
        else if (!addWorker(command, false))
            reject(command);
    }

### 5.3 RejectedExecutionHandler

#### 5.3.1 AbortPolicy

    /**
     * A handler for rejected tasks that throws a
     * {@code RejectedExecutionException}.
     */
    public static class AbortPolicy implements RejectedExecutionHandler {
        /**
         * Creates an {@code AbortPolicy}.
         */
        public AbortPolicy() { }

        /**
         * Always throws RejectedExecutionException.
         *
         * @param r the runnable task requested to be executed
         * @param e the executor attempting to execute this task
         * @throws RejectedExecutionException always
         */
        public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
            throw new RejectedExecutionException("Task " + r.toString() +
                                                 " rejected from " +
                                                 e.toString());
        }
    }

#### 5.3.2 DiscardPolicy

    /**
     * A handler for rejected tasks that silently discards the
     * rejected task.
     */
    public static class DiscardPolicy implements RejectedExecutionHandler {
        /**
         * Creates a {@code DiscardPolicy}.
         */
        public DiscardPolicy() { }

        /**
         * Does nothing, which has the effect of discarding task r.
         *
         * @param r the runnable task requested to be executed
         * @param e the executor attempting to execute this task
         */
        public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
        }
    }

#### 5.3.3 DiscardOldestPolicy

    /**
     * A handler for rejected tasks that discards the oldest unhandled
     * request and then retries {@code execute}, unless the executor
     * is shut down, in which case the task is discarded.
     */
    public static class DiscardOldestPolicy implements RejectedExecutionHandler {
        /**
         * Creates a {@code DiscardOldestPolicy} for the given executor.
         */
        public DiscardOldestPolicy() { }

        /**
         * Obtains and ignores the next task that the executor
         * would otherwise execute, if one is immediately available,
         * and then retries execution of task r, unless the executor
         * is shut down, in which case task r is instead discarded.
         *
         * @param r the runnable task requested to be executed
         * @param e the executor attempting to execute this task
         */
        public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
            if (!e.isShutdown()) {
                e.getQueue().poll();
                e.execute(r);
            }
        }
    }

#### 5.3.4 CallerRunsPolicy

    /**
     * A handler for rejected tasks that runs the rejected task
     * directly in the calling thread of the {@code execute} method,
     * unless the executor has been shut down, in which case the task
     * is discarded.
     */
    public static class CallerRunsPolicy implements RejectedExecutionHandler {
        /**
         * Creates a {@code CallerRunsPolicy}.
         */
        public CallerRunsPolicy() { }

        /**
         * Executes task r in the caller's thread, unless the executor
         * has been shut down, in which case the task is discarded.
         *
         * @param r the runnable task requested to be executed
         * @param e the executor attempting to execute this task
         */
        public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
            if (!e.isShutdown()) {
                r.run();
            }
        }
    }