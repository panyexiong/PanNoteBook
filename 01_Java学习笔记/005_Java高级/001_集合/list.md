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

JDK7有点像单例模式中的饿汉式，JDK8有点像懒汉式，延时了数组的创建，节省内存。

### JDK_7

JDK7中，new一个ArrayList的时候，会直接创建大小为10的Object数组。

### JDK_8

```java
import java.util.ArrayList;
/**
 * @author panyexiong
 * @version 1.0
 * @date 2019/8/29 21:44
 */
public class Demo01 {
    public static void main(String[] args) {
        ArrayList arrayList = new ArrayList();		//1
        arrayList.add(1);						    //2
        arrayList.add(2);						    //3
    }
}

```

1. 第一步，new一个ArrayList的时候，底层new一个Object类型的数组：Object[] elementData，调用了ArrayList类中的空参构造方法将其设置为空数组。

   注意：JDK8中elementData数组创建时为空，源码解释为错误。`Constructs an empty list with an initial capacity of ten.`。第一次调用add()方法时，底层才创建了长度10的数据，并将数据添加到第一个位置上。

   ```java
       /**
        * Constructs an empty list with an initial capacity of ten.
        */
       public ArrayList() {
            //DEFAULTCAPACITY_EMPTY_ELEMENTDATA为空
           this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
       }
       
        private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
   ```

2. 第二步，第一次向arrayList中添加数据的时候，ArrayList类中的add方法会调用一个ensureCapacityInternal方法，传入一个参数，当前数组elementData大小+1。在这个方法里面会判断第一步new出来的elementData数组是不是为空即是不是第一次创建，若为空则返回一个默认的值DEFAULT_CAPACITY=10。判断10是不是比当前数组elementData的长度大，大则通过grow(minCapacity)方法将数组扩容。

   ```java
   arrayList.add(1);
   
   	//执行了底层的这个方法，第一次添加所以size=0
       public boolean add(E e) {
           ensureCapacityInternal(size + 1);  // Increments modCount!!
           elementData[size++] = e;
           return true;
       }
   	//ensureCapacityInternal(size + 1)
       private void ensureCapacityInternal(int minCapacity) {
           ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
       }
   	//calculateCapacity(elementData, minCapacity)
   	//DEFAULT_CAPACITY=10
       private static int calculateCapacity(Object[] elementData, int minCapacity) {
           if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
               return Math.max(DEFAULT_CAPACITY, minCapacity);
           }
           return minCapacity;
       }
   	//ensureExplicitCapacity(calculateCapacity(elementData, minCapacity))
       private void ensureExplicitCapacity(int minCapacity) {
           modCount++;
   
           // overflow-conscious code
           if (minCapacity - elementData.length > 0)
               grow(minCapacity);
       }
       /**
        * Increases the capacity to ensure that it can hold at least the
        * number of elements specified by the minimum capacity argument.
        *
        * @param minCapacity the desired minimum capacity
        */
       private void grow(int minCapacity) {
           // overflow-conscious code
           int oldCapacity = elementData.length;
           //当底层数组不够存放数据时，扩容为原来的1.5倍，原理就是下面这条语句。
           //(oldCapacity >> 1)右移1位表示oldCapacity除以2
           int newCapacity = oldCapacity + (oldCapacity >> 1);
           if (newCapacity - minCapacity < 0)
               newCapacity = minCapacity;
           if (newCapacity - MAX_ARRAY_SIZE > 0)
               newCapacity = hugeCapacity(minCapacity);
           // minCapacity is usually close to size, so this is a win:
           elementData = Arrays.copyOf(elementData, newCapacity);
       }
   ```

   

```java
//源码总结
public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, java.io.Serializable{
    /**
     * Default initial capacity.
     */
    private static final int DEFAULT_CAPACITY = 10;
    
    /**
     * The size of the ArrayList (the number of elements it contains).
     *
     * @serial
     */
    private int size;
    
    /**
     * Shared empty array instance used for default sized empty instances. We
     * distinguish this from EMPTY_ELEMENTDATA to know how much to inflate when
     * first element is added.
     */
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
    
    transient Object[] elementData;
    
    //ArrayList类中的构造方法
	public ArrayList() {
    	this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
    
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
    
    private void ensureCapacityInternal(int minCapacity) {
        ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
    }
    
    private static int calculateCapacity(Object[] elementData, int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        return minCapacity;
    }
    
    
    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }
}
```

## LinkedList源码分析

JDK7和JDK8没有很大的区别

```java
public class Demo01 {
    public static void main(String[] args) {
        LinkedList linkedList = new LinkedList();
        linkedList.add(1);
    }
}
```

```java
    private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```

- add()方法

  ```java
      /**
       * Appends the specified element to the end of this list.
       *
       * <p>This method is equivalent to {@link #addLast}.
       *
       * @param e element to be appended to this list
       * @return {@code true} (as specified by {@link Collection#add})
       */
      public boolean add(E e) {
          linkLast(e);
          return true;
      }
  
      /**
       * Links e as last element.
       */
      void linkLast(E e) {
          final Node<E> l = last;
          final Node<E> newNode = new Node<>(l, e, null);
          last = newNode;
          if (l == null)
              first = newNode;
          else
              l.next = newNode;
          size++;
          modCount++;
      }
  ```

## Vector源码分析

创建对象时，底层创建了长度为10的数组

扩容:扩容为原来的2倍

```java
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                     capacityIncrement : oldCapacity);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

## 面试题

```java
@Test
public void testListRemove(){
    List list = new ArrayList();
    //add中的数值自动装箱，按顺序存值
    list.add(1);
    list.add(2);
    list.add(3);
    //remove方法按索引位置删除值
    list.remove(2);
    //手动装箱，删除2
    list.remove(new Integer(2));
    System.out.println(list);
}
//out
[1]
```