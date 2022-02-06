# bitmap位图

是一种特殊的数据结构，可以用来快速的判断某个元素是否存在，位图只用了一位也就是1bit'来表示某个数是否存在，这可以很大的程度上减少空间的使用达到空间节约的效果，一般这种数据结构可以用在海量数据的去重排序等等

## QQ号去重

假设你有40亿的qq需要进行去重，判断这么多的qq号里面是不是存在重复QQ，并按照QQ号的大小进行排序，如果对于排序来说，利用位图不能处理重复元素。

40多亿相当于只需要使用40多亿位的0和1来表示元素是否存在，也就是40多亿/8字节的空间存储

```java
public class BitMap {
    int[] map;           // 使用int整形数组来存储表示元素是否存在
    
    public BitMap(int length) {
        map = new int[(length>>5)+1];
    }
    
    public void set(int num,boolean bool) {
        int site = num>>5;      // 找到元素所在的数组元素的位置，除以32来找寻
        int index = num%32;     // 找到元素对应的位置数字所在的位数
        if (bool) {
            map[site] |= (1<<index);       // 元素设置为存在
        }else {
            map[site] &= ~(1<<index);      // 将元素设置为不存在
        }
    }
    
    public boolean find(int num) {
        int site = num>>5;
        int index = num%32;
        return map[site] & (1<<index);
    }
}
```

