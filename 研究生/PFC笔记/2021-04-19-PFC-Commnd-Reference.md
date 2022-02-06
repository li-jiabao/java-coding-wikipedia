---
title: PFC常用命令
author: lijiabao
date: 2021-04-23 22:30
categories: ["PFC笔记", "数值模拟"]
tags: ["PFC笔记", "数值模拟"]
---

介绍常用的PFC的命令，以及命令的功能

## PFC基本元素相关命令

### ball(球）相关命令

- <font color='red'>`ball attribute`：设置球体的固有属性，如位置、大小和速度等</font>
- <font color='red'>`ball create`：使用指定的属性创建单个ball</font>
- `ball delete`：删除球体命令
- <font color='red'>`ball distribute`：可重叠的分布球体，可指定按照指定的distribution(分布)来分布球体位置</font>
- `ball extra`：设置球体的额外变量
- <font color='red'>`ball fix`：固定指定球体的速度</font>
- <font color='red'>`ball free`：释放指定球体的速度，球体的运动就反应了作用在球体上面的force</font>
- <font color='red'>`ball generate`：无重叠的分布生成球体</font>
- `ball group`：指定球体ball的组名
- <font color='red'>`ball history`：添加一个球体history。history是就是在模型过程中定期对模型属性进行取样而指定的浮点值表</font>
- `ball initialize`：修改球体的属性，和`ball attribute`命令类似
- `ball list`：列出球体相关的信息
- `ball property`：设置球体面特性，主要是用在piece上的，用来决定piece之间怎么相互作用
- `ball result`：修改ball  result logic的使用，允许在不同时间实例下将模型状态state保存到内存或者输出为2进制文件。这个ball result可以用来重新创建模型，还可作于有效后处理
- `ball tolerance`：设置接触关系探测的容许范围
- `ball trace`：添加一个球颗粒的踪迹信息。球的位置和速度在模型运行的时候可以被取样和存储，并且通过空间的路径可以被看到。
- <font color='red'>`list ball`：列出球的信息</font>
- `trace ball`：添加球粒的踪迹信息，这个命令是和`ball trace`相同的命令
- `ball ccfd list`：列出ccfd球体的信息。ccfd是：组合了DEM计算和可计算的流体动力的一个组件
- `ball thermal attributes`：设置球体温度参数的值
- `ball thermal extra`：设置球体温度的额外参数
- `ball thermal fix`：固定球体的温度
- `ball thermal free`：释放球体的温度
- `ball thermal group`：指定特定温度的球体的分组名
- `ball thermal history`：添加一个球体的温度历史
- `ball thermal initialize`：修改球体温度的属性
- `ball thermal list`：列出温度球体的信息
- `ball thermal property`：修改温度球体的面属性
- `history ball thermal`：修改球体的温度历史
- `list ball thermal`：列出温度球体的信息



### clump（簇）相关命令

- <font color='red'>`clump attribute`：设置簇的属性值</font>
- <font color='red'>`clump create`：使用指定的属性创建一个球簇</font>
- `clump delete`：删除球簇或者pebble
- <font color='red'>`clump distribute`：有重叠的分布球簇</font>
- `clump extra`：设置球簇或者pebble的额外变量信息
- <font color='red'>`clump fix`：固定指定的球簇的速度</font>
- <font color='red'>`clump free`：释放指定球簇的速度</font>
- <font color='red'>`clump generate`：生成非重叠的球簇或者pebble</font>
- `clump group`：将指定球簇或者pebble的组名
- <font color='red'>`clump history`：添加球簇的历史信息</font>
- `clump initialize`：修改球簇的属性值，可attribute类似
- `clump list`：列出球簇的信息
- `clump order`：设置旋转EOM的顺序
- `clump property`：指定簇pebble面特性（surface property）
- `clump replicate`：从模板中创建一个簇
- `clump result`：修改簇计算结果的使用
- `clump rotate`：旋转簇
- `clump scale`：缩放簇
- <font color='red'>`clump template`：在单个簇模板上进行操作</font>
- `clump tolerance`：设置接触探测的容忍度容许范围
- `clump trace`：添加一个簇或者pebble颗粒的踪迹trace
- `history clump`：添加一个簇历史信息
- `list clump`：列出clump的信息
- `trace clump`：添加一个簇或者pebble颗粒的踪迹信息trace
- `clump ccfd list`：列出ccfd簇的信息。CCFD：整合了力学DEM计算和计算流体力学的代码而成的组件
- `clump ccfd attribute`：设置ccfd簇的属性
- `clump ccfd extra`：设置ccfd簇的额外变量信息
- `clump ccfd history`：添加一个ccfd簇的历史信息
- `clump ccfd group`：指定一个ccfd簇的组名
- `clump ccfd initialize`：修改ccfd簇的属性
- `clump thermal attribute`：设置温度簇的属性
- `clump thermal extra`：设置温度簇的额外变量信息
- `clump thermal group`：指定温度簇的组名
- `clump thermal fix`：固定指定温度簇的速度
- `clump thermal free`：释放指定温度簇的速度
- `clump thermal history`：添加一条温度簇的历史信息
- `clump thermal initialize`：设置温度簇的属性
- `clump thermal list`：列出温度簇的信息
- `clump thermal property`：设置温度簇的面特性（surface property）

### wall（墙）相关命令

- <font color='red'>`wall activeside`：指定哪一面的facet是激活的</font>
- <font color='red'>`wall addfacet`：添加一个facet（wall中组成成分）</font>
- `wall conveyor`：为facet指定一个旋转传送速度
- <font color='red'>`wall create`：根据顶点信息创建墙体</font>
- `wall delete`：删除墙体或者facet
- `wall extra`：设置墙或者facet的额外变量信息
- <font color='red'>`wall generate`：根据指定形状创建墙体</font>
- `wall group`：指定墙或者facet的组名
- <font color='red'>`wall history`：添加一个墙体历史</font>
- `wall import`：导入一个墙体
- `wall initialize`：修改墙体的属性值
- `wall list`：列出墙体信息
- `wall property`：指定墙体facet面特性
- `wall resolution`：修改接触精度策略为指定的策略
- `wall result`：修改墙体结果的用法
- `wall rotate`：旋转墙体
- `wall tolerance`：设置墙体的接触探测的允许波动范围
- `wall thermal attribute`：设置温度相关墙体的属性值
- `wall thermal extra`：设置温度相关墙体的额外变量值
- `wall thermal group`：指定温度相关球体的组名
- `wall thermal initialize`：设置温度相关墙体的属性值，和attribute命令效果一样
- `wall thermal list`：列出温度相关墙体的信息，和`list wall thermal`命令效果类似
- `wall thermal property`：指定温度相关墙体facet的面特性

### contact（接触）相关命令

- <font color='red'>`contact activate`：修改接触激活的标志位flag</font>
- `contact extra`：设置接触的额外变量信息
- `contact group`：指定特定接触的组名
- `contact groupbehavior`：接触和片piece都有组指定信息
- <font color='red'>`contact history`：添加一个接触历史</font>
- `contact inhibit`：阻碍限制在指定范围的接触
- `contact list`：列出接触信息
- `contact method`：调用一个接触method
- `contact model`：指定接触模型
- `contact persist`：设置接触持续性标志位，是否持续接触
- `contact property`：指定接触特性property
- `list contact`：列出接触信息

### cmat（接触模型属性分配表）相关命令

- <font color='red'>`cmat add`：向CMAT表中添加一个新的entry</font>
- `cmat apply`：应用这个cmat表到当前的接触
- <font color='red'>`cmat default`：设置默认的CMAT表的默认slot插槽</font>
- `cmat list`：列出当前的CMAT表信息
- <font color='red'>`cmat modify`：修改CMAT表中已经存在的entry</font>
- `cmat remove`：删除CMAT表中的entry
- `list cmat`：列出CAMT表的信息

## PFC通用元素相关命令

### brick（砖块）相关命令

压缩连接后的元素（通过将在一定空间内的所有颗粒的集合体进行压缩挤压，然后将操作后的集合体保存以备后续使用），可以重复使用以构建更大的模型。

<font color='red'>使用brick元素构建模型的两个主要原因：</font>

1. 由于brick已经被压缩并且是平衡了的（接触力也被存储在brick里面了的，而且如果原先的force已经处于平衡状态那么brick连接处也会自动平衡），可以更快速的构建模型
2. 为了加快计算的速度，每块brick可以使用线性矩阵（matrix）形式来代替。靠近模型边界处（不考虑非线性的机制），线性版本的brick通常是可以接受的。有两种矩阵形式：
   1. full-matrix brick：粘结粒子pbrick平移自由度的一对一的替换刚度矩阵
   2. 和单一有限差分区brick替换相关的刚度矩阵

- `brick assembl`：组装模型中已存的brick
- `brick delete`：删除在范围内的brick
- `brick export`：导出brick为二进制文件
- `brick import`：从二进制文件中导入brick
- `brick make`：从当前的模型状态创建一个brick

### dfn（离散裂隙网络）相关命令

离散裂隙模块中，嵌入岩块内部的裂隙被看成是<font color='red'>离散的平面的有限尺寸的</font>裂隙。默认下，离散裂隙是圆盘disk型的

离散裂隙网络的成分：

- DFN：一个裂隙的集合。多个DFN可以共存在同一个模型里面，如多种裂隙或者多种裂隙类型
- DFN模板（template）：DFN模板是DFN参数的数学描述，包含了尺寸、位置和倾向分布。通过`dfn template`命令创建，是`dfn generate`命令必备的条件
- 裂隙（fractrue）：离散的平面的有限尺寸的元素。默认在3D下是圆盘形（也可以是convex polygon）。2D下面的形状是一个片段。可以使用`dfn generate`创建裂隙，也可以使用`dfn addfracture`明确定义
- intersection set（并集）：intersection的几何（裂隙之间的相交处或者DFN和其他几何形体之间的相交处）
- intersection：裂隙和几何元素（线，多边形等等）或者和其他裂隙之间的相交处。2D下是点，3D下是线段
- cluster集群：DFN集群是一系列通过intersection连接的裂隙集合，DFN集群的属性定义了DFN属性的连通性connectivity，<font color='red'>连通性对于渗流特性是至关重要的</font>。使用`dfn cluster`来进行计算，通过`dfn connectivity`使用连通性

离散裂隙网络常用命令

- <font color='red'>`dfn addfracture`：添加一个确定的裂隙fracture</font>
- `dfn aperture`：为裂隙指定孔径aperture。aperture可以用来在接触模型指定的时候以及DFN范围内元素之间，用于连通性距离计算
- <font color='red'>`dfn attribute`：设置裂隙的属性值</font>
- `dfn autoupadate`：设置交集中裂隙自动更新
- <font color='red'>`dfn cluster`：计算集群并且指定相应的组</font>
- `dfn conbine`：DFN中的简化方法，住主要是将满足一定要求的裂隙整合分类到一起去进行计算处理
- <font color='red'>`dfn connectivity`：计算和指定裂隙之间的网络连通性</font>
- `dfn copy`：赋值裂隙到另外一个裂隙网络当中
- `dfn delete`：删除DFN或者裂隙
- `dfn export`：导出裂隙到文件中
- `dfn extra`：设置dfn额外的变量信息
- <font color='red'>`dfn generate`：从数据描述中创建生成裂隙</font>
- `dfn gimport`：从指定的几何geometry集合中导入裂隙
- `dfn group`：指定裂隙的组名
- `dfn import`：从文件中导入裂隙
- `dfn information`：获取DGN裂隙信息
- `dfn initialize`：设置DFN或者裂隙的属性
- <font color='red'>`dfn intersection`：划分或者删除指定的交集</font>
- `dfn list`：列出DFN或者fracture属性
- `dfn model`：通过裂隙指定接触模型
- `dfn property`：指定裂隙的面特性
- `dfn prune`：修剪去除孤单的裂隙，单独的裂隙
- <font color='red'>`dfn template`：DFN模板实体</font>
- `dfn trace`：创建和scanline映射相关的交集

### domain（域）相关命令

所有的模型组成成分都需要位于这个domain指定的范围之内。domain的范围是固定的，不会随着模型组分分散而变化。domain边界的类型：

- destroy：碰到边界的元素和组分直接消失
- stop：碰到边界的元素和组分直接停止
- reflect：碰到边界的元素立刻以相反方向反弹
- periodic：周期，碰到边界之后从另外相对的边界处重新出现

`domain condition sx <sy sz> extent fxl fxu <fyl fyu fzl fzu>`：只有在3D下才可以指定sz和fzl、fzu。

```
; domain 创建示例
domain extent -10 10 -10 10 -10 10 condition stop reflect destory
; 上述这条命令创建了一个-10到10的正方体，指定x边界条件为stop，y边界是reflect反弹边界，z边界是删除边界。
```

### fragment（片段）相关命令

通过力学接触连接的实体集合叫做fragment，当考虑粘结颗粒破坏的本质的时候，fragmentation分析会很有用

fragment必须注册（使用命令`fragment register`）接触类型，这样才可追踪接触创建和删除以及连接bond的改变事件，从而维持fragment数据结构

fragment命令：

- `fragment activate`：激活fragment自动计算（指定时间间隔进行计算）
- `fragment clear`：清除fragment
- `fragment compute`：计算fragment，使用该命令之前需要先使用`fragment register`命令
- `fragment deacitvate`：关闭fragment的自动计算
- `fragment grouplist`：指定fragmentID到某一组中
- `fragment list`：列出fragment信息
- `fragment map`：获取fragment表的map信息
- `fragment register`：注册一个接触模型用于fragment计算分析

### geometry（几何体）相关命令

几何体可以用来作为range的过滤器，也可以作为绘图时的可视化，额外变量和组名也可以指定到几何体数据上。基本组成：node（点）、edge（线）、polygon（多边形）。geometry对象不需要基于domain创建，因为它不是模型的组成要素，geometry导入命令（`geometry import`）只支持导入stl、dxf和Itasca的数据格式。

- <font color='red'>`geometry copy`：复制几何数据</font>
- `geometry delete`：删除几何对象
- <font color='red'>`geometry edge`：创建一个edge边缘</font>
- <font color='red'>`geometry explode`：把edge连接的多边形explode分割成集合</font>
- `geometry export`：导出几何数据
- <font color='red'>`geometry generate`：根据指定形状创建几何对象</font>
- `geometry group`：指定几何体组名
- `geometry import`：导入几何数据
- `geometry list`：列出几何体信息
- <font color='red'>`geometry node`：在指定位置创建一个结点</font>
- <font color='red'>`geometry polygon`：创建一个多边形</font>
- `geometry totate`：选装结点
- `geometry set`：指定当前几何体集
- `geometry tessellate`：镶嵌结点
- `geometry translate`：平移结点

### group（组）相关命令

每个模型对象都提供了一个group关键字（主要是用来为这种对象类型创建组

**在同一个slot中，相同的对象指定到分组可能会出现覆盖缺失的情况：**

```
; 任何组在创建时如果没有使用add或者slot关键字，都会被指定到slot 1
ball group set1 range id 1,3  ; 把id在1-3范围内的球体放进set1分组中，slot 1
ball group set2 range id 1,6
; 将1-6放进行set2中，此时set1为空
ball group set1 range id 4,6
; now set1 contains ball ids 4-6, which have been subtracted from set2
ball group set3 slot 2 range id 1,6
; 因为指定slot，并不会清除前面创建的分组内容，将id1-6内容放进了slot 2中，和前面的slot 1不一样

```

**使用add关键字分组是安全的：**

```
ball group set1 range id 1,3

ball group set2 add range 1,6
; 因为使用了add关键字，会强制将set2强制放到slot 2中，因为1-3在set1中已经存在
```

**使用slot关键字是最明确的分组：**

使用slot关键字提供了最明确的组控制，并且允许用户管理不同的组集合。当作图的时候，尤其有用

```
ball group bottom_layer slot 3 range z 1 100
ball group middle_layer slot 3 range z 100 200
ball group top_layer slot 3 range z 200 300
; slot 3包含了三个分组，这三个分组属于同一个slot
ball group top_half slot 4 range z 150 300
ball group bottom_half slot 4 range z 1 150
; 上面将1-300划分为两组上下一半，放在slot4中，和3不冲突
ball group left_side slot 5 range y 1 150
ball group right_side slot 5 range y 150 300
; slot 5将模型分为两组，左右两组，按照y的范围分
```

#### 命名range和group的区别

尽管初一看，命名range和group是类似的，但是其中还是有一个很大的区别。range是对象的挑选准则，因此<font color='red'>在不同的时间可能会有不同的结果（取决于模型的behavior和range的定义）</font>

此外<font color='red'>命名range可以使用在不同的对象上，而组总是确切的指向一系列对象，只会在组中成员被删除或者在同一个slot中被一个新的组覆盖</font>

### range（范围）关键字相关命令

范围range命令是很多命令的关键字，也是用来定义范围名称的命令，一旦范围被指定，就可以在任何命令中使用该范围用来替代范围内的元素。

```
; 范围定义名称
; 将x方向的0-10区域内的元素区域定义为名为sss的范围区域
range name 'sss' range x 0 10
range name 'sss' delete  ; 删除名为sss的范围区域的定义

; range的实际使用示例
new
domain extent -500.0 500.0
ball genenrate number 1000 radius 10 30  ;生成指定半径为10-30的1000个球体
range name 'testRange' union x 0.0 300.0 x -500.0 -200.0  ; 定义一个名为testRange的区域，，区域范围利用union命令取的并集范围
ball group 'testGroup' range x -200.0 0.0   ; 创建一个分组，范围为-200到0范围内的球体
ball delete range nrange 'testRange' not group 'testGroup' not  ; 删除不在tesrGroup分组个testRange范围内的球体颗粒

```

### history（历史记录）相关命令

使用`history`命令可以在模型执行过程中取样或者存储一系列变量的值，这些变量之后可以和step数或者和其他变量一起进行绘图操作，历史记录还可以被写进文件中（使用`write`关键字），每个history命令只会给定一个变量

所有的历史记录在单个取样间隔内进行取样记录，默认取样间隔是10steps，可以使用`nstep`关键字改变取样间隔。不同的取样间隔不可以指定给不同的历史变量

`history keyword ...`：

主要的关键字有：

- `add <id i> keyword ...`：添加一个新的历史记录来存储值列表
- `delete`：删除所有的历史信息，和reset命令类似的效果
- `dump i1 ... <keyword>`：把列表中的历史记录输出到屏幕
- `hist_rep i`：指定记录历史记录值的时间步间隔，默认为10，是`ncycle`和`nstep`关键字的效果一样
- `label i s`：替换ID为i的历史记录的标签为s，当使用历史记录绘图时label将会出现，如果想使用原来的初始名，可以指定s为空字符
- `limits`：列出所有历史记录的约束值（最小/最大时间步数和最小/最大值）到屏幕
- `list <label>`：输出到屏幕的概述信息，如果指定了label，会展示指定的label而不是位置信息，和`list history`效果类似
- `ncycle i`：和hist_rep关键字效果一样
- `nstep i`：和hist_rep关键字效果一样
- `purge`：清除所有历史记录内容但是保存历史记录的列表，一旦继续cycle可以继续记录新的值
- `reset`：删除所有记录，和delete关键字效果一样
- `write i1 ... <keyword...>`：输出历史记录的内容到文件，和dump关键字效果类似，但是默认目的文件为pfc2d.his(2D情况)，pfc3d.his(3D情况)

### label（标签）相关命令

label命令可以用来在模型内部创建一个用户自定义的标签注解，在绘图的时候可以使用label，因为是在内部模型空间内定义的，随着模型视图改变，label会维持其方向。label还可以有可以在图例中显示相关的文字信息。label的ID可以用户自定义也可以自动指定，默认的label的位置是模型的原点，但是可以使用`vb`设置原点在不同位置

`label <id i> <vb> <keyword>`

label的关键字：

- `arrow b`：指定是否使用箭头从vb指向ve，只在end指定的情况下才可以
- `delete`：完全删除标签，需要指定一个ID并且必须指向一个存在的标签
- `end ve`：如果ve不同于vb，label将包含一条从vb到ve的线
- `text s`：设置标签文本为s，这个text文本将会输出到vb定义的位置处

### measure（测量）相关命令

measurement区域是用户自定义的区域，在这个区域内一系列PFC模型的量化指标被测量。通过`measure list`和`measure dump`命令可以获取任意的这些量化指标

measurement区域返回的是和measurement区域相交的或者位于该区域的对象所计算的平均值。measurement可以获取的量化指标有：坐标值，孔隙度，sliding fraction，应力和应变率。

measure相关命令：

- `measure create`：创建一个measurement区域
- measure delete`：删除measurement对象
- `measure dump`：输出累积的size分布数据
- `measure history`：添加一个measure历史
- `measure list`：列出关于measurement区域的相关信息
- `measure modify`：修改measurement区域

### plot（绘图）相关命令

plot命令获取的内容被传递给当前正在执行PFC计算引擎的用户界面输出。



## 实体命令共同的几个关键字

- `attribute`：设置实体单元的固有属性值
- `create`：创建单个实体命令 
- `delete`：删除实体命令
- `extra`：设置实体单元的额外变量
- `fix`和`free`：球和球簇共同的命令，固定实体单元的速度值或者释放实体单元的速度值
- `group`：指定分组组名
- `history`：添加一条实体单元的历史信息
- `initialize`：设置实体单元的属性值，和attribute效果一样
- `list`：列出当前实体单元的所有信息
- `property`：指定实体单元的面特性（surface property）
- `thermal attribute`：设置带温度实体的属性值
- `thermal extra`：设置带温度实体的额外变量信息
- `thermal fix/free`：固定/释放温度实体（<font color=''>只有球和簇）的速度
- `thermal group`：设置带温度实体的组名
- `thermal history`：添加一个带温度实体的历史信息
- `thermal initialize`：设置带温度实体的属性值
- `thermal list`：列出带温度实体的信息
- `thermal property`：指定带温度实体的面特性（surface property）

