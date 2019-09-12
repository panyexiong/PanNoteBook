# HashMap的构造方法

1. 空参构造方法

   ```java
   public HashMap() {
       this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
   }
   ```

   HashMap底层使用一个Node类型的数组来存储数据，调用空参构造的时候，会创建一个空的Node数组。

   使用默认的负载因子0.75f。

   负载因子主要用于扩容。

2. 一个参数的构造方法

   ```java
   public HashMap(int initialCapacity) {
       this(initialCapacity, DEFAULT_LOAD_FACTOR);
   }
   ```

   一个参数的构造方法其实也就是负载因子为默认的0.75f。

3. 两个参数的构造方法

   ```java
   public HashMap(int initialCapacity, float loadFactor) {
       if (initialCapacity < 0)
           throw new IllegalArgumentException("Illegal initial capacity: " +
                                              initialCapacity);
       if (initialCapacity > MAXIMUM_CAPACITY)
           initialCapacity = MAXIMUM_CAPACITY;
       if (loadFactor <= 0 || Float.isNaN(loadFactor))
           throw new IllegalArgumentException("Illegal load factor: " +
                                              loadFactor);
       this.loadFactor = loadFactor;
       this.threshold = tableSizeFor(initialCapacity);
   }
   ```

   注意：使用第二种和第三种构造方法的时候，指定HashMap的大小，但是HashMap底层Node数组并不是这个大小，而是将其扩大为最接近它的2的指数次幂。例如当指定大小为13的时候，会调用`tableSizeFor()`方法对其进行放大。关于为什么要这么做？主要是为了后面向Map中添加元素的时候用到。

   ```java
   HashMap<String, Integer> map1 = new HashMap<>(13);
   
   static final int tableSizeFor(int cap) {
       int n = cap - 1;
       n |= n >>> 1;
       n |= n >>> 2;
       n |= n >>> 4;
       n |= n >>> 8;
       n |= n >>> 16;
       return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
   }
   ```

# `put()`方法

1. 调用put方法会调用HashMap中的`putVal()`方法。主要涉及到三个参数，key的hash值，key，value。

   ```java
   public V put(K key, V value) {
       return putVal(hash(key), key, value, false, true);
   }
   ```

2. key的hash值是如何计算的来的呢？
   1. 调用了hashMap中的`hash()`方法，如果key为null，则返回0，这也就是为什么HashMap可以存储key==null的原因。如果key！=null，则通过key所属类的`hashCode()`方法计算器hashCode值，再将其与自身无符号右移16位得到的值相异或，最终返回这个值。为什么要这样做？防止在后面计算新元素在Node数组中插入位置的索引的时候，尽可能避免hash冲突。
   2. 补充说明：
   1. Integer类的hashCode方法就直接返回传入的值;
      2. String类的hashCode方法
      3. Boolean类的hashCode方法，如果为true则返回1231，false返回1237(为什么？)。


```java
   static final int hash(Object key) {
    int h;
       return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
   
//Integer.java
   public static int hashCode(int value) {
    return value;
   }

   //String.java
public int hashCode() {
       int h = hash;
       if (h == 0 && value.length > 0) {
           char val[] = value;
           for (int i = 0; i < value.length; i++) {
               h = 31 * h + val[i];
           }
           hash = h;
       }
       return h;
   }
   
   //Boolean.java
   public static int hashCode(boolean value) {
       return value ? 1231 : 1237;
   }
```

3. `putVal()`方法

   1. 先判断当前的Node数组是不是为空，为空代表第一次添加元素，就通过resize()方法对其进行扩容。
2. 然后计算出添加的新元素在Node数组中应该插入的位置的索引。
   
   1. `(n-1) & hash`，其中n为当前Node数组的长度，hash为上一步根据key值计算出来的hash值。创建HashMap对象的时候提到传入的参数会被自动放大到最接近它的2的指数次幂的数。例如传入13，会变成16。此时n==16，n-1==15，15的二进制表示为0000 1111，只有最低4位为1，高位都为0，所以当15&hash值的时候，不管hash值有多大，最终&出来的值都会保持在0-15内，也就是Node数组长度之内。
3. 判断Node数组在上一步计算得出的索引位置上是不是为空。
      1. 如果为空，表示Node数组当前位置还没有数据，直接调用`tab[i] = newNode(hash, key, value, null)`插入新元素成功；//1
   2. 如果不为空，表示Node数组当前位置已经有数据了，发生了hash冲突。这个时候继续判断当前Node数组当前索引位置上元素的hash值、key值和待插入元素的hash值、key值相不相同？
         1. 

   ```java
   final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                  boolean evict) {
       Node<K,V>[] tab; Node<K,V> p; int n, i;
       //判断数组有没有进行初始化
       if ((tab = table) == null || (n = tab.length) == 0)
           //初始化数组，扩容数组
           n = (tab = resize()).length;
       //判断Node数组待插入位置有没有元素
       if ((p = tab[i = (n - 1) & hash]) == null)
           //没有，直接插入，插入成功
           tab[i] = newNode(hash, key, value, null);
       else {
           Node<K,V> e; K k;
           //判断key,hash值是不是相同，是，则替换value值。
           if (p.hash == hash &&
               ((k = p.key) == key || (key != null && key.equals(k))))
               e = p;
           //判断p是不是红黑树
           else if (p instanceof TreeNode)
              	//如果是红黑树，则向书里面添加元素
               e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
           //不是红黑树
           else {
               //binCount用于对整条链子进行计数，用于转变红黑树
               for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    //如果当前节点的下一个节点为空，则插入新元素
                       p.next = newNode(hash, key, value, null);
                    	//如果binCount大于等于8-1=7时，则将链表转换成红黑树
                       if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                           treeifyBin(tab, hash);
                       break;
                   }
                   if (e.hash == hash &&
                       ((k = e.key) == key || (key != null && key.equals(k))))
                       break;
                   //继续for循环
                   p = e;
               }
           }
           //替换旧值
           if (e != null) { // existing mapping for key
               V oldValue = e.value;
               if (!onlyIfAbsent || oldValue == null)
                   e.value = value;
               afterNodeAccess(e);
               return oldValue;
           }
       }
       ++modCount;
       //判断Node数组的大小是否到达扩容的阈值
       if (++size > threshold)
           //进行扩容
           resize();
       afterNodeInsertion(evict);
       return null;
   }
   
   ```


# 扩容方法resize()



```java
final Node<K,V>[] resize() {
    //取到原来数组
    Node<K,V>[] oldTab = table;
    //取到原来数组大小
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    //取到旧的阈值,阈值=数组大小*负载因子；size*0.75f
    int oldThr = threshold;
    int newCap, newThr = 0;
    //判断原来数组大小是不是大于0，大于表示扩容.
    if (oldCap > 0) {
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        //如果原来的容量*2小于最大容量并且大于等于默认初始化容量16，则扩大2倍
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else {               // 这里为Node数组初始化，默认大小1>>>4=16，阈值16*0.75f=12
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) {
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

# 链表转红黑树



```java
final void treeifyBin(Node<K,V>[] tab, int hash) {
    int n, index; Node<K,V> e;
    if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
        resize();
    else if ((e = tab[index = (n - 1) & hash]) != null) {
        TreeNode<K,V> hd = null, tl = null;
        do {
            TreeNode<K,V> p = replacementTreeNode(e, null);
            if (tl == null)
                hd = p;
            else {
                p.prev = tl;
                tl.next = p;
            }
            tl = p;
        } while ((e = e.next) != null);
        if ((tab[index] = hd) != null)
            hd.treeify(tab);
    }
}
```



