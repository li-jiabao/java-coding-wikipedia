## 自定义hashcode和equals方法实现元素值相同，元素相同的情况

```java
class Reference {
    int[] values;
    
    public Reference(int[] values) {
        this.values = values;
    }
    
    @Override
    public int hashCode() {
        int hash = 0;
        for (int i:values) {
            hash = (hash+i)*i;
        }
        return hash;
    }
    
    @Override
    public boolean equals(Object obj) {
        Reference ref = (Reference) obj;
        if (values.length!=ref.values.length) return false;
        for (int i=0;i<values.length;i++) {
            if (values[i]!=ref.values) return false;
        }
        
    }
}
```

