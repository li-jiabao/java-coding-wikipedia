# 项目测试

当你创建好了services层代码想要测试他的正确性和功能性的时候，需要对其进行测试。

## mapper层

mapper是项目中用来映射sql操作而生成的类，mapper有一个mapper类文件还有一个mapper的xml配置文件，在这二者的结合之下，就有了可以利用mapper的映射方法来操作对应的sql语句。

```java
package com.jiabao.mall.mapper;

import com.jiabao.mall.model.PmsBrand;
import com.jiabao.mall.model.PmsBrandExample;
import java.util.List;

import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;
import org.springframework.stereotype.Component;

import javax.annotation.Resource;

@Mapper  // 要想mapper可以被自动装载，需要使用注解注释告诉sprin
public interface PmsBrandMapper {
    long countByExample(PmsBrandExample example);

    int deleteByExample(PmsBrandExample example);

    int deleteByPrimaryKey(Long id);

    int insert(PmsBrand record);

    int insertSelective(PmsBrand record);

    List<PmsBrand> selectByExampleWithBLOBs(PmsBrandExample example);

    List<PmsBrand> selectByExample(PmsBrandExample example);

    PmsBrand selectByPrimaryKey(Long id);

    int updateByExampleSelective(@Param("record") PmsBrand record, @Param("example") PmsBrandExample example);

    int updateByExampleWithBLOBs(@Param("record") PmsBrand record, @Param("example") PmsBrandExample example);

    int updateByExample(@Param("record") PmsBrand record, @Param("example") PmsBrandExample example);

    int updateByPrimaryKeySelective(PmsBrand record);

    int updateByPrimaryKeyWithBLOBs(PmsBrand record);

    int updateByPrimaryKey(PmsBrand record);
}
```

mapper想要正确运行，需要创建配置类，此外还需要配置datasource（数据库连接），这个需要在配置文件中输入数据库的相关配置项

```java
// 这个配置类用来实现mapper类的自动装配的

@Configuration
@MapperScan("mapper所在包路径")
public class MybatisConfig {
    
}
```



```yaml
# 配置数据库连接的配置项
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mall?useUnicode=true&characterEncoding=utf-8&serverTimezone=Asia/Shanghai
    username: root
    password: 981221
    driver-class-name: com.mysql.cj.jdbc.Driver

# 配置mybatis的mapper文件路径
mybatis:
  mapper-locations:
    - classpath:mapper/*.xml
    - classpath*:com/**/mapper/*.xml
```

## service层

想要使用mapper，就需要在service实现类中注入mapper类

```java
@Autowired
PmsBrandMapper brandMapper;
// 在spring中，想要实现自动注入，就只能使用这种方式
// 如果使用new自己创建，是不能创建出来的，会出现NullPointException
```

service实现类的示例：

```java
package com.jiabao.mall.service.impl;

import com.github.pagehelper.PageHelper;
import com.jiabao.mall.mapper.PmsBrandMapper;
import com.jiabao.mall.model.PmsBrand;
import com.jiabao.mall.model.PmsBrandExample;
import com.jiabao.mall.service.PmsBrandService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;


@Service
public class PmsBrandServiceImpl implements PmsBrandService {

    @Autowired
    private PmsBrandMapper brandMapper;

    @Override
    public List<PmsBrand> listAllBrand() {
        return brandMapper.selectByExample(new PmsBrandExample());
    }

    @Override
    public PmsBrand getBrand(Long id) {

        return brandMapper.selectByPrimaryKey(id);
    }

    @Override
    public int createBrand(PmsBrand brand) {

        return brandMapper.insertSelective(brand);
    }

    @Override
    public int updateBrand(Long id, PmsBrand brand) {
        brand.setId(id);
        return brandMapper.updateByPrimaryKeySelective(brand);
    }

    @Override
    public int deleteBrand(Long id) {

        return brandMapper.deleteByPrimaryKey(id);
    }

    @Override
    public List<PmsBrand> listBrand(int pageNum, int pageSize) {
        PageHelper.startPage(pageNum,pageSize);
        return brandMapper.selectByExample(new PmsBrandExample());
    }
}

```

## Test测试

在springboot中，使用test类测试需要创建测试类并使用注解注释

```java
package com.jiabao.mall.service.impl;

import com.jiabao.mall.model.PmsBrand;
import com.jiabao.mall.service.PmsBrandService;
import org.junit.jupiter.api.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import static org.junit.jupiter.api.Assertions.*;

@RunWith(SpringRunner.class)
@SpringBootTest
class PmsBrandServiceImplTest {

    @Autowired
    PmsBrandService brandService;

    @Test
    void listAllBrand() {
        for (PmsBrand pmsBrand : brandService.listAllBrand()) {
            System.out.println(pmsBrand);
        }
    }

    @Test
    void testCreate() {
        PmsBrand brand = new PmsBrand();
        brand.setId(7L);
        brand.setName("魅族");
        brand.setFirstLetter("M");
        brand.setSort(0);
        brand.setShowStatus(1);
        brand.setFactoryStatus(1);
        brand.setProductCommentCount(100);
        brand.setBrandStory("追求源于热爱");
        brandService.updateBrand(59L,brand);
    }
}
```



springboot的配置类文件都是通过application自动加载的，因此我们只需要创建好相应的配置类就可以了

此外springboot应用的配置文件时`application.yml`，一般数据库的配置项，mybatis的配置等等都是在这里进行配置的

相比ssm，springboot省去了很多配置类的加载