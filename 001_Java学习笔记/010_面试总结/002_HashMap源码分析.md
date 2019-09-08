1. HashMap的构造方法

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

2. `put()`方法

   1. 调用put方法会调用HashMap中的`putVal()`方法。主要涉及到三个参数，key的hash值，key，value。

      ```java
      public V put(K key, V value) {
          return putVal(hash(key), key, value, false, true);
      }
      ```

   2. key的hash值是如何计算的来的呢？

      调用了hashMap中的`hash()`方法，如果key为null，则返回0，这也就是为什么HashMap可以存储key==null的原因。如果key！=null，则通过key所属类的`hashCode()`方法计算器hashCode值，再将其与自身无符号右移16位得到的值相异或，最终返回这个值。为什么要这样做？防止在后面计算新元素在Node数组中插入位置的索引的时候，尽可能避免hash冲突。

      ```java
      static final int hash(Object key) {
          int h;
          return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
      }
      ```

   3. `putVal()`方法

      1. 先判断当前的Node数组是不是为空，为空代表第一次添加元素，就通过resize()方法对其进行扩容。

      2. 然后计算出添加的新元素在Node数组中应该插入的位置的索引。

         `(n-1) & hash`，其中n为当前Node数组的长度，hash为上一步根据key值计算出来的hash值。创建HashMap对象的时候提到传入的参数会被自动放大到最接近它的2的指数次幂的数。例如传入13，会变成16。此时n==16，n-1==15，15的二进制表示为0000 1111，只有最低4位为1，高位都为0，所以当15&hash值的时候，不管hash值有多大，最终&出来的值都会保持在0-15内，也就是Node数组长度之内。

      3. 

      ```java
      final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                     boolean evict) {
          Node<K,V>[] tab; Node<K,V> p; int n, i;
          if ((tab = table) == null || (n = tab.length) == 0)
              n = (tab = resize()).length;
          if ((p = tab[i = (n - 1) & hash]) == null)
              tab[i] = newNode(hash, key, value, null);
          else {
              Node<K,V> e; K k;
              if (p.hash == hash &&
                  ((k = p.key) == key || (key != null && key.equals(k))))
                  e = p;
              else if (p instanceof TreeNode)
                  e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
              else {
                  for (int binCount = 0; ; ++binCount) {
                      if ((e = p.next) == null) {
                          p.next = newNode(hash, key, value, null);
                          if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                              treeifyBin(tab, hash);
                          break;
                      }
                      if (e.hash == hash &&
                          ((k = e.key) == key || (key != null && key.equals(k))))
                          break;
                      p = e;
                  }
              }
              if (e != null) { // existing mapping for key
                  V oldValue = e.value;
                  if (!onlyIfAbsent || oldValue == null)
                      e.value = value;
                  afterNodeAccess(e);
                  return oldValue;
              }
          }
          ++modCount;
          if (++size > threshold)
              resize();
          afterNodeInsertion(evict);
          return null;
      }
      
      ```

      