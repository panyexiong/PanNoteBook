# 阻塞队列

- 当阻塞队列为空时，从队列中获取元素的操作将会被阻塞
- 当阻塞队列时满时，从队列里添加元素的操作将会被阻塞

## BlockingQueue接口

实现Queue接口，Queue接口是Collection的子接口。

### 具体实现类

1. **ArrayBlockingQueue：由数组结构组成的有界阻塞队列**
2. **LinkedBlockingQueue：由链表结构组成的有界阻塞队列（但大小默认值为Integer.MAX_VALUE）**
3. PriorityBlockingQueue:支持优先级排序的无界阻塞队列
4. DelayQueue：使用优先级队列实现的延迟无界阻塞队列
5. **SynchronousQueue：不存储元素的阻塞队列，也即单个元素的队列**
6. LinkedTransferQueue：由链表结构组成的无界阻塞队列
7. LinkedBlockingDeque：由链表结构组成的双向阻塞队列

## BlockingQueue的核心方法

1. 抛出异常

   1. ```java
      public class Demo01 {
          public static void main(String[] args) {
              BlockingQueue<String> blockingQueue =new ArrayBlockingQueue<>(3);
              System.out.println(blockingQueue.add("a"));
              System.out.println(blockingQueue.add("b"));
              System.out.println(blockingQueue.add("c"));
              System.out.println(blockingQueue.add("d"));
          }
      }
      //out
      true
      true
      true
      Exception in thread "main" java.lang.IllegalStateException: Queue full
      	at java.util.AbstractQueue.add(AbstractQueue.java:98)
      	at java.util.concurrent.ArrayBlockingQueue.add(ArrayBlockingQueue.java:312)
      	at com.pan.codeExercises.thread.BlockingQueue.Demo01.main(Demo01.java:18)
      ```

   2. ```java
      public class Demo01 {
          public static void main(String[] args) {
              BlockingQueue<String> blockingQueue =new ArrayBlockingQueue<>(3);
              System.out.println(blockingQueue.add("a"));
              System.out.println(blockingQueue.add("b"));
              System.out.println(blockingQueue.add("c"));
      
              System.out.println(blockingQueue.remove());
              System.out.println(blockingQueue.remove());
              System.out.println(blockingQueue.remove());
              System.out.println(blockingQueue.remove());
          }
      }
      //out
      true
      true
      true
      a
      b
      c
      Exception in thread "main" java.util.NoSuchElementException
      	at java.util.AbstractQueue.remove(AbstractQueue.java:117)
      	at com.pan.codeExercises.thread.BlockingQueue.Demo01.main(Demo01.java:22)
      ```

   3. ```java
      System.out.println(blockingQueue.element());
      //检查返回头部元素
      ```

2. 特殊值

   1. ```java
      public class Demo02 {
          public static void main(String[] args) {
              BlockingQueue<String> blockingQueue =new ArrayBlockingQueue<>(3);
              System.out.println(blockingQueue.offer("a"));
              System.out.println(blockingQueue.offer("b"));
              System.out.println(blockingQueue.offer("c"));
              System.out.println(blockingQueue.offer("d"));
          }
      }
      //out
      true
      true
      true
      false
      ```

   2. ```java
      public class Demo02 {
          public static void main(String[] args) {
              BlockingQueue<String> blockingQueue =new ArrayBlockingQueue<>(3);
              System.out.println(blockingQueue.offer("a"));
              System.out.println(blockingQueue.offer("b"));
              System.out.println(blockingQueue.offer("c"));
              System.out.println(blockingQueue.offer("d"));
      
              System.out.println(blockingQueue.peek());
      
              System.out.println(blockingQueue.poll());
              System.out.println(blockingQueue.poll());
              System.out.println(blockingQueue.poll());
              System.out.println(blockingQueue.poll());
      
          }
      }
      //out
      true
      true
      true
      false
      a
      a
      b
      c
      null
      ```

3. 阻塞

   1. ```java
      public class Demo03 {
          public static void main(String[] args) throws InterruptedException {
              BlockingQueue blockingQueue =new ArrayBlockingQueue(3);
              blockingQueue.put("a");
              blockingQueue.put("a");
              blockingQueue.put("x");
              System.out.println("=================");
      
              blockingQueue.take();
              blockingQueue.take();
              blockingQueue.take();
              blockingQueue.take();
      
          }
      }
      ```

   2. 

4. 超时

   1. ```java
      public class Demo03 {
          public static void main(String[] args) throws InterruptedException {
              BlockingQueue blockingQueue =new ArrayBlockingQueue(3);
              System.out.println(blockingQueue.offer("a", 2L, TimeUnit.SECONDS));
              System.out.println(blockingQueue.offer("a", 2L, TimeUnit.SECONDS));
              System.out.println(blockingQueue.offer("a", 2L, TimeUnit.SECONDS));
              System.out.println(blockingQueue.offer("a", 2L, TimeUnit.SECONDS));
      
          }
      }
      //out
      true
      true
      true
      false
      ```

## SynchronousQueue

```java
public class SynchronousQueueDemo {
    public static void main(String[] args) {
        BlockingQueue<String> blockingQueue = new SynchronousQueue<>();

        new Thread(() -> {
            try {
                System.out.println(Thread.currentThread().getName() + "\t put 1");
                blockingQueue.put("1");

                System.out.println(Thread.currentThread().getName() + "\t put 2");
                blockingQueue.put("2");

                System.out.println(Thread.currentThread().getName() + "\t put 3");
                blockingQueue.put("3");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();

        new Thread(() -> {
            try {
                TimeUnit.SECONDS.sleep(5);
                System.out.println(Thread.currentThread().getName() + "\t" + blockingQueue.take());

                TimeUnit.SECONDS.sleep(5);
                System.out.println(Thread.currentThread().getName() + "\t" + blockingQueue.take());

                TimeUnit.SECONDS.sleep(5);
                System.out.println(Thread.currentThread().getName() + "\t" + blockingQueue.take());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
    }
}
//out
Thread-0	 put 1
Thread-1	1
Thread-0	 put 2
Thread-1	2
Thread-0	 put 3
Thread-1	3
```

## 用在哪里

### 生产者消费者模式

#### 传统版本ProdConsumer_TraditionDemo

```java
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * 初始值为0的一个变量，两个线程对其交替操作，一个加1一个减1
 * 1、线程操作资源类
 * 2、判断干活通知
 * 3、防止虚假唤醒机制
 *
 * @author panyexiong
 * @version 1.0
 * @date 2019/9/2 10:37
 */
public class ProdConsumer_TraditionDemo {
    public static void main(String[] args) {
        ShareData shareData =new ShareData();
        new Thread(()->{
            for (int i = 0; i < 5; i++) {
                try {
                    shareData.increment();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();

        new Thread(()->{
            for (int i = 0; i < 5; i++) {
                try {
                    shareData.decrement();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }
}

/**
 * 资源类
 */
class ShareData {
    private int number = 0;
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();

    public void increment() throws Exception {
        lock.lock();
        try {
            while(number != 0){
                condition.await();
            }
            number++;
            System.out.println(Thread.currentThread().getName()+"\t"+number);
            condition.signalAll();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void decrement() throws Exception {
        lock.lock();
        try {
            while(number == 0){
                condition.await();
            }
            number--;
            System.out.println(Thread.currentThread().getName()+"\t"+number);
            condition.signalAll();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

}

//out
Thread-0	1
Thread-1	0
Thread-0	1
Thread-1	0
Thread-0	1
Thread-1	0
Thread-0	1
Thread-1	0
Thread-0	1
Thread-1	0
```

#### 阻塞队列版本ProdConsumer_BlockQueueDemo

```java
package com.pan.codeExercises.thread.BlockingQueue;

import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicInteger;

/**
 * @author panyexiong
 * @version 1.0
 * @date 2019/9/2 10:38
 */
public class ProdConsumer_BlockQueueDemo {
    public static void main(String[] args) throws Exception {
        MyResource myResource = new MyResource(new ArrayBlockingQueue<>(3));

        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "\t 生产线程启动");
            try {
                myResource.myProd();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, "Prod").start();

        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "\t 消费线程启动");
            try {
                myResource.myConsumer();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, "Prod").start();


        TimeUnit.SECONDS.sleep(5);

        System.out.println("5秒钟时间到，main线程停止，活动结束");
        myResource.stop();
    }
}

class MyResource {
    private volatile boolean FLAG = true;
    private AtomicInteger atomicInteger = new AtomicInteger();

    BlockingQueue<String> blockingQueue = null;

    public MyResource(BlockingQueue<String> blockingQueue) {
        this.blockingQueue = blockingQueue;
        System.out.println(blockingQueue.getClass().getName());
    }

    public void myProd() throws Exception {
        String data = null;
        boolean retValue;
        while (FLAG) {
            data = atomicInteger.incrementAndGet() + "";
            retValue = blockingQueue.offer(data, 2L, TimeUnit.SECONDS);
            if (retValue) {
                System.out.println(Thread.currentThread().getName() + "\t 插入队列" + data + "成功");
            } else {
                System.out.println(Thread.currentThread().getName() + "\t 插入队列" + data + "失败");
            }
            TimeUnit.SECONDS.sleep(1);
        }
        System.out.println(Thread.currentThread().getName() + "\t 停止，表示FLAG=False，生产动作结束");
    }

    public void myConsumer() throws Exception {
        String result = null;
        while (FLAG) {
            result = blockingQueue.poll(2L, TimeUnit.SECONDS);
            if (null == result || result.equalsIgnoreCase("")) {
                FLAG = false;
                System.out.println(Thread.currentThread().getName() + "\t 超时2秒钟没有取到蛋糕，消费退出");
                return;
            }
            System.out.println(Thread.currentThread().getName() + "\t 消费队列" + result + "成功");
        }
    }

    public void stop() throws Exception {
        this.FLAG = false;
    }

}
//out
java.util.concurrent.ArrayBlockingQueue
Prod	 生产线程启动
Prod	 消费线程启动
Prod	 插入队列1成功
Prod	 消费队列1成功
Prod	 插入队列2成功
Prod	 消费队列2成功
Prod	 插入队列3成功
Prod	 消费队列3成功
Prod	 插入队列4成功
Prod	 消费队列4成功
Prod	 插入队列5成功
Prod	 消费队列5成功
5秒钟时间到，main线程停止，活动结束
Prod	 停止，表示FLAG=False，生产动作结束
Prod	 超时2秒钟没有取到蛋糕，消费退出
```



### 线程池

### 消息中间件