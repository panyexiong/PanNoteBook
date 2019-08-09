# Map

## Hashtable：

古老的实现类；线程安全，效率低；不能存储null的key和value

Proprerties:常用来处理配置文件。key和value都是String类型的。

## HashMap：

作为Map的主要实现类；线程不安全，效率高；可以存储null的key和value

**LinkedHashMap**：作为HashMap的子类。保证在遍历Map元素时，可以按照添加的顺序实现遍历。原因：在原有的HashMap底层结构基础上，添加了一对指针，指向前一个和后一个元素。对于频繁的遍历操作，此类执行效率高于Hashmap.

## TreeMap：

保证按照添加的key-value对进行排序，实现排序遍历。底层使用红黑树。

## SortedMap

## 总结

HashMap的底层：JDK1.7及其之前使用数组+链表；1.8后使用数组+链表+红黑树

## 面试题

1. HashMap的底层实现原理
2. HashMap和Hashtable的异同
3. CurrentHashMap与Hashtable的异同

## Map结构的理解：

- Map中key是无序的、不可重复的，使用Set存储所有的key
- Map中的value：无序的、可重复的，使用Collection存储所有的value
- 一个键值对：key-value构成一个Entry对象
- Map中的entry：无序的、不可重复的，使用Set存储所有的entry

## HashMap底层实现原理?JDK1.7为例

```java
HashMap map = new HashMap();
```

在实例化以后，底层创建了一个长度为16的一维数组Entry[] table。

```java
map.put(key1,value1);
```

首先调用key1所在类的hashCode方法计算key1哈希值，此哈希值经过某种算法计算后，得到Entry数组中的存放位置。

- 如果此位置上的数据为空，此时的key1-value1添加成功。
- 如果此位置上的数据不为空（意味着此位置上存在一个或多个数据，链表形式存储），比较当前key1和已经存在的一个或多个数据的哈希值：
  - 如果key1的哈希值与已经存在的数据的哈希值都不相同，此时key1-value1添加成功。
  - 如果key1的哈希值和已经存在的某一个数据的哈希值相同，继续比较：调用key1所在类的equals方法，比较：
    - 如果equals方法返回false：此时key1-value1添加成功。
    - 如果equals方法返回true：使用value1替换相同的key的value值。

在不断的添加过程中，会涉及到扩容问题，默认的扩容方式：扩容为原来容量的两倍，并将原有的数据复制过来。

JKD1.8相较于JDK1.7在底层实现方面的不同：

1. new HashMap（）：底层没有创建一个长度16的数组。

2. jdk1.8底层的数组是Node[] ，而非Entry[]

3. 首次使用put方法时，底层创建长度为16的数组

4. jdk1.7底层结构只有数组+链表；JDK1.8底层结构：数组+链表+红黑树

   当数组中某一个索引位置上的元素以链表形式存在的数据个数>8，且当前数据的长度>64时，

   此索引位置上的所有数据改为使用红黑树存储。

