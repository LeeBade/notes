# Java对多线程的支持

线程调度完全由操作系统决定，Java程序不能决定线程什么时候执行，以及执行多长时间
- Java程序启动的时将启动一个JVM进程，JVM在该进程中启动主线程执行main()方法
- 创建Thread实例后，通过调用其start()方法启动新线程
  - 注意直接调用Thread实例的run()方法是无效的

```JAVA

Thread t = new Thread();
//因为该线程未装入代码，因此什么都不会做就会结束
t.start();

```

方法一：从Thread派生一个自定义类，然后覆写run()方法

```JAVA
public class Main {
    public static void main(String[] args) {
        Thread t = new MyThread();
        t.start(); // 启动新线程
    }
}

class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("start new thread!");
    }
}

```

方法二：创建Thread实例时，传入一个Runnable实例：
- 可以使用lambda简写匿名类

```JAVA

public class Main {
    public static void main(String[] args) {
        Thread t = new Thread(() -> {
            System.out.println("start new thread!");
        });
        t.start(); // 启动新线程
    }
}

```



可以对线程设定优先级
- 不能通过优先级保证某个线程一定先执行

```java
Thread.setPriority(int n) // 1~10, 默认值5

```


## 线程的状态

线程的状态
- New：已创建，未执行

- Runnable：运行中，正在执行run()方法

- Blocked：运行中，被阻塞

- Waiting：运行中，等待

- Timed Waiting：运行中，执行sleep()方法正在计时等待

- Terminated：已终止，run()方法执行完毕



在一个线程中获取对其它线程的引用t，执行t.join()方法：等待另一个线程结束才运行
- join(long)：等待指定时间后不再继续等待

在一个线程中获取对其它线程的引用t，执行t.interrupt()方法，通知目标线程立即中断
-  t.interrupt()仅发出中断请求
-  目标线程是否响应中断请求取决于该线程是否具有相应代码

```JAVA
class MyThread extends Thread {
    public void run() {
        int n = 0;
        while (! isInterrupted()) {
          //若此处不判断，则代码会一直执行下去
            n ++;
            System.out.println(n);
        }
    }
}

```

- 若目标线程接收到中断请求，且处于等待中，则该等待方法立刻结束等待并抛出InterruptedException
- 在目标线程中断之前，目标线程应向其等待的线程也发送interrupt请求
- 在执行sleep方法时，如果被其它线程中断，将立即抛出InterruptedException

```JAVA
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new MyThread();
        t.start();
        Thread.sleep(1000);
        t.interrupt(); // 中断t线程
        t.join(); // 等待t线程结束
        System.out.println("end");
    }
}

class MyThread extends Thread {
    public void run() {
        Thread hello = new HelloThread();
        hello.start(); // 启动hello线程
        try {
            hello.join(); // 等待hello线程结束
        } catch (InterruptedException e) {
            System.out.println("interrupted!");
        }
        hello.interrupt();
    }
}

class HelloThread extends Thread {
    public void run() {
        int n = 0;
        while (!isInterrupted()) {
            n++;
            System.out.println(n + " hello!");
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                break;
            }
        }
    }
}

```

通过线程间共享变量设置标志位中断线程
- 线程间共享变量需要使用volatile修饰
  - volatile修饰变量：每次访问变量时，总是获取主内存的最新值；每次修改变量后，立刻回写到主内存。
  - volatile关键字解决的是可见性问题：当一个线程修改了某个共享变量的值，其他线程能够立刻看到修改后的值
  - volatile关键字禁止编译器重排序指令
  - volatile关键字不保证原子性：仍需使用synchronized保证原子性
```JAVA

public class Main {
    public static void main(String[] args)  throws InterruptedException {
        HelloThread t = new HelloThread();
        t.start();
        Thread.sleep(100);
        t.running = false; // 标志位置为false
    }
}

class HelloThread extends Thread {
    public volatile boolean running = true;
    public void run() {
        int n = 0;
        while (running) {
            n ++;
            System.out.println(n + " hello!");
        }
        System.out.println("end!");
    }
}



```

## 守护线程

什么时候需要守护线程Daemon Thread：为其他线程提供服务，该线程无限执行且不会阻止JVM退出
- 非守护线程未结束前，JVM都不会退出




```JAVA

Thread t = new MyThread();
t.setDaemon(true);
t.start();


```


## 同步

多个线程读写同一个变量时，需要同步：一个线程读写该变量时，其它线程等待
- 获取目标

```JAVA
synchronized(Counter.lock) { // 获取锁
    ...
} // 释放锁
```

不需要同步的操作
- 基本类型赋值
  - 若不是x64平台的JVM，则long和double除外
- 引用类型赋值
- 不可变对象


若方法被synchronized修饰，则被锁住的对象是调用该方法的实例
- 下面的写法等价

```JAVA

public void add(int n) {
    synchronized(this) { // 锁住this
        count += n;
    } // 解锁
}
public synchronized void add(int n) { // 锁住this
    count += n;
} // 解锁

```

- 若静态方法被synchronized修饰，则被锁住的对象是调用该方法的类的Class实例
- 若读方法仅返回一个原子变量，则不需要同步；若返回两个以上则需要同步


## 死锁

Java的线程锁是可重入的锁：即允许同一个线程重复获取同一个锁，jvm需要记录每个线程获取某个锁多少次，直到持有该锁数量为0时才释放该锁

死锁：两个线程各自持有不同的锁，然后各自试图获取对方手里的锁


## wait、notify

多线程协调运行的原则
- 当条件不满足时，线程进入等待状态
- 当条件满足时，线程被唤醒，继续执行任务

wait、notify必须在synchronized同步块中使用，其次wait、notify必须通过被同步的对象调用
- wait()方法执行时一定至少拥有一个锁，此时该锁将会被释放
- 当该线程被唤醒时，wait()方法将尝试获取一次锁，获取到后返回
- wait()方法必须在当前获取的锁对象上调用
```JAVA
public synchronized String getTask() {
    while (queue.isEmpty()) {
        this.wait();
    }
    return queue.remove();
}
```

- 必须使用notify()方法唤醒指定锁下某个在wait中的线程
  - 只要执行notify方法，就代表当前线程持有一个锁进行写操作，此时其它读线程必定处于wait状态

```JAVA
public synchronized void addTask(String s) {
    this.queue.add(s);
    this.notify(); // 唤醒在this锁等待的线程
}
```


- notifyAll()方法将唤醒所有处于指定锁下处于wait状态的线程
  - 所有线程被唤醒进入竟态条件，被唤醒的线程将依次获取锁


```JAVA

// 该代码是错误代码，当wait方法返回时即使获取到锁，可能也有其它线程先获取到锁并消费了产品
public synchronized String getTask() throws InterruptedException {
    if (queue.isEmpty()) {
        this.wait();
    }
    return queue.remove();
}

```

## java.util.concurrent

### ReentrantLock 

ReentrantLock替代synchronized

```JAVA

public class Counter {
    private final Lock lock = new ReentrantLock();
    private int count;

    public void add(int n) {
        lock.lock();
        try {
            count += n;
        } finally {
            lock.unlock();
        }
    }
}

```

ReentrantLock可以尝试获取锁
- 在指定时间内未获取到锁就立即返回false，避免死锁

```JAVA

if (lock.tryLock(1, TimeUnit.SECONDS)) {
    try {
        ...
    } finally {
        lock.unlock();
    }
}

```
### Condition

ReentrantLock和Condition对象配合实现wait和notify的功能
1. Condition对象绑定ReentrantLock对象
2. 使用ReentrantLock对象获取锁，并通过Condition对象执行await()、signal()、signalAll()方法
3. 在等待指定时间后，若没有其他线程通过signal()或signalAll()唤醒当前线程，则await()可以自己醒来


```JAVA

class TaskQueue {
    private final Lock lock = new ReentrantLock();
    private final Condition condition = lock.newCondition();
    private Queue<String> queue = new LinkedList<>();

    public void addTask(String s) {
        lock.lock();
        try {
            queue.add(s);
            condition.signalAll();
        } finally {
            lock.unlock();
        }
    }

    public String getTask() {
        lock.lock();
        try {
            while (queue.isEmpty()) {
                condition.await();
            }
            return queue.remove();
        } finally {
            lock.unlock();
        }
    }
}

```

### ReadWriteLock

什么时候使用：大量读少量写
- 只允许一个线程写入；
- 允许多个线程在没有写入时同时读取；
- 缺点：悲观锁，读操作优先，写线程必须等待所有读线程释放锁
```JAVA
public class Counter {
    private final ReadWriteLock rwlock = new ReentrantReadWriteLock();
    private final Lock rlock = rwlock.readLock();
    private final Lock wlock = rwlock.writeLock();
    private int[] counts = new int[10];

    public void inc(int index) {
        wlock.lock(); // 加写锁
        try {
            counts[index] += 1;
        } finally {
            wlock.unlock(); // 释放写锁
        }
    }

    public int[] get() {
        rlock.lock(); // 加读锁
        try {
            return Arrays.copyOf(counts, counts.length);
        } finally {
            rlock.unlock(); // 释放读锁
        }
    }
}

```

### StampedLock
StampedLock：允许读锁未释放时获取写锁并写入
- 使用StampedLock会导致读数据不一致，所以需要额外的代码来判断读的过程中是否有写入
- 乐观锁：乐观地估计读的过程中大概率不会有写入
- 悲观锁：读的过程中拒绝写入，写入必须等待

- StampedLock和ReadWriteLock的加写锁操作完全一样
- StampedLock在获取写锁的前后判断快照的版本号，如果版本号不一致需要重复读取值，由于写入操作少，所以再次读取值时版本号大概率能一致
- 缺点：StampedLock是不可重入锁，不能在一个线程中反复获取同一个锁
```JAVA
public class Point {
    private final StampedLock stampedLock = new StampedLock();

    private double x;
    private double y;

    public void move(double deltaX, double deltaY) {
        long stamp = stampedLock.writeLock(); // 获取写锁
        try {
            x += deltaX;
            y += deltaY;
        } finally {
            stampedLock.unlockWrite(stamp); // 释放写锁
        }
    }

    public double distanceFromOrigin() {
        long stamp = stampedLock.tryOptimisticRead(); // 获得一个乐观读锁
        double currentX = x;
        double currentY = y;

        if (!stampedLock.validate(stamp)) { // 检查乐观读锁后是否有其他写锁发生
            stamp = stampedLock.readLock(); // 获取一个悲观读锁
            try {
                currentX = x;
                currentY = y;
            } finally {
                stampedLock.unlockRead(stamp); // 释放悲观读锁
            }
        }
        return Math.sqrt(currentX * currentX + currentY * currentY);
    }
}

```

### Semaphore

Semaphore：同一时刻最多有N个线程能访问

```JAVA
public class AccessLimitControl {
    // 任意时刻仅允许最多3个线程获取许可:
    final Semaphore semaphore = new Semaphore(3);

    public String access() throws Exception {
        // 如果超过了许可数量,其他线程将在此等待:
        semaphore.acquire();
        try {
            // TODO:
            return UUID.randomUUID().toString();
        } finally {
            semaphore.release();
        }
    }
}

```

### 线程安全的并发集合类


java.util.concurrent包提供的线程安全的并发集合类：下面是您提供的内容转换为 Markdown 格式的表格：

| Interface | Non-thread-safe                | Thread-safe                         |
|-----------|--------------------------------|-------------------------------------|
| List      | ArrayList                      | CopyOnWriteArrayList                |
| Map       | HashMap                        | ConcurrentHashMap                   |
| Set       | HashSet / TreeSet              | CopyOnWriteArraySet                 |
| Queue     | ArrayDeque / LinkedList        | ArrayBlockingQueue / LinkedBlockingQueue |
| Deque     | ArrayDeque / LinkedList        | LinkedBlockingDeque                 |


### Atomic

java.util.concurrent.atomic包提供一组原子操作的封装类

例如AtomicInteger
- 增加值并返回新值：int addAndGet(int delta)

- 加1后返回新值：int incrementAndGet()

- 获取当前值：int get()

- 用CAS方式设置：int compareAndSet(int expect, int update)

CAS：Compare and Set，无锁实现线程安全

```JAVA
public int incrementAndGet(AtomicInteger var) {
    int prev, next;
    do {
        prev = var.get();
        next = prev + 1;
    } while ( ! var.compareAndSet(prev, next));
    return next;
}
```

什么时候使用atomic包
- 多线程环境下的计数器，累加器
- 优点：使用无锁的线程安全实现的封装好的原子操作
- 利用AtomicLong编写多线程安全的全局唯一ID生成器：

```JAVA
class IdGenerator {
    AtomicLong var = new AtomicLong(0);

    public long getNextId() {
        return var.incrementAndGet();
    }
}

```

## 线程池

什么时候使用线程池：接收大量小任务并进行分发处理


ExecutorService接口+Executors类

- ExecutorService接口代表线程池，Executors类封装三种常用的实现类
- ExecutorService接口提供submit方法，接受Runnable接口
- 提供三个线程池关闭方法
  - shutdown()等待所有任务执行结束后关闭线程池
  - shutdownNow()立刻停止正在执行的任务，并关闭线程池
  - awaitTermination()等待指定的时间后再关闭线程池
- 

```JAVA
// 创建固定大小的线程池:
ExecutorService executor = Executors.newFixedThreadPool(3);
// 提交任务:
executor.submit(task1);
executor.submit(task2);
executor.submit(task3);
executor.submit(task4);
executor.submit(task5);

```

Executors类封装创建线程池的方法
- FixedThreadPool：线程数固定的线程池

- CachedThreadPool：线程数根据任务动态调整的线程池

- SingleThreadExecutor：仅单线程执行的线程池

使用ScheduledThreadPool替代java.util.Timer类
- 原因；java.util.Timer每次都会启动一个线程执行一次Timer
- ScheduledThreadPool仅启用一个线程定时重复执行一个任务
- ScheduledThreadPool保证
  - 任务不会在同一时间点执行两次
  - 一旦任务抛出异常，后续任务将不再进行
- 提交一次性任务，在指定延迟后只执行一次：

```JAVA
// 1秒后执行一次性任务:
ses.schedule(new Task("one-time"), 1, TimeUnit.SECONDS);

```
- 每3秒执行一次任务

```JAVA
// 2秒后开始执行定时任务，每3秒执行:
ses.scheduleAtFixedRate(new Task("fixed-rate"), 2, 3, TimeUnit.SECONDS);

```
- 每两个任务间隔3秒
```JAVA
// 2秒后开始执行定时任务，以3秒为间隔执行:
ses.scheduleWithFixedDelay(new Task("fixed-delay"), 2, 3, TimeUnit.SECONDS);

```
注意FixedRate和FixedDelay的区别
- FixedRate：任务总是以固定时间间隔触发，不管任务执行多长时间
  - 若任务执行时间可能超过固定时间间隔，则每次任务执行时间超过固定时间间隔时，都会将固定时间间隔进行一次自增

│░░░░   │░░░░░░ │░░░    │░░░░░  │░░░  
├───────┼───────┼───────┼───────┼────>
│<─────>│<─────>│<─────>│<─────>│

- FixedDelay：上一次任务执行完毕后，等待固定的时间间隔，再执行下一次任务：

│░░░│       │░░░░░│       │░░│       │░
└───┼───────┼─────┼───────┼──┼───────┼──>
    │<─────>│     │<─────>│  │<─────>│

## Callable接口& Future

Runnable接口中的run方法没有返回值，使用Callable接口配合`Future<V>`泛型接口实现不同返回并获取值


```JAVA
class Task implements Callable<String> {
    public String call() throws Exception {
        return longTimeCalculation(); 
    }
}
```

ExecutorService.submit()方法返回`Future<T>`
- 对`Future<T>`调用get方法，返回T类型的对象，该方法可能会阻塞当前线程

```JAVA
ExecutorService executor = Executors.newFixedThreadPool(4); 
// 定义任务:
Callable<String> task = new Task();
// 提交任务并获得Future:
Future<String> future = executor.submit(task);
// 从Future获取异步执行返回的结果:
String result = future.get(); // 可能阻塞
```


`Future<V>`接口定义的方法

- get()：获取结果（可能会等待）

- get(long timeout, TimeUnit unit)：获取结果，但只等待指定的时间；

- cancel(boolean mayInterruptIfRunning)：取消当前任务；

- isDone()：判断任务是否已完成

## CompletableFuture

Future阻塞方法get()或轮询isDone()两种方法都不好，因此使用CompletableFuture异步调用，类似js的promise
- `CompletableFuture<V>`接口需要传入一个Supplier接口的对象
  - Supplier接口要求lambda形式：`T get();`
- 正常时CompletableFuture调用Consumer对象：

```JAVA
public interface Consumer<T> {
    void accept(T t);
}
```
- 异常时CompletableFuture调用Function对象：

```JAVA
public interface Function<T, R> {
    R apply(T t);
}
```

```JAVA
public class Main {
    public static void main(String[] args) throws Exception {
        // 创建异步执行任务:
        CompletableFuture<Double> cf = CompletableFuture.supplyAsync(Main::fetchPrice);
        // 如果执行成功:
        cf.thenAccept((result) -> {
            System.out.println("price: " + result);
        });
        // 如果执行异常:
        cf.exceptionally((e) -> {
            e.printStackTrace();
            return null;
        });
        // 主线程不要立刻结束，否则CompletableFuture默认使用的线程池会立刻关闭:
        Thread.sleep(200);
    }

    static Double fetchPrice() {
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
        }
        if (Math.random() < 0.3) {
            throw new RuntimeException("fetch price failed!");
        }
        return 5 + Math.random() * 20;
    }
}

```

使用CompletableFuture链式执行串行任务

```JAVA
public class Main {
    public static void main(String[] args) throws Exception {
        // 第一个任务:
        CompletableFuture<String> cfQuery = CompletableFuture.supplyAsync(() -> {
            return queryCode("中国石油");
        });
        // cfQuery成功后继续执行下一个任务:
        CompletableFuture<Double> cfFetch = cfQuery.thenApplyAsync((code) -> {
            return fetchPrice(code);
        });
        // cfFetch成功后打印结果:
        cfFetch.thenAccept((result) -> {
            System.out.println("price: " + result);
        });
        // 主线程不要立刻结束，否则CompletableFuture默认使用的线程池会立刻关闭:
        Thread.sleep(2000);
    }

    static String queryCode(String name) {
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
        }
        return "601857";
    }

    static Double fetchPrice(String code) {
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
        }
        return 5 + Math.random() * 20;
    }
}

```

CompletableFuture并行执行

## Fork/Join线程池

适用场景：把一个大任务拆成多个小任务并行执行
- 判断一个任务是否足够小，如果是，直接计算，否则，就分拆成几个小任务分别计算

## ThreadLocal

在同一个线程中跨方法共享对象，这些对象组成线程的上下文Context

在静态字段初始化ThreadLocal实例
```java

static ThreadLocal<User> threadLocalUser = new ThreadLocal<>();

```

使用
- 如果未回收ThreadLocal实例，该线程放回线程池并再次被使用时，其它代码将会看见此次状态
```JAVA
void processUser(user) {
    try {
        threadLocalUser.set(user);
        step1();
        step2();
    } finally {
        threadLocalUser.remove();
    }
}

void step1() {
    User u = threadLocalUser.get();
    log();
    printUser();
}

void log() {
    User u = threadLocalUser.get();
    println(u.name);
}

void step2() {
    User u = threadLocalUser.get();
    checkUser(u.id);
}
```

通过AutoCloseable接口配合try (resource) {...}结构，让编译器自动关闭资源
```JAVA
public class UserContext implements AutoCloseable {

  static final ThreadLocal<String> ctx = new ThreadLocal<>();

  public UserContext(String user) {
      ctx.set(user);
  }

  public static String currentUser() {
      return ctx.get();
  }

  @Override
  public void close() {
      ctx.remove();
  }
}

```
- 使用
```JAVA
try (var ctx = new UserContext("Bob")) {
    // 可任意调用UserContext.currentUser():
    String currentUser = UserContext.currentUser();
} // 在此自动调用UserContext.close()方法释放ThreadLocal关联对象
```

## 虚拟线程

虚拟线程需要java 21支持，由一个普通线程管理数千个虚拟线程，并交替执行IO密集型任务
- IO密集型任务：CPU执行代码消耗的时间非常少，线程的大部分时间都在等待IO



# 并发编程基础知识


## 线程安全

实现线程安全的核心：使用线程和锁对状态访问操作进行管理，包括但不限于对共享状态Shared和可变状态Mutable的访问
- 共享：多个线程可同时访问
- 可变：变量状态在其生命周期内可发生变化
- 变量状态：任何会更改对象外部可见行为的数据；不仅包括对象的实例域和静态域，且可能包括外界依赖对象的数据
- 良好的面向对象设计所带来的封装性和




