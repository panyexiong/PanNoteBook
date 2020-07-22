# Set接口

- Set集合不允许包含相同的元素
  - 不可重复性：保证添加的元素按照equals方法判断时，不能返回true。即相同的元素只能添加一个。
- Set集合存储数据是无序的
  - 无序性不代表随机性。
  - 以HashSet说明：存储的数据在底层数组中并非按照数组索引顺序添加。根据添加数据的HashCode哈希值添加的。
- Set判断两个对象是否相等使用equals（）方法，而不是==运算符。

## HashSet

- 作为Set接口的主要实现类；
- 线程不安全
- 可以存储null值
- 向set中添加数据时，其所在类一定要重写hashCode和equals方法
- 重写的hashCode和equals方法，尽可能保持一致性：相等的对象必须具有相等的散列码（hash值）

### hashCode和equals方法的重写

```java
    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (o == null || getClass() != o.getClass()) {
            return false;
        }
        User user = (User) o;
        return age == user.age &&
                Objects.equals(name, user.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }

    public static int hash(Object... values) {
        return Arrays.hashCode(values);
    }

    public static int hashCode(Object a[]) {
        if (a == null)
            return 0;

        int result = 1;

        for (Object element : a)
            result = 31 * result + (element == null ? 0 : element.hashCode());

        return result;
    }
```

31:

- 选择系数的时候要选择尽量大的系数，因为如果计算出来的hash地址越大，所谓冲突就越少，查找起来的效率也会提高。
- 并且31只占用了5bits，相乘造成数据溢出的概率较小
- i*31可以由i<<5-1表示,现在许多虚拟机里面都有做相关优化
- 31是一个素数，素数的作用就是如果我用一个数字来乘以这个素数，那么最终出来的结果只能被素数和本身还有1来整除，减少哈希冲突。

## LinkedHashSet

- 是HashSet的一个子类，继承于HashSet
- 在添加数据的同时，每个数据还维护了两个引用，记录此数据的前一个数据和后一个数据。
  - 优点：对于频繁的遍历操作，效率高于HashSet

## TreeSet

- 使用红黑树，查询速度比List快
- 要求放入TreeSet中的对象必须是同一个类new的对象。
- 可以按照添加对象的指定属性，进行排序。

### 自然排序

自然排序中，比较两个对象是否相同的标准为：CompareTo（）返回0，不再是equals（）。

```java
    /**
     * 自然排序
     * 按照姓名排序,姓名相同，比较年龄
     * @param o
     * @return
     */
    @Override
    public int compareTo(User o) {
        int compare = this.name.compareTo(o.name);
        if (compare!=0){
            return compare;
        }else{
            return Integer.compare(this.age,o.age);
        }
    }
```

### 定制排序

定制排序中，比较两个对象是否相同的标准为compare（）返回0，不再是equals（）。

```java
    @Test
    public void test02(){
        Comparator<User> comparator = new Comparator<User>() {
            @Override
            public int compare(User o1, User o2) {
                return o1.getName().compareTo(o2.getName());
            }
        };
        Set<User> set = new TreeSet<>(comparator);

        set.add(new User("pan",23));
        set.add(new User("zhang",19));
        set.add(new User("Tom",21));
        set.add(new User("zhang",20));

        Iterator<User> iterator = set.iterator();
        while (iterator.hasNext()){
            System.out.println(iterator.next());
        }
    }
```

## 面试题

```java
    @Test
    public void test02(){
        HashSet set =new HashSet();
        Person p1 =new Person(1001,"AA");
        Person p2 =new Person(1002,"BB");

        set.add(p1);
        set.add(p2);
        System.out.println(set);

        p1.name = "CC";
        //由于p1的name修改了，所以根据现在p1的属性 id=1001，name=“CC”计算的hash值找的不是到原来的索引位置，现在的位置上大概率为null，所以删除失败。
        boolean remove = set.remove(p1);
        System.out.println(remove);
        System.out.println(set);

        //这一步添加对象时，根据id=1001,name="CC"计算的hash值，不等于原来的id=1001,name="AA"的hash值，所以两个对象不会放在同一个地址，不会覆盖。
        set.add(new Person(1001,"CC"));
        System.out.println(set);
        //这一步根据id=1001,name="AA"计算的hash值,等于原来的根据id=1001,name="AA"的hash值，但是此时两个对象中的属性值不一样，前者对象的的name="CC"，此时的新添加的对象的name="AA",所以不会覆盖。
        set.add(new Person(1001,"AA"));
        System.out.println(set);
    }

//out
[Person{age=1002, name='BB'}, Person{age=1001, name='AA'}]
false
[Person{age=1002, name='BB'}, Person{age=1001, name='CC'}]
[Person{age=1002, name='BB'}, Person{age=1001, name='CC'}, Person{age=1001, name='CC'}]
[Person{age=1002, name='BB'}, Person{age=1001, name='CC'}, Person{age=1001, name='CC'}, Person{age=1001, name='AA'}]
```



