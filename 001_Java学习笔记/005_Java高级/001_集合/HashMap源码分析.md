1.  jdk1.8中的hash算法，对key进行hash。
```java
//HashMap.java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```
-   解释：
    -   判断key值为空，为空则返回0；
    -   判断key值不为空，然后根据key所属类的hashCode()方法，得到key的hashCode值。然后将这个值无符号右移16位，再与原来的值进行异或，得到hash值。这样做的原因是为了避免hash冲突。
        -   补充说明：
            -   Integer类的hashCode方法就直接返回传入的值;
            -   String类的hashCode方法，
            -   Boolean类的hashCode方法，如果为true则返回1231，false返回1237(为什么？)。
        ```java
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
2.  创建一个HashMap对象的时候
    1.  当调用空参构造的的时候，底层会创建一个空数组。
    2.  当传入一个整型的时候，底层会根据这个值创建一个最接近它的2的指数次幂的数值的数组(使用的是HashMap中的静态方法`tableSizeFor`)。比如传入13，就会变成16。
    ```java
    HashMap<Integer, Integer> map = new HashMap<>();
    HashMap<Integer, Integer> map = new HashMap<>(13);
    
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
3.  向map中添加元素
    1.  根据hash算法计算key的hash值。
    2.  然后根据(n-1) & hash的算法(jdk1.8.0_191)得到在Node数组中存放位置的索引，n为Node数组的长度，之前解释了n一定是2的指数次幂。通过这种算法可以保证计算出来的值不会超出Node数组的大小。
        -   例如： n=16：0001 0000，hash值：1011 1101；
        n-1=15:0000 1111;两个相与高位肯定为0，可以将计算出来的数值控制在数组索引内。
        -   这样的话hash值为：0110 1101和1101 1101，只要低四位相同，那么在底层Node数组中的索引位置就会相同，这既是hash冲突。
        -   但是，由于通过hash方法计算key的hash值的时候，在根据key所属类的hashCode方法计算出hashCode值后，还将其无符号后移16位，再与其自身异或，最终得到一个高16位为0的hash值。

    3.  判断Node数组在上一步计算得出的索引位置上的Node变量是不是为空。
        -   如果为空，代表这个桶里面还没有数据，调用`tab[i] = newNode(hash, key, value, null);`添加新元素成功。//0
        -   如果不为空，代表这个桶里面装了数据了(此时发生了hash冲突)。判断当前索引位置上的Node对象的hash值与待插入元素的hash值相不相等，并且它们的key值相不相等?
            -   如果条件都满足，则将新元素直接替换掉旧元素，也就是根据key值修改value值(map中key不允许重复)。//1
            -   如果不满足条件(不考虑红黑树)，表明新插入一个key-value键值对。判断当前索引位置元素的下一个元素是不是为null？
                -   如果是null，则直接将当前索引位置元素的next指针指向新插入的元素，添加成功。//2
                -   如果不为null，则判断这个位置的元素的hash值与待插入的元素的hash值相不相等，并且两个元素的key值是否相等。
                    -   不满足条件，则指向下下个元素，进行下一轮判断。//3
                    -   满足条件，则将更新value值。//4
        ```java
        /**
         * Implements Map.put and related methods
         *
         * @param hash hash for key
         * @param key the key
         * @param value the value to put
         * @param onlyIfAbsent if true, don't change existing value
         * @param evict if false, the table is in creation mode.
         * @return previous value, or null if none
         */
        final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                       boolean evict) {
            Node<K,V>[] tab; Node<K,V> p; int n, i;
            if ((tab = table) == null || (n = tab.length) == 0)
                n = (tab = resize()).length;
            if ((p = tab[i = (n - 1) & hash]) == null)
                tab[i] = newNode(hash, key, value, null);//0
            else {
                Node<K,V> e; K k;
                if (p.hash == hash &&
                    ((k = p.key) == key || (key != null && key.equals(k))))
                    e = p;//1
                else if (p instanceof TreeNode)
                    e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
                else {
                    for (int binCount = 0; ; ++binCount) {
                        if ((e = p.next) == null) {
                            p.next = newNode(hash, key, value, null);//2
                            if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                                treeifyBin(tab, hash);
                            break;
                        }
                        if (e.hash == hash &&
                            ((k = e.key) == key || (key != null && key.equals(k))))
                            break;
                        p = e;//3
                    }
                }
                if (e != null) { // existing mapping for key
                    V oldValue = e.value;
                    if (!onlyIfAbsent || oldValue == null)
                        e.value = value;
                    afterNodeAccess(e);
                    return oldValue;//4
                }
            }
            ++modCount;
            if (++size > threshold)
                resize();
            afterNodeInsertion(evict);
            return null;
        }
    
        ```