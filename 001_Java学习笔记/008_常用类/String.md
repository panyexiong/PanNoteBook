# String特性

- String是一个final类，待变不可变的字符序列，不能被继承。
- String对象的字符内容存储在一个字符数组value[]中。
- 实现了Serializable接口，可序列化

## String对象的创建

```java
@Test
public void test02() {
    String s1= "JavaEE";
    String s2= "JavaEE";

    String s3 = new String("JavaEE");
    String s4 = new String("JavaEE");

    System.out.println(s1==s2);
    System.out.println(s1==s3);
    System.out.println(s1==s4);
    System.out.println(s3==s4);
}
```

```shell
true
false
false
false
```

