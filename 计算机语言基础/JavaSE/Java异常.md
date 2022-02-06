# Java异常

## Java语言标准库中定义的异常

最顶层的：Throwable

然后就是Exception：RuntimeException就是Exception的子类

常见的一些异常以及他们的继承结构：

```
Exception                           异常类，所有异常的父类
│
├─ RuntimeException                 运行时异常，在运行代码时出现的异常
│  │
│  ├─ NullPointerException          空指针异常，实际业务中常遇见的异常
│  │
│  ├─ IndexOutOfBoundsException     越界异常
│  │
│  ├─ SecurityException             安全异常
│  │
│  └─ IllegalArgumentException      非法参数异常
│     │
│     └─ NumberFormatException      格式异常，字符串格式异常等等，常出现在Number.parseInt()
│
├─ IOException                      IO异常，文件读写和网络层传输时遇见的异常等
│  │
│  ├─ UnsupportedCharsetException   不支持的字符集异常
│  │
│  ├─ FileNotFoundException         文件找不到异常
│  │
│  └─ SocketException               Socket网络传输异常
│
├─ ParseException                   
│
├─ GeneralSecurityException
│
├─ SQLException                      数据库交互异常
│
└─ TimeoutException                  超时异常
```

一般情况下，我们使用JDK中定义好的一些异常即可，比如参数不合法，抛出`IllegalArgumentException`，文件的读写异常抛出`IOException`

```java
static void process1(int age) {
    if (age <= 0) {
        throw new IllegalArgumentException();
    }
}
```



## 项目中的异常

可以自定义新的异常类型，只是需要注意保证一个合理的异常继承体系很重要。

一般实际的项目中的异常常见做法：

- 定义一个根异常`BaseException`，然后从这个异常去派生出各种业务类型的异常
- 一般根异常需要从合适的Exception派生出来，一般都使用`RuntimeException`作为根异常的父类

```java
public class BaseException extends RuntimeException {
    // 空构造函数
    public BaseException() {
        super();
    }
    
    // 传入异常信息和造成此异常的Throwable
    public BaseException(String message,Throwable cause) {
        super(message,cause);
    }
    
    // 传入异常信息创建异常类
    public BaseException(String message) {
        super(message);
    }
    
    // 传入造成此异常的异常
    public BaseException(Throwable cause) {
        super(cause);
    }
}
```

```java
package run.halo.app.exception;

import org.springframework.http.HttpStatus;
import org.springframework.lang.NonNull;
import org.springframework.lang.Nullable;

/**
 * 实际业务中的根异常
 */
public abstract class AbstractHaloException extends RuntimeException {

    /**
     * 错误的数据包装类，存储错误信息
     */
    private Object errorData;

    public AbstractHaloException(String message) {
        super(message);
    }

    public AbstractHaloException(String message, Throwable cause) {
        super(message, cause);
    }

    /**
     * Http status code
     *
     * @return {@link HttpStatus}
     */
    @NonNull
    public abstract HttpStatus getStatus();

    @Nullable
    public Object getErrorData() {
        return errorData;
    }

    /**
     * Sets error errorData.
     *
     * @param errorData error data
     * @return current exception.
     */
    @NonNull
    public AbstractHaloException setErrorData(@Nullable Object errorData) {
        this.errorData = errorData;
        return this;
    }
}

```



## 派生异常

```java
public class LoginFailureException {
    // 自定义默认的异常消息
    private static final String EXCEPTION_MESSAGE="用户登录失败异常，用户名或密码错误";
    
   // 默认无参构造器传入默认的异常消息
    public LoginFailureException() {
        this(EXCEPTION_MESSAGE);
    }
    
    public LoginFailureException(String message) {
        super(message);
    }
}
```



## Objects

```java
public static void getName(String name) {
    // 这个方法用来限定某些参数不能为空，为空就会抛出异常
    Objects.require(name,"传入的参数必须不为空");
}
```

## Java日志

### Log4j

配置文件，文件名：`log4j2.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
	<Properties>
        <!-- 定义日志格式 -->
		<Property name="log.pattern">%d{MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36}%n%msg%n%n</Property>
        <!-- 定义文件名变量 -->
		<Property name="file.err.filename">log/err.log</Property>
		<Property name="file.err.pattern">log/err.%i.log.gz</Property>
	</Properties>
    <!-- 定义Appender，即目的地 -->
	<Appenders>
        <!-- 定义输出到屏幕 -->
		<Console name="console" target="SYSTEM_OUT">
            <!-- 日志格式引用上面定义的log.pattern -->
			<PatternLayout pattern="${log.pattern}" />
		</Console>
        <!-- 定义输出到文件,文件名引用上面定义的file.err.filename -->
		<RollingFile name="err" bufferedIO="true" fileName="${file.err.filename}" filePattern="${file.err.pattern}">
			<PatternLayout pattern="${log.pattern}" />
			<Policies>
                <!-- 根据文件大小自动切割日志 -->
				<SizeBasedTriggeringPolicy size="1 MB" />
			</Policies>
            <!-- 保留最近10份 -->
			<DefaultRolloverStrategy max="10" />
		</RollingFile>
	</Appenders>
	<Loggers>
		<Root level="info">
            <!-- 对info级别的日志，输出到console -->
			<AppenderRef ref="console" level="info" />
            <!-- 对error级别的日志，输出到err，即上面定义的RollingFile -->
			<AppenderRef ref="err" level="error" />
		</Root>
	</Loggers>
</Configuration>
```

### Slf4j

配置文件，文件名：`logback.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>

	<appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
		<encoder>
			<pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
		</encoder>
	</appender>

	<appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<encoder>
			<pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
			<charset>utf-8</charset>
		</encoder>
		<file>log/output.log</file>
		<rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
			<fileNamePattern>log/output.log.%i</fileNamePattern>
		</rollingPolicy>
		<triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
			<MaxFileSize>1MB</MaxFileSize>
		</triggeringPolicy>
	</appender>

	<root level="INFO">
		<appender-ref ref="CONSOLE" />
		<appender-ref ref="FILE" />
	</root>
</configuration>
```

