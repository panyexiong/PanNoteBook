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



# CyclicBarrier

# Semaphore

