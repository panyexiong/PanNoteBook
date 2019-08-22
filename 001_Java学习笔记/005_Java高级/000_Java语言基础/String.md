String类型变量的使用

1. String属于引用数据类型
2. 声明String类型变量是，使用一对""
3. String可以和八种基本数据类型变量作运算，并且只能作拼接运算+。
4. 运算结果仍然是String类型

```java
public static void main(String[] args) {
    int num1 = 0b110;
    int num2 = 110;
    int num3 = 0127;
    int num4 = 0x110A;

    System.out.println(num1);
    System.out.println(num2);
    System.out.println(num3);
    System.out.println(num4);
}

6
110
87
4362
```

