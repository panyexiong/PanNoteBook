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

# String源码分析

1. 常用构造方法

   1. String方法底层是一个用final修饰的char型数组，保证了String不可变型。
   2. 当创建一个String对象时，默认构造，不会对char数组进行初始化，大小为0。

   ```java
   //String底层是一个char型的数组，用final修饰，所以String是不可变的。
   private final char value[];
   
   //1.空参构造
   public String() {
       this.value = "".value;
   }
   
   //2.传入一个String类型的对象。
   public String(String original) {
       this.value = original.value;
       this.hash = original.hash;
   }
   
   //3.传入一个char型数组，调用Arrays工具类下的copyOf方法
   public String(char value[]) {
       this.value = Arrays.copyOf(value, value.length);
   }
   //4.copyOfRange方法
   public String(char value[], int offset, int count) {
       if (offset < 0) {
           throw new StringIndexOutO+fBoundsException(offset);
       }
       if (count <= 0) {
           if (count < 0) {
               throw new StringIndexOutOfBoundsException(count);
           }
           if (offset <= value.length) {
               this.value = "".value;
               return;
           }
       }
       // Note: offset or count might be near -1>>>1.
       if (offset > value.length - count) {
           throw new StringIndexOutOfBoundsException(offset + count);
       }
       this.value = Arrays.copyOfRange(value, offset, offset+count);
   }
   
   //5.
   public String(int[] codePoints, int offset, int count) {
       if (offset < 0) {
           throw new StringIndexOutOfBoundsException(offset);
       }
       if (count <= 0) {
           if (count < 0) {
               throw new StringIndexOutOfBoundsException(count);
           }
           if (offset <= codePoints.length) {
               this.value = "".value;
               return;
           }
       }
       // Note: offset or count might be near -1>>>1.
       if (offset > codePoints.length - count) {
           throw new StringIndexOutOfBoundsException(offset + count);
       }
   
       final int end = offset + count;
   
       // Pass 1: Compute precise size of char[]
       int n = count;
       for (int i = offset; i < end; i++) {
           int c = codePoints[i];
           if (Character.isBmpCodePoint(c))
               continue;
           else if (Character.isValidCodePoint(c))
               n++;
           else throw new IllegalArgumentException(Integer.toString(c));
       }
   
       // Pass 2: Allocate and fill in char[]
       final char[] v = new char[n];
   
       for (int i = offset, j = 0; i < end; i++, j++) {
           int c = codePoints[i];
           if (Character.isBmpCodePoint(c))
               v[j] = (char)c;
           else
               Character.toSurrogates(c, v, j++);
       }
   
       this.value = v;
   }
   
   ```

# StringBuilder源码

1. StringBuilder继承AbstractStringBuilder类

   1. AbstractStringBuilder类是一个抽象类，里面有一个成员变量，没有用final修饰所以StringBuilder是可变的。

      ```java
      abstract class AbstractStringBuilder implements Appendable, CharSequence {
          /**
           * The value is used for character storage.
           */
          char[] value;
      ```

      

2. 

# String与StringBuilder源码对比

1. 继承父类，实现接口--->String不可变，StringBuilder可变

   1. String

      ```java
      public final class String
          implements java.io.Serializable, Comparable<String>, CharSequence
      ```

   2. StringBuilder

      ```java
      public final class StringBuilder
          extends AbstractStringBuilder
          implements java.io.Serializable, CharSequence
      ```

   3. 对比总结

      1. String没有父类；而StringBuilder的父类是AbstractStringBuilder。
      2. String类底层是一个final修饰的char型数组，所以String类是不可变的。
      3. StringBuilder继承自AbstractStringBuilder，而AbstractStringBuilder抽象类中有一个成员变量，char类型的数组，没有用final修饰，所以是可变的。

2. 创建对象

   1. String可以通过构造方法的形式进行初始化，还可以直接赋值
   2. StringBuilder只能通过构造方法

3. 