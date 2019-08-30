# List接口

- List集合类中的元素有序，且可重复
- List容器中的元素都有对应一个整数型的序号记载其在容器中的位置，可以根据序号存取容器中的元素
- JDK API中List接口的实现类常有：ArrayList，LinkedList，和Vector。

## ArrayList,LinkedList,Vector的异同：

- 相同：三个类都实现了List接口，存储数据的特点相同。
- 不同：
  - ArrayList：作为List接口的主要实现类；线程不安全，执行效率高；底层使用Object[]数组存储
  - LinkedList：底层使用双向链表存储，对于频繁的插入，删除操作使用此类效率比ArrayList效率要高。
  - Vector：作为List接口的古老实现类；线程安全，执行效率低；底层使用Object[]数组存储

## ArrayList源码分析



## LinkedList源码分析

## Vector源码分析