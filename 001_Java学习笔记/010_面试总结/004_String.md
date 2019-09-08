# String

## String的两种创建方式

1. 直接赋值

   ```java
   String str = "AA";
   ```

2. 通过构造方法

   ```java
   String str = new String("AA");
   ```

3. 两种方式比较

   通过第二种方式创建对象的时候，会先在常量池中查找有没有AA这个常量，如果有，则在常量池中创建一个AA对象；如果没有AA这个常量，则会在常量池中创建一个AA常量，并在堆中创建一个AA对象。

## 几种情况代码演示,考虑编译期和运行期。
如果代码在编译期值可以被确定，那么就使用已有对象，否在会创建新的对象。
```java
public class Demo01 {
    public static void main(String[] args) {
        String a = "a";
        String a1 = a + 1;
        String a2 = "a1";
        System.out.println(a1 == a2);
        System.out.println("============");
        
        final String b = "b";
        String b1 = b + "1";
        String b2 = "b1";
        System.out.println(b1 == b2);
        System.out.println("============");
    }
}
//out
false
============
true
```

