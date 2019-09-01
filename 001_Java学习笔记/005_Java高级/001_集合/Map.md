# Map

- 双列数据，存储key-value

- 实现类：

  - HashMap：主要实现类；线程不安全，效率高；可以存储null的key和value；

    - LinkedHashMap:保证在遍历map元素时，可以按照添加的顺序实现遍历。

      原因：在原有的HashMap底层结构基础上，添加了一对指针，指向前一个添加的元素，和后一个添加的元素。

      对于频繁的遍历操作，此类执行效率高于HashMap。

  - TreeMap：按照添加的Key-value进行排序，实现排序遍历。

    - 此时考虑key的自然排序和定制排序。
    - 底层和TreeSet一样使用的时红黑树。

  - Hashtable：古老的实现类；线程安全，效率低；不可以存储null的key和value；

    - Properties：常用来处理配值文件，key和value都是String类型。

  HashMap的底层：JDK1.7及其之前使用数组+链表；1.8后使用数组+链表+红黑树

## 面试题

1. HashMap的底层实现原理
2. HashMap和Hashtable的异同
3. CurrentHashMap与Hashtable的异同

## Map结构的理解

- Map中key：无序的、不可重复的，使用Set存储所有的key -----> key所在的类要重写equals()和hashCode()方法
- Map中的value：无序的、可重复的，使用Collection存储所有的value-----> value所在的类要重写equals()
- 一个键值对：key-value构成一个Entry对象
- Map中的entry：无序的、不可重复的，使用Set存储所有的entry

## HashMap底层实现原理

### JKD7

- 第一步

  ```java
  HashMap<Integer,Integer> map = new HashMap<>();
  ```

  在实例化后，底层创建了一个长度16的一维数组Entry[] table；

- 第二步

  ```java
  map.put(key1,value1);
  ```

  - 首先调用key1所在类的hashCode方法计算key1的哈希值，此哈希值经过某种算法计算以后，得到Entry数组中的存放位置。
    - 如果此位置上的数据为空，则key-value添加成功---------情况1
    - 如果此位置上的数据不为空（意味着此位置上存在一个或多个数据，链表形式存储），比较当前key1和已经存在的一个或多个数据的哈希值：
      - 如果key1的哈希值与已经存在的数据的哈希值都不相同，此时key1-value1添加成功。---------情况2
      - 如果key1的哈希值和已经存在的某一个数据的哈希值相同，继续比较：调用key1所在类的equals方法，比较：
        - 如果equals方法返回false：此时key1-value1添加成功。---------情况3
        - 如果equals方法返回true：使用value1替换相同的key的value值。---------情况4

  补充：

  - 情况2和情况3：此时key-value和原来的数据以链表的方式存储。

#### 扩容问题：

在不断的添加过程中，会涉及到扩容问题，默认的扩容方式：扩容为原来容量的2倍，并将原有的数据复制过来。

### JDK8

JKD1.8相较于JDK1.7在底层实现方面的不同：

1. new HashMap（）：底层没有创建一个长度16的数组。

2. jdk1.8底层的数组是Node[] ，而非Entry[]

3. 首次使用put方法时，底层创建长度为16的数组

4. jdk1.7底层结构只有数组+链表；JDK1.8底层结构：数组+链表+红黑树

   当数组中某一个索引位置上的元素以链表形式存在的数据个数>8，且当前数据的长度>64时，

   此索引位置上的所有数据改为使用红黑树存储。

#### 源码分析

```java
public HashMap() {
    this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
}
```

## Map接口的常用方法

- 元视图操作的方法：

  - Set keySet()：返回所有key构成的Set集合
  - Collection values()：返回所有value构成的Collection集合
  - Set entrySet()：返回所有key-value构成的Set集合

  ```java
  import java.util.*;
  
  /**
   * @author panyexiong
   * @version 1.0
   * @date 2019/8/30 13:23
   */
  public class HashMapTest {
      public static void main(String[] args) {
          Map<Integer, Integer> map = new HashMap<>();
          map.put(1, 1);
          map.put(2, 4);
          map.put(3, 3);
          map.put(4, 2);
  
          Set set = map.keySet();
          Iterator iterator = set.iterator();
          while (iterator.hasNext()) {
              System.out.print(iterator.next() + " ");
          }
          System.out.println();
  
          Collection collection = map.values();
          Iterator iterator1 = collection.iterator();
          while (iterator1.hasNext()) {
              System.out.print(iterator1.next() + " ");
          }
          System.out.println();
  
          Set set1 = map.entrySet();
          for (Object obj :
                  set1) {
              System.out.print(obj + " ");
          }
          System.out.println();
      }
  }
  
  //out
  1 2 3 4 
  1 4 3 2 
  1=1 2=4 3=3 4=2 
  ```

  总结：

  - 添加：put(Object key, Object value)
  - 删除：remove(Object key)
  - 修改：put(Object key, Object value)
  - 查询：get(Object key)
  - 长度：size()
  - 遍历：keySet();values();entrySet()

## TreeMap

```java
package com.pan.commonClasses.map.TreeMapTest;

import org.junit.Test;

import java.util.Comparator;
import java.util.Iterator;
import java.util.Set;
import java.util.TreeMap;

/**
 * @author panyexiong
 * @version 1.0
 * @date 2019/8/30 20:05
 */
public class Demo01 {

    /**
     * 自然排序
     */
    @Test
    public void test01() {
        //向TreeMap中添加key-value，要求key必须是由同一个类创建的对象
        TreeMap<User, Integer> treeMap = new TreeMap<>();

        User user1 = new User("Tom", 21);
        User user2 = new User("Jerry", 32);
        User user3 = new User("Jack", 18);
        User user4 = new User("Rose", 41);
        treeMap.put(user1, 98);
        treeMap.put(user2, 89);
        treeMap.put(user3, 70);
        treeMap.put(user4, 92);

        Set set = treeMap.entrySet();
        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }

    /**
     * 定制排序
     */
    @Test
    public void test02() {
        TreeMap<User, Integer> treeMap = new TreeMap<>(new Comparator<User>() {
            @Override
            public int compare(User o1, User o2) {
                return Integer.compare(o1.getAge(),o2.getAge());
            }
        });

        User user1 = new User("Tom", 21);
        User user2 = new User("Jerry", 32);
        User user3 = new User("Jack", 18);
        User user4 = new User("Rose", 41);
        treeMap.put(user1, 98);
        treeMap.put(user2, 89);
        treeMap.put(user3, 70);
        treeMap.put(user4, 92);

        Set set = treeMap.entrySet();
        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }
}
```

## Properties

常用来处理配值文件。key和value都是String类型。

