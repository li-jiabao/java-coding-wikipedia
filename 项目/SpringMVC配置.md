# SpringMVC配置



对于空业务处理的页面跳转，可以直接使用SpringMVC里面的WebMvcConfigurer接口实现，然后使用addViewControllers方法实现自定义的一些页面跳转

```java
package com.jiabao.jbmall.auth.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

/**
 * @Author: JiabaoLi
 * @Email: 905001136@qq.com
 * @CreatedDate: 2021/12/13
 */

@Configuration
public class JbmallAuthWebConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        // 设置对应路径下的视图解析器，也就是html页面的模板名
        // 就可以不用在controller层创建空业务逻辑的视图解析
        registry.addViewController("/login.html").setViewName("login");
        registry.addViewController("/registry.html").setViewName("registry");
    }
}

```

