# JDBC使用

## MySQL

MySQL在Windows服务的关闭和重启：

```powershell
net stop mysql  # 关闭MySQL服务
net start mysql  # 开启MySQL服务
```

MySQL的时区设置：

临时修改：在MySQL命令行中输入`set time_zone='+08:00'`，这个操作MySQL关闭之后就失效

永久修改，在配置文件的[mysqld]项相面添加一项：`default-time-zone='+08:00'`，修改配置文件之后，重启MySQL生效

查看时区设置：`show variables like '%time_zone';`，如果显示SYSTEM说明并未对时区进行修改设置



## IDEA

对于IDEA的控制台乱码问题，在VM option设置项中添加`-Dfile.encoding=UTF-8`设置编码为utf-8



## JDBC

### JDBC驱动安装

1. 首先需要安装数据库的驱动，也就是你需要使用的数据库

2. 根据你使用的数据库去找寻JDBC的驱动，比如MySQL的驱动就是connector/j，需要去数据库的官网下载：https://dev.mysql.com/downloads/connector/j/

3. 下载好之后，驱动的使用方法：进入Eclipse或者IDEA，在项目文件中新建lib文件夹，将驱动的jar包放入到文件夹，并将这个路径加入到library。或者使用maven的话，直接将依赖项添加到pom.xml

   ```xml
   <!-- MySQL的JDBC数据库驱动 -->
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <version>8.0.19</version>
   </dependency>
   ```

### 简单连接使用

1. jdbc驱动加载：`Class.forName('com.mysql.cj.jdbc.driver');`
2. 使用DriverManager获取数据库连接`Connection conn = DriverManager.getConnection(url, user, password);`
3. 利用连接创建statement对象：`Statement stat = conn.createStatement();`
4. 利用statement对象执行数据库操作：`ResultSet result = stat.executeQuery(sql);`
5. 结果处理，结果防止在一个结果集对象中，可以利用迭代器来获取所有结果，`resutl.next();`获取结果
6. 关闭连接，将result，stat和conn都关闭

### 数据库连接

#### MySQL

驱动class：`com.mysql.cj.jdbc.Driver`或者`com.mysql.jdbc.Driver`

连接语句：`jdbs:mysql://localhost:3306/database-name`

增加配置项的连接语句：`"jdbc:mysql://localhost:3306/db_admin?serverTimezone=Hongkong&useUnicode=true&characterEncoding=utf8&useSSL=false"`

#### Oracle数据库

驱动class：`oracle.jdbc.driver.OracleDriver`

连接语句url：`jdbc:oracle:thin:@127.0.0.1:1521:db-name`

#### PostgreSQL数据库

驱动class：`org.postgresql.Driver`

连接语句url：`jdbc.postgresql://localhost/db-name`

#### SQLServer2000数据库

驱动class：`com.microsoft.jdbc.sqlserver.SQLSeverDriver`

连接语句url：`jdbc.microsoft.sqlserver://localhost:1433;DatabaseName=db-name`

SQLServer2005数据库

驱动class：`com.microsoft.sqlserver.jdbc.SQLServerDriver`

连接语句url：`jdbc.sqlserver://localhost:1433;DatabaseName=db-name`

#### DB2数据库

驱动class：`com.ibm.db2.jcc.DB2Driver`

连接语句url：`jdbc.db2://127.0.0.1:50000/db-name`



## 使用前的准备工作

### 安装数据库

找到自己需要使用数据库，亦或是自己比较熟悉的关系型数据库，常用的数据库：MySQL、Oracle、微软的SQLServer、DB2和PostgreSQL

去各自的官网依照安装向导将其安装好，并测试是否已经安装好可以使用

### 安装数据库对应的JDBC驱动

MySQL对应的JDBC驱动下载地址：[MySQL :: Download Connector/J](https://dev.mysql.com/downloads/connector/j/#:~:text= MySQL Connector%2FJ is the official JDBC driver,use with MySQL Server 8.0%2C 5.7 and 5.6.)

Oracle对应JDBC驱动下载：[Central Repository: com/oracle/database/jdbc/ojdbc8 (maven.org)](https://repo1.maven.org/maven2/com/oracle/database/jdbc/ojdbc8/)

下载好对应的驱动jar包之后，可以直接将其复制到项目文件下的lib文件夹下面，然后将这个文件add as library，添加至库中

或者在命令行操作时，将jar包加入到classpath中

```powershell
java --classpath .:/directory/ojdbc8-21.1.0.0.jar
```

## 使用步骤

### 1. 注册驱动器

主要有两种方法：

1. 使用`Class.forName()`

   ```java
   // mysql
   Class.forName("com.mysql.cj.jdbc.Driver");
   // oracle
   Class.forNmae("oracle.jdbc.driver.OracleDriver");
   ```

2. 使用`System.setProperty("jdbc.drivers", "com.mysql.cj.jdbc.driver")`
   或者 ：`java -Djdbc.drivers=com.mysql.cj.jdbc.driver:oracle.jdbc.driver.OracleDriver`

### 2. 连接数据库

```java
// jdbc数据库连接
String url = "jdbc:mysql://localhost:3306/test?useUnicode=true&useSSL=true&characterEncoding=utf8";
String user = "root";
String password = "密码";
Connection conn = DriverManager.getConnection(url, user, password);
```

### 3. 获取statement对象

```java
// 获取常规的Statement对象
Statement stat = conn.createStatement();
// statement的几种使用
stat.executeQuery(sql); // 执行查询操作，返回的对象是一个结果集
stat.executeUpdate(sql);  // 执行增删改操作，返回操作影响的行数
stat.execute(sql);  // 所有数据库操作都可执行，第一个执行结果是结果集就返回true，反之返回false，getResultSet()或者getUpdateCount()获取第一个执行结果

// 获取预备语句PrepareStatement
PreparedStatement stat = conn.prepareStatement(querySql);
// 预备语句的使用
// 预备语句就是提前设计好缺口，可以根据程序传入的参数或者设置好的参数执行相应的查询操作
String querySQL = ”select name, studentid, register_date" + 
    " from student" + 
    " where name = ?";   // 其中的?号就是提前预备号的缺口
// 为了使用这些预定的缺口，需要使用set方法将变量绑定到实际的值上来
stat.setString(1, "变量值");   // 第一个参数的数字代表预备变量的编号，第二个参数代表的是变量对应的值
stat.clearParameters();  // 清除设置的变量信息
// setXxx(); 根据变量类型不同选择不同的设置变量方法名，有Int、Double、Date等
ResultSet result = stat.executeQuery();  // 执行预定语句的查询操作并返回结果集
Int affectedRows = stat.executeUpdate();  // 执行更新操作，返回影响的行数
```

### 4. 获取结果集对象/操作影响行数

```java
// 获取查询结果集对象
ResultSet result = stat.executeQuery(sql);   // 返回的是结果集


// 结果集相关的一些操作
result.next();  // 查看下一行数据

// 获取数据有两种传参方式，一种是传递列名，一种是传递列所在的位置
result.getObject();  // 不确定列数据类型的时候使用这个方法获取列内容
result.getXxx();  // 其中的Xxx可以是Int、Double、Date、String等数据类型
result.findColumn(String columnName); // 根据指定的列名获取列的序号
result.updateObject();  // 返回或者更新列的值，并将值转换成指定的类型
```

### 5. 关闭连接

connection、resultset和statement对象属于资源对象，在使用结束之后需要关闭

```java
// 手动关闭执行close方法即可
result.close();
stat.close();
conn.close();

// 自动关闭，利用try-resource资源语句。自动关闭释放资源
try( Connection conn = DriverManager.getConnection();)
{
    ....
}
```



## Connection、ResultSet、Statement管理

每一个connection对象可以创建一个或者多个Statement对象

同一个Statement对象可以用于多个不想管的命令和查询，但是每个Statement对象只能有一个打开的ResultSet对象

如果需要执行多个查询操作并且都需要查看结果，需要创建多个Statement对象来执行操作

<font color='red'>实际上，并不需要同时处理多个结果集，如果结果集相互关联，可以先使用联表查询语句将结果查询到一个表中，而且执行一个组合查询的效率比多个结果集要更高效</font>

这三个对象都属于资源对象。因此，在使用完后就需要手动关闭，或者利用try-resource语句来达成自动关闭资源的操作

## 查询操作

### 读写LOB

除了常规的数据类型之外，许多的数据库还可以存储大对象，例如图片或者其他数据

BLOB：二进制大对象大

CLOB：字符型大对象

大对象的读取都是通过结果集中的方法来进行的

```java
// 读取二进制大对象
Blob blob= result.getBlob();  // 也是两种方法，一种传入列序号，一种传入列标签
// blob对象下面有getBinaryStream()读取数据，setBinaryStream()创建写入数据，getBytes()获取指定范围数据

// 利用connection对象可以创建Blob和Clob对象
conn.createBlob();
conn.cretaeClob();

// 读取字符型大对象
Clob clob = result。getClob();  // 合上面的二进制大对象类似的传参数方法
// clob对象下有getCharacterStream()和setCharacterStream()，还有一个getSubString()获取子字符串
```

### 查询语句中的SQL转义

日期格式中，需要使用d、t、ts来表示DATE、TIME和TIMESTAMP值：

```java
{d '2008-01-24'}
{t '12:25:59'}
{dt '2008-01-24 12:25:59.999'}
```

标量函数指仅返回单个值的函数。JDBC规范提供了标准的名字并将其转译为数据库相关的名字，下面是调用函数的方式：

```java
{fn left(?, 20)}
{fn user()}
```

数据库中的存储过程的调用，在JDBC需要使用call转义命令，存储过程没有参数的时候可以不加括号，使用=来捕获存储过程返回的值：

```java
{call PROC1(?, ?)}
{call PROC2}
{call ? = PROC3(?)}
```

对于联表查询的语句需要使用转义语法

```
"select * from {oj student left outer join subject on student.studentid=subject.studentid}"
// 需要在oj后面写联表查询的语句
```

最后一种转义场景就是like语句的通配符，_和%，表示匹配一个和匹配一个或多个的字符通配符，为了匹配原来的符号，需要使用下面的结构来匹配：

```java
"where ? like %!_% {escape '!'}";
// 其中的{escape '!'}设置指定的符号为转义符号
```

### 多结果集

执行存储过程，或者在使用允许在单个查询中提交多个select语句的数据库时，一个查询中有可能返回多个结果集，湖泊去结果集的步骤：

1. 使用execute方法来执行SQL语句
2. 获取第一个结果集或更新计数
3. 重复调用getMoreResults方法以移动到下一个结果集
4. 不存在更多的结果集或更新计数时，完成操作

多结果集构成的链中的下一项是结果集，execute和getMoreResults方法返回true，如果链中的下一项不是更新计数，getUPdateCount方法返回-1

```java
// 遍历多结果集的代码结构
Boolean isResult = stat.execute(command);
Boolean done = false;
while (!done) {
    // 判断是否是结果集
    if (isResult) {
        ResultSet result = stat.getResultSet();
        ...
    }
    // 不是就判断是不是更新计数，如果不是更新计数说明已经到达结果集末尾，结束
    else {
        int count = stat.getUpdateCount();
        if (count >= 0)
            ...  // 是更新计数，进行相应的处理即可
        else
            done = true;
    }
    // 执行获取下一个结果集的方法
    if (!done) isResult = stat.getMoreResults();
}
```

### 获取生成的键

数据库都支持一种自动编号的机制，JDBC提供了获取自动生成键的方式，但是没有提供生成该类键的方法

```java
stat.executeUpdate(sql, Statement.RETURN_GENERATED_KEYS); //这样设置之后，并且语句是插入语句，则第一列就是自动生成的键 
ResultSet result= stat.getGeneratedKeys();
while (result.next())
{
	int key = result.getInt(1);  // 获取自动生成键的值
}
```

### 可滚动的结果集

默认情况下，结果集是不可滚动和不可更新，为了从查询中获取可滚动的结果集，需要使用不同的方法获取statement

```java
Statement stat = conn.createStatement(type, concurrency);

// 预备语句
PreparedStatement stat = conn.prepareStatement(sql, type, concurrency);
```

| type值                  | 解释                                   |
| ----------------------- | -------------------------------------- |
| TYPE_FORWARD_ONLY       | 结果集不能滚动（默认值）               |
| TYPE_SCROLL_INSENSITIVE | 结果集可以滚动，但是对数据库变化不敏感 |
| TYPE_SCROLL_SENSITIVE   | 结果集可以滚动且对数据库变化敏感       |

| concurrency值    | 解释                               |
| ---------------- | ---------------------------------- |
| CONCUR_READ_ONLY | 结果集不能用于更新数据库（默认值） |
| CONCUR_UPDATABLE | 结果集可以用于更新数据库           |



```java
// 结果集的滚动
rs.relatice(n);  // 向前或向后移动多行，n为正数，前滚，负数，后滚
rs.previous();  // 后滚
rs.next();  // 前滚
rs.absolute(num)l;  // 滚动到指定行上
int currentRow = rs.getRow(); // 获取当前行号
first();  // 移动到第一行
last();  // 移动到最后一行
befareFirst();  // 第一行之前
afterLast();  // 最后一行之后
isFirst();isLast();  // 测试是否第一行或者最后一行
isBefareFirst();  // 第一行之前
isAfterLast();  // 最后一行之后
```

### 可更新的结果集

指定concurrency参数类型为CONCURRENCY_UPDATABLR，表示使用这个语句获取的结果集是可更新的

```java
// 可更新的结果集
Statement stat = conn.createStatement(TYPE_SCROLL_INSESITIVE, CONCUR_UPATEABLE);
// 或者使用preparestatement语句
PreparedStatement stat = conn.prepareStatement(type, concurrency);

// 更新结果
stat.updateXxx("列标签，或者使用列标号", "更新的值");
stat.updateRow();  // 执行完更新操作之后进行这个，将更改写入数据库
// 插入结果
stat.moveToInsertRow();
stat.updateStrinstat("COF_NAME", coffeeName);
stat.updateInt("SUP_ID", supplierID);
stat.updateFloat("PRICE", price);
stat.updateInt("SALES", sales);
stat.updateInt("TOTAL", total);

stat.insertRow();   // 执行插入之后需要使用这个，将数据更新到stat和数据库中
stat.beforeFirst();  // 将游标指针返回第一行之前的位置


// 想要批量操作不自动提交，需要关闭自动提交
conn.setAutoCommit(false);
// 批量更新操作
stat.addBatch("insert into student values(1, 'hhh','20219-05-03','20290012')");
stat.addBatch("insert into student values(2, 'hhh','20219-05-03','20290012')");
stat.addBatch("insert into student values(3, 'hhh','20219-05-03','20290012')");
stat.addBatch("insert into student values(4, 'hhh','20219-05-03','20290012')");
stat.executeBatch();  // 执行批量操作
conn.commit();   // 提交操作更新数据库
```

## 行集

比结果集更容易方便使用的一种数据集合，所有的行集对象都实现了结果集接口，并且行集多了另外两个特性：

- 可以作为JavaBeans的组件
- 增加了可滚动操作和可更新操作，有些数据库不支持更新或者滚动，因此想要这些操作，可以使用行集

## 元数据

通过`DatabaseMetadata meta = conn.getMetaDate();`获取数据库连接的元数据，元数据包含了数据库的表名列名等基础信息

通过`ResultSetMeta meta = result.getMetaData();`使用这个方法来获取结果集的元数据，包含了列名列数量等信息

## 事务

当你需要一系列操作全部执行成功的情况下才提交更改数据库操作，这种时候就需要使用事务来达成这个目的

### 基本事务命令

JDBC关闭事务自动提交：`conn.setAutoCommit(false)`

JDBC开启自动提交：`conn.setAutoCommit(true)`

手动提交数据库更改：`conn.commit()`

手动回滚数据库操作：`conn.rollback()`

创建保存点：`conn.setSavepoint()`

释放保存点：`conn.releaseSavepoint()`

回滚到保存点：`conn.rollback(savepoint)`

查看事务隔离级别：`conn.getTransactionIsolation()`

设置事务隔离级别：`conn.setTransactionIsolation(isolation_level)`

### 事务隔离级别

| 事务的隔离级别               | 隔离级别介绍                             |
| ---------------------------- | ---------------------------------------- |
| TRANSACTION_NONE             | 不支持事务隔离                           |
| TRANSACTION_READ_COMMITTED   | 支持，可以避免脏读                       |
| TRANSACTION_READ_UNCOMMITTED | 支持事务隔离，会出现脏读不可重复读和幻读 |
| TRANSACTION_REPEATED_READ    | 支持，避免脏读和不可重复读               |
| TRANSACTION_SERIALIZABLE     | 支持，避免脏读，不可重复读和幻读         |

不可重复读：事务A先读取一行，然后事务B更新数据，事务A再读取同一行，两次读取数据不一致

幻读：执行两次查询操作，获取的行数量不一致，发生在两个事务交互处理的时候

脏读：当事务A读取到了事务B还未提交的数据，出现脏读

获取事务的隔离级别：`conn.getTransactionIsolation()`

修改事务的隔离级别：`conn.setTransactionIsolation()`

### 保存点

在整个事务操作过程中的不同位置设置的一种类似标记的对象，利用这个对象可以让你的操作回滚到这个标记点所在的位置，保存点之前的内容都会被提交到数据库

```java
SavePoint point1 = conn.setSavepoint();  // 设置保存点
SavePoint point2 = conn.setSavepoint("point-label");  // 设置带标签名的保存点
conn.releaseSavapoint(point1);  // 释放保存点
conn.rollback(point1);  // 回滚到指定的保存点位置
```

### 使用回滚的场景

当尝试使用数据库操作的时候，如果出现了SQLException，这个时候就需要使用rollback()


