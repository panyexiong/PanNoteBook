# CountDownLatch

java.util.concurrent包下

```java
public class CountDownLatchDemo {
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(6);

        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName());
                countDownLatch.countDown();
            }).start();
        }
        countDownLatch.await();
        System.out.println(Thread.currentThread().getName());
    }
}
//out
Thread-0
Thread-4
Thread-3
Thread-2
Thread-1
Thread-5
main
```

- 让一些线程阻塞直到另外一些线程完成一系列操作后才被唤醒
- CountDownLatch主要有两个方法，当一个或多个线程调用await方法时，调用线程会被卒社。其他线程调用CountDown方法的时候会将计数器减一（调用countDown方法的线程不会阻塞），当计数器的值变为零的时候，因调用await方法被阻塞的线程会被唤醒，继续执行。

# CyclicBarrier

```JAVA
import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

/**
 * @author panyexiong
 * @version 1.0
 * @date 2019/9/2 9:15
 */
public class CyclicBarrierDemo {
    public static void main(String[] args) {

        CyclicBarrier cyclicBarrier = new CyclicBarrier(7,()->{
            System.out.println("=====召唤神龙");
        });

        for (int i = 0; i < 7; i++) {
            final int tempInt =i+1;
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+"\t 收集到第 "+tempInt+"颗龙珠。");

                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();


        }
    }
}
//out
Thread-0	 收集到第 1颗龙珠。
Thread-5	 收集到第 6颗龙珠。
Thread-4	 收集到第 5颗龙珠。
Thread-3	 收集到第 4颗龙珠。
Thread-2	 收集到第 3颗龙珠。
Thread-1	 收集到第 2颗龙珠。
Thread-6	 收集到第 7颗龙珠。
=====召唤神龙
```

# Semaphore

```java
package com.pan.codeExercises.thread;

import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;

/**
 * @author panyexiong
 * @version 1.0
 * @date 2019/9/2 9:21
 */
public class SemaphoreDemo {
    public static void main(String[] args) {
        //模拟三个车位
        Semaphore semaphore = new Semaphore(3);
        //模拟6部车
        for (int i = 0; i < 6; i++) {
            new Thread(() -> {
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName()+"\t 抢到车位");
                    TimeUnit.SECONDS.sleep(3);
                    System.out.println(Thread.currentThread().getName()+"\t 停车三秒后离开车位");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    semaphore.release();
                }
            }).start();
        }
    }
}
//out
Thread-0	 抢到车位
Thread-1	 抢到车位
Thread-2	 抢到车位
Thread-0	 停车三秒后离开车位
Thread-2	 停车三秒后离开车位
Thread-1	 停车三秒后离开车位
Thread-3	 抢到车位
Thread-4	 抢到车位
Thread-5	 抢到车位
Thread-3	 停车三秒后离开车位
Thread-4	 停车三秒后离开车位
Thread-5	 停车三秒后离开车位
```

