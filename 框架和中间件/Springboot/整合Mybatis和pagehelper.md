# springboot整合mybatis





## Pagehelper

### springboot配置

springboot加入依赖项：

```xml
<!-- https://mvnrepository.com/artifact/com.github.pagehelper/pagehelper-spring-boot-starter -->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.4.1</version>
</dependency>
```

springboot由于具有自动装配，因此帮我省略了不少的配置项，如果不是使用springboot构建项目，还需要手动配置配置文件。

```yaml
# mybatis 相关配置
mybatis:
  #... 其他配置信息
  configuration-properties:
    offsetAsPageNum: true
    rowBoundsWithCount: true
    reasonable: true
  mapper-locations: mybatis/mapper/*.xml
```

### SpringMVC配置

springmvc加入依赖：

```xml
<!-- pageHelper -->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.1.10</version>
</dependency>
```

配置类：mybatis-config.xml

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE configuration
    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-config.dtd">
 
<configuration>
  <plugins>
    <plugin interceptor="com.github.pagehelper.PageInterceptor">
      <!-- 该参数默认为false -->
      <!-- 设置为true时，会将RowBounds第一个参数offset当成pageNum页码使用 -->
      <!-- 和startPage中的pageNum效果一样-->
      <property name="offsetAsPageNum" value="true"/>
      <!-- 该参数默认为false -->
      <!-- 设置为true时，使用RowBounds分页会进行count查询 -->
      <property name="rowBoundsWithCount" value="true"/>
      <!-- 设置为true时，如果pageSize=0或者RowBounds.limit = 0就会查询出全部的结果 -->
      <!-- （相当于没有执行分页查询，但是返回结果仍然是Page类型）-->
      <property name="pageSizeZero" value="true"/>
      <!-- 3.3.0版本可用 - 分页参数合理化，默认false禁用 -->
      <!-- 启用合理化时，如果pageNum<1会查询第一页，如果pageNum>pages会查询最后一页 -->
      <!-- 禁用合理化时，如果pageNum<1或pageNum>pages会返回空数据 -->
      <property name="reasonable" value="true"/>
      <!-- 3.5.0版本可用 - 为了支持startPage(Object params)方法 -->
      <!-- 增加了一个`params`参数来配置参数映射，用于从Map或ServletRequest中取值 -->
      <!-- 可以配置pageNum,pageSize,count,pageSizeZero,reasonable,orderBy,不配置映射的用默认值 -->
      <!-- 不理解该含义的前提下，不要随便复制该配置 -->
      <property name="params" value="pageNum=start;pageSize=limit;"/>
      <!-- 支持通过Mapper接口参数来传递分页参数 -->
      <property name="supportMethodsArguments" value="true"/>
      <!-- always总是返回PageInfo类型,check检查返回类型是否为PageInfo,none返回Page -->
      <property name="returnPageInfo" value="check"/>
    </plugin>
  </plugins>
</configuration>
```



### 具体使用

使用的时候：

- 只需要查询list之前，调佣PageHelper的startPage静态方法设置分页信息，即可使用分页功能

```java
public Object getUsers(int pageNum, int pageSize) {
    PageHelper.startPage(pageNum, pageSize);
    // 不带分页的查询
    List<UserEntity> list = userMapper.selectAllWithPage(null);
    // 可以将结果转换为 Page , 然后获取 count 和其他结果值
    com.github.pagehelper.Page listWithPage = (com.github.pagehelper.Page) list;
    System.out.println("listCnt:" + listWithPage.getTotal());
    return list;
}
```

结合mybatis的dao层进行查询之后的数据的分页展示：将查询到的数据继续进一步进行封装即可。将查询到的数据封装到PageInfo对象内部作为一个PageInfo类返回。

```java
// PageInfo类的字段属性

public class PageInfo<T> extends PageSerializable<T> {
    public static final int DEFAULT_NAVIGATE_PAGES = 8;
    public static final PageInfo EMPTY = new PageInfo(Collections.emptyList(), 0);
    private int pageNum;
    private int pageSize;
    private int size;
    private long startRow;
    private long endRow;
    private int pages;
    private int prePage;
    private int nextPage;
    private boolean isFirstPage;
    private boolean isLastPage;
    private boolean hasPreviousPage;
    private boolean hasNextPage;
    private int navigatePages;
    private int[] navigatepageNums;
    private int navigateFirstPage;
    private int navigateLastPage;
    
    // 构造方法：
    public PageInfo() {
        this.isFirstPage = false;
        this.isLastPage = false;
        this.hasPreviousPage = false;
        this.hasNextPage = false;
    }

    public PageInfo(List<T> list) {
        this(list, 8);
    }

    public PageInfo(List<T> list, int navigatePages) {
        super(list);
        this.isFirstPage = false;
        this.isLastPage = false;
        this.hasPreviousPage = false;
        this.hasNextPage = false;
        if (list instanceof Page) {
            Page page = (Page)list;
            this.pageNum = page.getPageNum();
            this.pageSize = page.getPageSize();
            this.pages = page.getPages();
            this.size = page.size();
            if (this.size == 0) {
                this.startRow = 0L;
                this.endRow = 0L;
            } else {
                this.startRow = page.getStartRow() + 1L;
                this.endRow = this.startRow - 1L + (long)this.size;
            }
        } else if (list instanceof Collection) {
            this.pageNum = 1;
            this.pageSize = list.size();
            this.pages = this.pageSize > 0 ? 1 : 0;
            this.size = list.size();
            this.startRow = 0L;
            this.endRow = list.size() > 0 ? (long)(list.size() - 1) : 0L;
        }

        if (list instanceof Collection) {
            this.calcByNavigatePages(navigatePages);
        }

    }
    
}
```



### 原理

