# 业务增删

## entity修改

业务的增加或者修改只需要在相应的代码层级里面增加删除即可，一般如果不需要对实体类增加内容就不需要对entity进行任何的修改，如果需要向entity类里面增加属性，需要在实体类里面指定

```java
package com.jiabao.jbmall.product.entity;

import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;

import java.io.Serializable;
import java.util.Date;
import java.util.List;

import lombok.Data;

/**
 * 商品三级分类
 * 
 * @author JibalLi
 * @email 905001136@qq.com
 * @date 2021-11-21 22:48:56
 */

// lombok实体类增强的工具类，不需要再去手动的增加getter、setter和tostring这些方法
@Data
@TableName("pms_category")
public class CategoryEntity implements Serializable {
	private static final long serialVersionUID = 1L;

	/**
	 * 分类id
	 */
	@TableId
	private Long catId;
	/**
	 * 分类名称
	 */
	private String name;
	/**
	 * 父分类id
	 */
	private Long parentCid;
	/**
	 * 层级
	 */
	private Integer catLevel;
	/**
	 * 是否显示[0-不显示，1显示]
	 */
	private Integer showStatus;
	/**
	 * 排序
	 */
	private Integer sort;
	/**
	 * 图标地址
	 */
	private String icon;
	/**
	 * 计量单位
	 */
	private String productUnit;
	/**
	 * 商品数量
	 */
	private Integer productCount;

	// 不是数据表里面的内容需要设置这个注解并且设置exist为false
    /**
    如果需要增加额外的属性内容加入到实体类，需要像下面一样指定并指明这个属性在数据库中是否存在
    */
	@TableField(exist = false)
	private List<CategoryEntity> children;
}

// 一般实体类转入到前端都是经过了持久化操作转为了字符串了的，可以方便的json操作
```

