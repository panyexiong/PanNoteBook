Object类的使用

java.lang.Object类

Object类中的功能（属性，方法）就具有通用性

- clone():
- equals():
- finalize():
- getClass():
- hashCode():
- toString():
- notify():
- notifyAll():
- wait()

toString()的使用

- 当我们输出一个对象的引用时，直接调用该对象的toString方法
- String，Date，File都重写了Object类中的toString方法

```java
//Object中的toString方法    
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

基本数据类型，包装类，String三者之间的相互转换

```java
public class Demo01 {

    /**
     * 基本数据类型 ---> 包装类
     */
    @Test
    public void test01() {
        int num1 = 10;
        Integer integer1 = new Integer(num1);
        System.out.println(integer1.toString());

        Integer integer = new Integer("123");
        System.out.println(integer);

        Integer integer2 = 101;
    }

    /**
     * 基本数据类型、包装类 ----> String类型
     */
    @Test
    public void test02() {
        int num1 = 10;
        //方式1：连接运算
        String str1 = num1 + "";
        //方式2：调用String重载的valueOf()
        float f1 = 1.2f;
        String str2 = String.valueOf(f1);
    }

    /**
     * String ----> 基本数据类型，包装类
     */
    @Test
    public void test03(){
        String str = "123";
        int i = Integer.parseInt(str);
    }
}

```

面试题

```java
    @Test
    public void test04() {
        Integer i = new Integer(1);
        Integer j = new Integer(1);
        System.out.println(i == j);

        Integer m = 1;
        Integer n = 1;
        System.out.println(m == n);

        Integer x = 128;
        Integer y = 128;
        System.out.println(x == y);
    }

//结果
false
true
false
```

