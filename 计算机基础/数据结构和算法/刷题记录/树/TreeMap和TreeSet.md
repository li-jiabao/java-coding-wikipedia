# TreeMap和TreeSet的特点

插入删除查找O(logn)

`ceiling(E e)`返回数据结构中大于或等于给定元素的元素，如果没有这样的元素，返回null

`floor(E e)`返回数据中小于等于指定元素的元素，如果没有返回null

`add(E e )`添加元素，对于set结构，如果元素存在，不会添加







<font color='red'>对于滑动窗口在滑动过程中保证删除最左边元素的方法就是记录索引，然后删除指定索引对应值的位置元素即可</font>



```java
class Solution 
{
    public boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) 
    {
        int n = nums.length;

        TreeSet<Long> ts = new TreeSet<>();

        int r = 0;
        while (r < n)
        {
            Long ge = ts.ceiling((long)nums[r] - t);
            if (ge != null && ge <= (long)nums[r] + t)
                return true;
            
            ts.add((long)nums[r]);

            if (k <= r)
            {
                // 删除左边元素的方法，利用索引，找到对应位置的值，然后按值删除元素即可
                // 适合解决数组和map、set结合使用的场景
                int l = r - k;
                ts.remove((long)nums[l]);
            }

            r ++;
        }

        return false;
    }
}
```



## 日程表

```java
class MyCalendar {
    private TreeMap<Integer,Integer> map;
    public MyCalendar() {
        map = new TreeMap<>();
    }
    
    public boolean book(int start, int end) {
        Integer max = map.ceilingKey(start);
        if (max!=null && max<end) return false;
        Integer min = map.floorKey(start);
        if (min!=null && map.get(min)>start) return false;
        map.put(start,end);
        return true;
    }
}

/**
 * Your MyCalendar object will be instantiated and called as such:
 * MyCalendar obj = new MyCalendar();
 * boolean param_1 = obj.book(start,end);
 */
```

