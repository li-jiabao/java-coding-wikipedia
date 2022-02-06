# Redis压缩列表

```
<zlbytes><zltail><zllen><entry><entry><elend>
<列表总长度><指向末尾元素><元素的个数><entry元素内容，记录了前一个entry长度和当前的长度><><0xFF,ziplist定界符>
```

## 压缩列表和LinkedList的内存对比

redis的压缩列表实现中需要记录前一个entry的长度，用来访问上一个节点，记录当前entry的长度，用来方便访问下一个节点。通过两个entry的记录来实现了双向链表的访问机制。

对于前一个entry的长度来说，只要不超过255都只占用一个byte，如果超过了255就会占用5bytes。

相比linkedlist使用多个额外的指针，ziplist并没有这些指针内存的消耗，因此相比linkedlist会节省很多的内存使用

## 性能对比

因为ziplist的数组并不标准，因此查找速度基本上也是O(n)，而设计到删除操作的时候，涉及到内存的重新分配问题，效率一般很低。

这是为什么很多数据类型的实现虽然由ziplist的实现，但那些都是建议在键值不大的情况下使用。当数据量一大，ziplist的性能和linkedlist就相差很多了，因此会改用其他的数据结构

