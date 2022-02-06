# Java流

这个流操作在Java8之后加入的特性，在spring项目的开发中，这个流的操作是非常常见的，因此需要对流的操作非常熟悉

流操作一般是针对那些集合（容器）对象所进行一系列转换过滤函数映射执行等等操作

```java
List<CategoryEntity> children = entities.stream().
    filter(entity -> entity.getParentCid() == root.getCatId()).
    map((entity) -> {
        entity.setChildren(getChildren(entity, entities));
        return entity;
    }).
    sorted((entity1,entity2) -> 
           (entity1.getSort()==null?0:entity1.getSort())-
           (entity2.getSort()==null?0:entity2.getSort())).
    collect(Collectors.toList());

// 下面是流的一些常用操作


// stream方法是将集合容器对象转换为一个流对象，然后就可以针对这个流对象进行一系列的转换过滤等等操作

// fillter是针对你的返回值来进行过滤，true就将元素筛选出来进行后续的一些操作

// map是将所有的元素执行某个函数操作进行相应的操作处理，并返回一个对象

// sorted是对所有的元素进行一个排序操作

// collect是将所有的元素筛选出来并返回

// forEach：针对所有的元素进行某个操作，一般用来执行遍历操作

// allMatch：所有元素匹配才返回真

// anyMatch：只要有一个匹配就返回真

// noneMatch：没有匹配

// reduce：执行累加累减等等，对于相邻元素

// distinct：去重
 
// limit：限制输出的数量

// count统计数量

// max、min：返回最大最小值，自定义比较器

// skip：跳过指定数量的元素选择

// findAny、findFirst

// mapToInt、mapToDouble、mapToLong：数字的转换，转换之后便可以使用数学的统计函数

// average、max、min
```

并行流执行：多个线程并行执行，加快执行速度的一种流方法

```java
list.parallelStream().;

// 并行流存在的问题，最终的顺序不一致，这是由于线程执行的随机性所决定的
// 因此并行流的使用适合那些不要求执行顺序，不要求排序的一些场景，用来

double average = roster
    .parallelStream()
    .filter(p -> p.getGender() == Person.Sex.MALE)
    .mapToInt(Person::getAge)
    .average()
    .getAsDouble();
```

