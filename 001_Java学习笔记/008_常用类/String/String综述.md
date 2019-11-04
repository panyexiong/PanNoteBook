## Java运行时数据区域与常量池

- 程序计数器：多线程时，当线程数超过CPU内核数量，线程之间就要根据时间片轮询抢夺CPU时间资源。因此，每个线程要有一个独立的程序计数器，记录下一条要运行的指令，其为线程私有的内存区域。如果执行的是Java方法，计数器记录正在执行的Java字节码地址，如果执行的是native方法，则计数器为空。
- 虚拟机栈：线程私有的，与线程在同一时刻创建，是管理Java方法执行的内存模型。栈中主要存放一些基本类型的变量数据和对象引用。每个方法执行时都会创建一个栈帧来存放方法的变量表、操作数栈、动态链接方法、返回值、返回地址等信息。
- Java堆，存放所有对象实例和数组，是垃圾回收的主要区域。
- 方法区：线程共享的，用于存放被虚拟机加载的类的元数据信息：常量、静态变量、即时编译器后的代码。
- 常量池：常量池属于类信息的一部分，而类信息反映到JVM内存模型中对应方法区，也就是说，常量池位于方法区。常量池主要存放两大常量：字面量和符号引用。
  - 字面量：字符串字面量、整形字面量、声明为final的常量值
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

所以，通过new操作产生一个字符串”abc“时，会先去常量池中查找是否有”abc“对象，如果没有，则创建一个此字符串对象并放入常量池中。然后，在堆中再创建”abc“对象，并返回该对象的地址。

## 字符串常量池

### 情景1：字符串常量池

JVM中存在着一个字符串常量池，其中保存着很多String对象，并且这些String对象可以被共享使用，因此提高了效率。我们可以通过intern方法手动入池。

### 情景2：字符串连接符”+“

```java
public void test02() {
    String s1 = "ab";
    String s2 = "cd";
    String s3 = s1 + s2;
    String s4 = "abcd";
    System.out.println(s3 == s4);	//false
}
```

### 情景3：字符串的编译期优化

```java
public void test04() {
    String str1 = "ab" + "cd";
    String str2 = "abcd";
    System.out.println(str1 == str2);

    final String str3 = "cd";
    String str4 = "ab" + str3;
    System.out.println(str2 == str4);

    String str5 = "b";
    String str6 = "a" + str5;
    String str7 = "ab";
    System.out.println(str6 == str7);
}
//
true
true
false
```

Java编译器对于类似 常量+字面量 的组合，其值在编译的时候就能够被确定。

## 三大字符串类：String、StringBuilder、StringBuffer

### 编译时优化与字符串连接符的本质

```java
public void test04() {
    String s = "a" + "b" + "c";
    String s1 = "a";
    String s2 = "b";
    String s3 = "c";
    String s4 = s1 + s2 + s3;

    System.out.println(s);
    System.out.println(s4);
}
//
abc
abc
```

每做一次字符串连接操作 + 就产生一个StringBuilder对象，然后append后就扔掉。下次循环到达时，再重新new一个StringBuilder对象，然后append字符串，如此循环。事实上，如果我们直接采用StringBuilder对象进行append的话，我们可以节省N-1次创建和销毁对象的时间。所以对于循环中要进行字符串连接的应用，一般都是用StringBuilder对象。

## String与（深）克隆

克隆的定义意义：顾名思义，克隆就是制造一个对象的副本。一般地，根据所要克隆的对象的成员变量中是否含有引用类型，可以将克隆分为两种：浅克隆（Shallow Clone）和深克隆（Deep Clone），默认情况下使用Object中的clone方法进行克隆就是浅克隆，即完成对象域对域的拷贝。

### Object中的clone（）方法

在使用clone（）方法时，若该类未实现Cloneable接口，则抛出 java.lang.CloneNotSupportedException异常。

Cloneable接口是一个标识性接口，即该接口不包含任何方法，但是如果一个类像合法的进行克隆，那么就必须实现这个接口。

### Clone & Copy

假设现在有一个Employee对象，Employee tobby = new Employee(“CMTobby”,5000)，通常, 我们会有这样的赋值Employee tom=tobby，这个时候只是简单了copy了一下reference，tom 和 tobby 都指向内存中同一个object，这样tom或者tobby对对象的修改都会影响到对方。

### Shallow Clone & Deep Clone

Clone是如何完成的呢？Object中的clone()方法在对某个对象实施克隆时对其是一无所知的，它仅仅是简单地执行域对域的copy，这就是Shallow Clone。这样，问题就来了，以Employee为例，它里面有一个域hireDay不是基本数据类型的变量，而是一个引用变量，经过clone之后克隆类只会产生一个新的引用，它和原始引用都指向同一个对象。