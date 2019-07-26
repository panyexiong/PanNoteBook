# Java集合

## 三个接口的总结

1. List、Set、Map都是接口；List、Set继承自Collection接口，Map为独立接口。
2. List接口有三个实现类：LinkedList、ArrayList、Vector。
3. Set接口有两个实现类：HashSet（底层由HashMap实现）、LinkedHashSet。
4. Map接口有三个实现类：HashMap、HashTable、LinkedHashMap
5. List特点：元素有放入顺序，元素可重复
6. Set特点：元素无放入顺序，元素不可重复（注意：元素虽然无放入顺序，但是元素再Set中的位置是由该元素的HashCode决定的，其位置其实是固定的。）
7. Map特点：元素按键值对存储，无放入顺序。（Map集合存储键值对，且要求保持键的唯一性。）

## ArrayList和LinkedList区别

- 因为Array是基于索引的数据结构，它使用索引再数组中搜索和读取数据是很快的。Array获取数据的时间复杂度是O（1），但是要删除数据却是很大的开销，因为这可能需要重排数组中的所有数据。ArrayList底层数据结构是数组，是一块连续的内存区域。
- LinkedList插入更快，因为LinkedList不需要改变数组大小，也不需要在数组装满时要将所有的数组重新装入一个新的数组中。
- 类似于插入数据，删除数据时，LinkedList也优于ArrayList
- LinkedList需要更多的内存，因为ArrayList的每个索引的位置是实际的数据，而LinkedList中每个节点中存储的是实际数据和前后节点的位置。

