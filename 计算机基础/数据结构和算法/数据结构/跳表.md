# 跳表

```java
public class SkipList {
    private static final float SKIPLIST_P= 0.5f;     // 随机level生成时需要使用的浮点数据
    private static final int MAX_LEVEL = 16;         // 跳表允许的最大索引层数

    private int levelCount = 1;                      // 当前跳表的最大索引层数,初始为1

    private Node head = new Node(MAX_LEVEL,Integer.MIN_VALUE);      // 跳表的底层数据链表的头结点

    class Node {
        private int value;         // 存储链表的值
        private Node next[];       // 索引数组,存储的是该索引层下该节点的下一个节点
        private int level;         // 当前节点拥有的的索引层数

        public Node(int level,int value) {
            next = new Node[level];
            this.level=level;
            this.value=value;
        }

        @Override
        public String toString() {            // print节点时返回值的格式
            StringBuilder str = new StringBuilder();
            str.append("{\n\tvalue: "+this.value+"\n");
            str.append("\tlevel: "+this.level+"\n"+"}");
            return str.toString();
        }
    }
    // 理论来讲，一级索引中元素个数占原始数据的 50%，二级索引中元素个数占 25%，三级索引12.5% ，一直到最顶层。
    // 因为这里每一层的晋升概率是 50%。对于每一个新插入的节点，都需要调用 randomLevel 生成一个合理的层数。
    // 该 randomLevel 方法会随机生成 1~MAX_LEVEL 之间的数，且 ：
    //        50%的概率返回 1
    //        25%的概率返回 2
    //        12.5%的概率返回 3 ...
    // 主要就是让一个链表元素随机晋升
    public int randomLevel() {
        int level = 1;
        /**
        * redis的实现：(random()&0xFFFF) < (ZSKIPLIST_P * 0xFFFF)
        */
        // 巧妙的设计
        while (Math.random() < SKIPLIST_P && level<MAX_LEVEL) {
            level+=1;
        }

        return level;
    }

    public Node find(int value) {
        Node p = head;
        // 从最高层索引开始查找
        for (int i=levelCount-1;i>=0;i--) {
            // 找到小于查找元素值大小的最右边节点
            while (p.next[i]!=null && p.next[i].value<value)
                p=p.next[i];
        }
        // 如果找到的节点的下一个节点不为空且值等于查找元素值，表示查找到了该元素，返回该节点
        if (p.next[0]!=null && p.next[0].value==value) {
            return p.next[0];
        }else {
            return null;                   // 否则表示没有找到
        }
    }

    public void insert(int value) {
        // 生成一个随机的索引，确定当前插入节点的索引层数
        int level = randomLevel();
        Node p = head;
        Node[] help = new Node[level];    // 构建一个辅助数组记录每一层中小于当前数值的最右边节点

        // 从头节点开始遍历查找小于当前节点的最右边节点
        for (int i=level-1;i>=0;i--) {
            while (p.next[i]!=null && p.next[i].value<=value)
                p=p.next[i];
            // 将找到的节点放到辅助数组中
            help[i]=p;
        }

        if (help[0].value==value) return;     // 如果节点已经存在，直接返回
        // 创建一个需要插入的节点
        Node node = new Node(level,value);

        for (int i=0;i<level;i++) {               // 每一层索引都需要更新
            // 将插入的新节点的下一个节点指向当前小于节点的下一个节点
            node.next[i]=help[i].next[i];
            // 将当前小于节点的下一个节点指向新插入的节点
            help[i].next[i]=node;
        }

        // 更新当前跳表的最大索引层数
        if (levelCount<level) levelCount=level;
    }
	
    // 删除跳表元素的方法，元素不存在返回null，存在就返回元素所在节点
    public Node delete(int value) {
        Node p = head;
        // 遍历索引查找
        for (int i=levelCount-1;i>=0;i--) {
            while (p.next[i]!=null && p.next[i].value<value)
                p=p.next[i];
        }
        // 如果下一个节点为空或者下一节点值不等于删除的元素值，说明元素不存在，返回null
        if (p.next[0]==null || p.next[0].value!=value) return null;
        Node res = p.next[0];
        // 删除节点对应的索引
        for (int i=0;i<p.next[0].level;i++) {
            p.next[i]=p.next[i].next[i];
        }
        return res;
    }
    
    // 输出所有的链表元素
    public void printAllNode() {
        Node p = head;
        while (p.next[0]!=null) {
            System.out.print(p.next[0].value+" ");
            p=p.next[0];
        }
        System.out.println();
    }
    
    // 输出指定层索引的节点值
    public void printIndex(int index) {
        if (index<=0||index>levelCount) {
            System.out.println("索引不存在");
            return;
        }
        Node p = head;
        while (p.next[index-1]!=null) {
            System.out.print(p.next[index-1].value+" ");
            p=p.next[index-1];
        }
        System.out.println();
    }
    
    // 获取索引的层数
    public int indexLevel() {
        return levelCount;
    }

    // 测试跳表是否成功创建
    public static void main(String[] args) {
        SkipList list = new SkipList();
        list.insert(1);
        list.insert(3);
        list.insert(4);
        list.insert(6);
        list.insert(7);
        list.insert(-5);
        System.out.println(list.find(7));
    }
}
```

一种特殊的排序链表结构（可以实现近似二分查找的排序链表），为了方便快速的查找，建立多层链表索引（索引中有一个指针指向底层的排序链表），通过索引来降低查找

![image-20211017201202040](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E8%B7%B3%E8%A1%A8%E7%9A%84%E7%BB%93%E6%9E%84.png)

链表是一种顺序结构，访问查找元素的时间复杂度是O(n)

跳表的特点：

- 跳表的索引高度一般是log(n)
- 跳表的查找时间复杂度一般是O(log(n))
- 跳表的删除时间复杂度也是O(log(n))，先查找需要删除的链表节点，此外还需要删除链表节点对应的索引
- 跳表的插入时间复杂度也是O(log(n))，查找元素位置需要花费O(log(n))，此外可能还需要加上建立索引的时间
- 跳表可以实现二分查找的效率
- 跳表由于使用了额外的索引来加速查找的效率，空间复杂度O(n)
- 索引是根据你需要加速查找的方向进行创建即可。比如学生链表，按学号快速查找可以只建立学号的索引，然后索引指向对应的对象位置
- 跳表还有一个优点就是可以范围查找，效率还比较不错，可以对数时间复杂度找出起点

## 基本结构和常用操作

- 查找：
- 插入
- 删除
- 打印
- 获取索引层数
- 需要定义跳表的最大层数，跳表的层数需要根据元素的数量动态调整
- 随机层数（1~MAXLEVEL)的方法（随机的概率1/2返回1，1/4返回2，1/8返回3.。。）返回1不建立索引，返回2建立一级索引

## 跳表需要解决的一些问题

- 索引怎么建立？
- 索引如何维护？当不断插入/删除元素的时候，链表索引如何更新（不更新可能导致某一段的元素过多退化为单链表或者索引老旧不再可以准确查找）？

## 索引建立

在Node节点内部维护一个数组，这个数组的大小是该节点的索引层数

每个数组元素是下一个指针next位置对应的节点

```java
class Node {
    Node[] next;  			// 每个节点索引层i，对应的元素是该层索引中该节点的下一个元素
    int value;				// 节点的值
    int level;              // 节点拥有的索引层数
    
    // 构建节点的构造方法
    public Node(int level,int value) {
        this.level = level;
        this.value = value;
    }
}
```

对于跳表的元素的索引构建，最重要的方法是随机索引的构建

```java
public int randomLevel() {
    int level=1;
    // 0.5的概率选择索引为1，0.25的概率为2，0.125概率为3，一次类推
    // 此外还限制了最大的索引层数
    while ((Math.random()<SKIPLIST_P) && level<MAX_LEVEL)
        level+=1;
    return level;
}
```

