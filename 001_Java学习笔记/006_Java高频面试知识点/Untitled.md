# volatile关键字

## volatile是Java虚拟机提供的轻量级的同步机制

1. 保证可见性
2. 不保证原子性
   原子性：不可分割，完整性，也即某个线程正在做某个具体的业务时，中间不可以被加塞或者被分割。需要整体完整要么同时成功，要么同时失败。
3. 禁止指令重排

## 保证可见性

```java
//保证可见性
public class Demo02 {
   public static void main(String[] args) {
      Thread01 thread01 = new Thread01();

      new Thread(()->{
          System.out.println(Thread.currentThread().getName()+"\t come in");
          try {
              TimeUnit.SECONDS.sleep(3);
          } catch (InterruptedException e) {
              e.printStackTrace();
          }
           thread01.addTo();
          System.out.println(Thread.currentThread().getName()+"\t updated number value"+thread01.number);
      },"AAA").start();

      while(thread01.number == 0){

      }
       System.out.println(Thread.currentThread().getName()+"\t mission is over");
   }
}

class Thread01 extends Thread{
   volatile int number = 0;

   public void addTo(){
       this.number = 100;
   }
}
//输出
AAA	 come in
AAA	 updated number value100
main	 mission is over
```

volatile不保证原子性代码示例

```java
/**
* 验证volatile的不保证原子性
* @author panyexiong
* @version 1.0
* @date 2019/8/27 20:24
*/
public class Demo02 {
   public static void main(String[] args) {
       Thread02 thread02 = new Thread02();

       for (int i = 0; i < 20; i++) {
           new Thread(() -> {
               for (int j = 0; j < 1000; j++) {
                   thread02.addPlusPlus();
               }
           },String.valueOf(i)).start();
       }

       while(Thread.activeCount() > 2){
           Thread.yield();
       }
       System.out.println(Thread.currentThread().getName()+"\t finally number value:"+thread02.number);
   }
}

class Thread02 extends Thread{
   volatile int number = 0;

   public void addTo60(){
       this.number = 60;
   }

   public void addPlusPlus(){
       number++;
   }
}
//输出
main: finally number value:19834
```

## 如何解决原子性：

1. 加synchronized关键字

2. 使用AtomicInteger
   
```java
  //使用atomicInteger
  AtomicInteger atomicInteger = new AtomicInteger();
```

## 禁止指令重排序：

   JMM要求有序性。计算机在执行程序是，为了提高性能，编译器和处理器常常会对指令重排，一般分以下3种：

   源代码---编译器优化的重排---指令并行的重排---内存系统的重排---最终执行的指令

   - 单线程环境里面保证程序最终执行结果和代码顺序执行的结果一样。
   - 处理器在进行重排序时，必须要考虑指令之间的数据依赖性
   - 多线程环境中线程交替执行，由于编译器优化重排的存在，两个线程中使用的变量能否保证一致性是无法确定的，结果无法预测。

   volatile实现禁止指令重排优化，从而避免多线程环境下程序出现乱序的现象。

   内存屏障，是一个cpu指令，他有两个作用：

   1. 保证特定的操作的执行顺序
   2. 保证某些变量的内存可见性（利用该特性实现了volatile的内存可见性）

   由于编译器和处理器都能执行指令重排优化。如果在指令间插入一条内存屏障则会告诉编译器和CPU，不管什么指令都不能和这条内存屏障指令重排序，也就是说通过插入内存屏障禁止在内存屏障前后的指令执行重排序优化。内存屏障另外一个作用就是强制刷出各种CPU的缓存数据，因此任何CPU上的线程都能读取到这些数据的最新版本。

## JMM：可见性，原子性，有序性

工作内存与主内存同步延迟现象导致的可见性问题：

可以使用synchronized和volatile关键字解决，它们都可以使用一个线程修改后的变量立即对其他线程可见。

对于指令重排导致的可见性问题和有序性问题：

可以利用volatile关键字解决，因为volatile的另外一个作用就是禁止重排序优化。