# Collections

- Collections是一个操作Set、List、和Map等集合的工具类。

## 排序

- 反转List中的元素的顺序

  ```java
  Collections.reverse(list);
  ```

- 对List集合元素进行随机排序

  ```java
  Collections.shuffle(list);
  ```

  

- 根据元素的自然排序顺序对指定List集合元素按升序排序

  ```java
  Collections.sort(list);
  ```

  

- 根据指定的Comparator产生的顺序对List集合元素进行排序

  ```java
  
  ```

  

- 将指定List集合中的i处元素和j出元素进行交换

  ```java
  Collections.swap(list,1,2);
  ```

  

## 查找、替换

- 返回给定集合中的最大元素

  ```java
  Object max(Collection);
  Object max(Collection, Comparator);
  
  Object min(Collection);
  Object min(Collection, Comparator);
  ```

- 返回指定元素出现的次数

  ```java
  int frequency(Collection, Object)
  ```

- 将src中的内容复制到dest中

  ```java
  void copy(List dest, List src);
  ```

- 使用新值替换List对象对应旧值

  ```java
  boolean replaceAll(List list, Object oldValue, Object newValue);
  ```

## 同步控制方法

Collections类中的多个synchronizedXxx（）方法，该方法可使将指定集合包装成线程同步的集合，从而可以解决多线程并发访问集合时的线程安全问题。

```java
List list1 = Collections.synchronizedList(list);
```

## 练习题

1. 从键盘随机输入10个整数保存到list中，并按倒序、从大到小的顺序显示出来

   

2. 请把学生名与考试分数录入到集合中，并按分数显示前三名成绩学员的名字