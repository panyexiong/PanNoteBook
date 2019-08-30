## 公平锁和非公平锁

公平锁：是指多个线程按照申请锁的顺序来获取锁，类似排队打饭，先来后到

非公平锁：是指多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的线程比先申请的线程优先获取锁，在高并发的情况下，有可能会造成优先级反转或者饥饿的现象。ReentrantLock和Synchronized都是非公平锁

非公平锁的有点在于吞吐量比公平锁大。

```java
Lock lock = new ReentrantLock();
//默认非公平锁
/**源码
     * Creates an instance of {@code ReentrantLock}.
     * This is equivalent to using {@code ReentrantLock(false)}.
     */
public ReentrantLock() {
    sync = new NonfairSync();
}
```

## 可重入锁（递归锁）

解释：指的是同一线程外层函数获得锁之后，内层递归函数仍能获取该锁的代码，在同一线程在外层方法获取锁的时候，在进入内层方法会自动获取锁。也就是说，线程可以进入任何一个它已经拥有的锁所同步着的代码块。

`ReentrantLock`,`synchronized`就是典型的可重入锁。

可重入锁的最大作用：避免死锁。

```java
/**
 * 可重入锁（递归锁）
 * synchronized,ReentrantLock
 *
 * @author panyexiong
 * @version 1.0
 * @date 2019/8/28 19:38
 */
public class Demo01 {

    public static void main(String[] args) {
        Phone phone = new Phone();
        new Thread(() -> {
            try {
                phone.sendSMS();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, "Thread01").start();

        new Thread(() -> {
            try {
                phone.sendSMS();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, "Thread02").start();

        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("========================");

        Thread thread03 = new Thread(phone);
        Thread thread04 = new Thread(phone);

        thread03.start();
        thread04.start();
    }
}

class Phone implements Runnable {
    public synchronized void sendSMS() throws Exception {
        System.out.println(Thread.currentThread().getName() + "\t invoked sendSMS()");
        sendEmail();
    }

    public synchronized void sendEmail() throws Exception {
        System.out.println(Thread.currentThread().getName() + "\t ==========invoked sendEmail()");
    }

    Lock lock = new ReentrantLock();

    @Override
    public void run() {
        get();
    }

    public void get() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t invoked get()");
            set();
        } finally {
            lock.unlock();
        }
    }

    public void set() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t invoked set()");
        } finally {
            lock.unlock();
        }
    }
}
//输出
Thread01	 invoked sendSMS()
Thread01	 ==========invoked sendEmail()
Thread02	 invoked sendSMS()
Thread02	 ==========invoked sendEmail()
========================
Thread-0	 invoked get()
Thread-0	 invoked set()
Thread-1	 invoked get()
Thread-1	 invoked set()

```



## 独占锁（写）/共享锁（读）/互斥锁

ReentrantLock和Synchronized都是独占锁。

共享锁：指该锁可以被多个线程共有。

ReentrantReadWriteLock，其读锁是共享锁，写锁是独占锁。

读锁的共享锁可保证并发读是非常高效的，读写，写读，写写的过程是互斥的。

写操作：原子+独占

```java
package com.pan.codeExercises.thread.lock;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.locks.ReentrantReadWriteLock;

/**
 * @author panyexiong
 * @version 1.0
 * @date 2019/8/28 21:24
 */
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache myCache = new MyCache();

        for (int i = 0; i < 5; i++) {
            final int tempInt = i;
            new Thread(() -> {
                myCache.put(tempInt + "", tempInt + "");
            }, String.valueOf(i)).start();
        }

        for (int i = 0; i < 5; i++) {
            final int tempInt = i;
            new Thread(() -> {
                myCache.get(tempInt + "", tempInt + "");
            }, String.valueOf(i)).start();
        }
    }

}

class MyCache {
    private volatile Map<String, Object> map = new HashMap<>();
    private ReentrantReadWriteLock reentrantReadWriteLock = new ReentrantReadWriteLock();

    public void put(String key, Object value) {

        reentrantReadWriteLock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t 正在写入：" + key);
            map.put(key, value);
            System.out.println(Thread.currentThread().getName() + "\t 写入完成");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            reentrantReadWriteLock.writeLock().unlock();
        }
    }

    public void get(String key, Object value) {

        reentrantReadWriteLock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t 正在读取：");
            Object result = map.get(key);
            System.out.println(Thread.currentThread().getName() + "\t 读取完成：" + result);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            reentrantReadWriteLock.readLock().unlock();
        }
    }

    public void clearMap() {
        map.clear();
    }
}
//out
2	 正在写入：2
2	 写入完成
1	 正在写入：1
1	 写入完成
0	 正在写入：0
0	 写入完成
3	 正在写入：3
3	 写入完成
4	 正在写入：4
4	 写入完成
2	 正在读取：
1	 正在读取：
2	 读取完成：2
3	 正在读取：
3	 读取完成：3
1	 读取完成：1
4	 正在读取：
4	 读取完成：4
0	 正在读取：
0	 读取完成：0
```



## 自旋锁SpinLockDemo

解释：是指尝试获取锁的线程不会立即阻塞，而是采用循环方式去尝试获取锁，这样的好处是减少线程上下文切换的消耗，缺点是会消耗CPU

### 请手写一个自旋锁

通过CAS操作完成自旋锁，A线程先进来调用myLock方法自己持有锁5秒，B随后进来后发现当前线程持有锁，不是null，所以只能通过自旋等待，直到A释放锁，B随后抢到。

```java
package com.pan.codeExercises.thread.lock;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicReference;

/**
 * @author panyexiong
 * @version 1.0
 * @date 2019/8/28 20:56
 */
public class SpinLockDemo {

    AtomicReference<Thread> atomicReference = new AtomicReference<>();

    public static void main(String[] args) {
        SpinLockDemo spinLockDemo = new SpinLockDemo();
        new Thread(() -> {
            spinLockDemo.myLock();
            try {
                TimeUnit.SECONDS.sleep(5);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            spinLockDemo.myUnLock();
        }, "Thread01").start();

        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        new Thread(() -> {
            spinLockDemo.myLock();
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            spinLockDemo.myUnLock();
        }, "Thread02").start();

    }

    public void myLock() {
        Thread thread = Thread.currentThread();
        System.out.println(Thread.currentThread().getName() + "\t come in");
        while (!atomicReference.compareAndSet(null, thread)) {

        }
    }

    public void myUnLock() {
        Thread thread = Thread.currentThread();
        atomicReference.compareAndSet(thread, null);
        System.out.println(Thread.currentThread().getName() + "\t invoked myUnLock()");
    }
}
//out
Thread01	 come in
Thread02	 come in
Thread01	 invoked myUnLock()
Thread02	 invoked myUnLock()
```

