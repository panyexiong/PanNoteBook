## Java运行时数据区域与常量池

- 程序计数器：多线程时，当线程数超过CPU内核数量，线程之间就要根据时间片轮询抢夺CPU时间资源。因此，每个线程要有一个独立的程序计数器，记录下一条要运行的指令，其为线程私有的内存区域。如果执行的是Java方法，计数器记录正在执行的Java字节码地址，如果执行的是native方法，则计数器为空。
- 虚拟机栈：线程私有的，与线程在同一时刻创建，是管理Java方法执行的内存模型。栈中主要存放一些基本类型的变量数据和对象引用。每个方法执行时都会创建一个栈帧来存放方法的变量表、操作数栈、动态链接方法、返回值、返回地址等信息。
- Java堆，存放所有对象实例和数组，是垃圾回收的主要区域。
- 方法区：线程共享的，用于存放被虚拟机加载的类的元数据信息：常量、静态变量、即时编译器后的代码。
- 常量池：常量池属于类信息的一部分，而类信息反映到JVM内存模型中对应方法区，也就是说，常量池位于方法区。常量池主要存放两大常量：字面量和符号引用。
  - 字面量：字符串字面量、整形字面量和声明为final的常量值
  - 符号引用：类的接口的全限定名；字段的名称和描述符；方法的名称和描述符。

## 常量和变量

一般把内存地址不变，值可以改变的东西成为变量。

一般把内存地址不变，则值也不可以改变的东西称为常量。

## String的不可变性

在Java中，引用是访问、操纵对象的唯一方式：我们不能直接操作对象本身，所有的对象都由一个引用指向，必须通过这个引用才能访问对象本身。

### String对象真的不可变吗？

String的成员变量是private final修饰的。value比较特殊，因为它是一个引用变量，而不是真正的对象。value是final修饰的，也就是说value不能再指向其他数组对象，那么我们可以改变value指向的数组！！！可以通过反射，反射出String对象中的value属性，进而改变通过获得的value引用改变数组的结构。

```JAVA
public static void main(String[] args) throws Exception {
    String s = "HelloWorld";
    String s1 = "HelloWorld";
    System.out.println("s= " + s);

    Field value = String.class.getDeclaredField("value");
    value.setAccessible(true);
    char[] values = (char[]) value.get(s);

    values[5] = '_';
    System.out.println("s= " + s);
    System.out.println(s == s1);
    System.out.println(s1);
}
//
s= HelloWorld
s= Hello_orld
true
Hello_orld
```

## String对象创建方式

### 字面量形式

JVM会自动根据字符串常量池中字符串的实际情况来决定是否创建新的对象。

```java
String s = "abc";

char data[] = {'a','b','c'};
String s = new String(data);
```



该种方式先在栈中创建一个对String类的对象应用s，然后去查找”abc“是否被保存再字符串常量池中。若”abc“已经被保存在字符串常量池中，则再字符串常量池中找到值为”abc“的对象，然后将s指向这个对象。否则，在堆中创建char数组data，然后再堆中创建一个String对象object，它由data数组支持，然后将String对象object被存入字符串常量池中，最后将s执行这个对象。

```java
public void test01() {
    String s0 = "abcd";
    String s1 = "abcd";
    String s2 = "ab" + "cd";
    System.out.println(s0==s1);	//true
    System.out.println(s0==s2); //true
}
```

### 通过new创建字符串对象

一概在堆中创建新对象，无论字符串字面值是否相等

```java
String s = new String("abc");

String s1 = "abc";
String s = new String(s1);
```







