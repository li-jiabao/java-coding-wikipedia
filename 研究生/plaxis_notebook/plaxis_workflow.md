---
title: plaxis_workflow
author: lijiabao
date: 2020-11-13 10:32:21.042194300 +0800
category: md_photos
categories: md_photos
tags: md_photos
---
# plaxis工作流程

## 前处理

### 1. 土层frame
​	这个界面主要是用来模拟土层情况，即ground modeling
​	soil contour（土层轮廓边界）可以在文件菜单的子菜单进行设置调整

#### 钻孔设置
​	通过土界面的钻孔进行选点绘制钻孔位置，位置设置完毕之后还可进入钻孔设置工具进行详细的修改，可增加减少钻孔，设置钻孔的层厚，设置初始水位，钻孔修改面板有：

1. 土层：设置土层位置和材料属性。
2. 水：设置土层的上下面的水头
3. 初始条件：设置土层的初始K~0~值（设置K~0x~或者使用gravity loading，k~0~设置需设置k~0x~：水平有效应力和数值有效应力的比值），在土层材料属性里面设置的初始条件
4. 预固结：土层的pop值，一般在土层材料属性设置的初始界面设置OCR和POP，需要spatial distribution的POP时选择从钻孔来设置土层的pop
5. 场数据：可以自己定义具体的场地数据。自定义场地数据，比较高级，运用到了SBT chart，利用I~SBT~值对土层进行分类，考虑cone resistance和friction ratio，还有一个CUR 3layers method进行土层分类的标准

#### 模型土层的绘制
​	模型土层的定义设置可在钻孔修改工具进行设置定义同钻孔设置，在钻孔修改界面进行土层增减和层厚的设置，按自己的实际工况进行相应的设置和绘制。

​	也可以利用导入土层工具进行导入。设置好土层之后可在该处分配土层的材料属性。
![](%E6%B0%B4%E6%9D%A1%E4%BB%B6.png)

> dry条件是用来去除土层水压。
interpolate能够土层上部边界压力和土层下部边界眼里之间进行垂直内插
head根据指定的水头生成孔压
hydrostatic给定土层上边界压力值，程序相应的计算土层孔压分布

> 场地响应分析只能用于钻孔定义的土层。这个场地响应分析被认为是一个结构必要动力分析的预备研究，地震响应主要被支撑土层的岩土性质所影响。

#### 材料模型设置
​	材料的模型设置在材料设置里面的子菜单里面进行选择设置，材料模型有许多种：

1. 线弹性模型（适用于土体刚性结构模拟）

2. 摩尔库伦模型（一般用于岩土性状的初步近似）

3. 硬化土体模型（用于砂土，砾石，粘土和分土等软土性状的模拟）

4. 小应变硬化土体模型

5. 软土模型（适用于正常固结土和泥炭等软土性状模拟）

6. 软土蠕变模型（模拟和时间有关的软土性状）

7. 节理岩体模型（适用于成层岩体和节理发育的岩体）

8. 混凝土模型（模拟随时间变化的混凝土的性状）

​	根据实际的工况并选择相应的适应该工况土体的模型，模型设置之后还可设置土体的排水性能：排水（适用于干土，不产生超静水压），不排水（适用于研究超静水压的完全发展过程），非多孔性状（无论初始孔压还是超静水压都不考虑）。也在相同子菜单进行设置。

不同模型和排水条件下的参数设置都不一样。

#### ==土和界面材料属性和材料数据组==



### 2. 结构Frame
#### 模型的结构绘制
​	根据自己的实际工况进行模型的简化和设定，设定好模型之后确定模型需要的结构、荷载位移条件和排水等条件，再利用plaxis软件界面进行模型结构的绘制。

绘制可选的结构有：

​	土多边形soil polygon（定义一个封闭的土层），点和线（很多结构的依托绘制基准），板，铰和转动约束，点对点锚杆，锚定杆，界面，土工隔栅，隧洞。荷载和边界排水等也在这个部分进行绘制设定，有指定位移，约束，边界条件设置，集中荷载，转动约束，排水线，排水点。

​	依托点绘制的结构（point load、point displacement、fix-end anchor）
依托线绘制的结构distribution load、prescribed displacement、plate、geogrid、groundwater flow boundary condition etc（line load、linedisplacement、line contraction、plate、geogrid、embeddedbeam row， interface、node-to-node anchor、well、drain，groundwater flow bc、thermal flow bc）

#### 结构和荷载位移单元
##### 几何结构（geometric entity）
1. 点和线
    	主要是用来绘制依托结构（右击可现实可创建assign如下提到的高级结构），或者用来视觉上分割图形显得更直观

2. 板

   ​	主要用来模拟有flexural rigidity（bending stiffness）和normal stiffness的slender structure，可用来模拟wall、plate、shell、或lining extending in z-direction。

   ==最重要的属性==：flexural rigidity（弯曲刚度EI）和axial stiffness（轴向刚度EA），d~eq~=（12*EI/EA）^0.5^等效平面厚度equivalent plate thickness。

   ==提示==：两个板的连接可以通过使用创建连接功能。在输出程序可以选择双击板查看受力变形等内容

3. 土工隔栅

   ​	是具有axial stiffness无bending stiffness的slender structure，只能承拉力不能承压，一般用来模拟soil reinforcements。材料参数是EA（elastic axial stiffness）大部分应用需要使用各向异性属性避免不真实的剪切变形unrealistic shear deformation而且要求使用updated mesh calculation考虑membrane effect。

4. 界面

   ​	界面是一个用来为了更好的模拟土-结构相互作用（soil-structure interaction）的连接单元joint element。interface可能用来模拟在板和土之间的连接处的剧烈剪切材料区域。

   ​	界面可以创建在板、土工格构和两个土多边形的之间的地区。如果已经有几何实体（line），尽量右击对象去创建而不是再创建一个，避免模型变得不必要的large和unwieldy。界面有正负界面，只是为了更好区分，而不会影响实际的模拟。

   ==界面的属性==：

   ①材料模式：

   1. 从相邻土层：界面的roughness通过选择合适的strength reduction factor进行模拟，这个是在界面的土层材料集设置给相邻土层的。可选的还有residual strength如果界面强度达到可以指定。这个因子和界面强度（wall friction and cohesion）和土强度（friction angle and cohesion）有关。
   2. 定制：选择这个选项可以直接赋值材料属性给界面，strength reduction factor默认设置为1.  

   ② permeability渗透性：默认像plate和geogrid这些结构单元是可渗的。把界面赋给处结构单元外的几何实体可设置为不可渗的。原则上In principle，界面渗透性是在相应的材料集中设置，但对象浏览器中界面的一可选框是可以激活界面是否flow，这可以影响实际计算的界面渗透性。In Flow，只有计算非结构单元需要设置几何实体为deformation的不考虑。因此只是用interface阻挡flow不充分insufficient。界面的endpoint始终可渗。

   ③虚拟界面厚度：是一个假想尺寸，用来定义界面材料属性，厚度越大产生的变形越大。一般来说，界面假定产生很小的变形，因此界面厚度设置很小，另一方面如果数值设置过小，会出现ill-condition。虚拟厚度等于虚拟厚度因子乘以全局元素尺寸global element size。global element size有生成网格是由全局粗糙度设置global coarseness setting决定。默认因子为0.1，在对象浏览器中这个值可以被更改。如果界面受较大的应力，应减少factor。

   ④靠近角点的界面：有些需要特别注意的点，钢体结构的角点和边界条件突然改变的点可能会引起应力应变的高峰值。这些引起的问题（non-physical stress oscillations）可以通过设置界面单元来解决。

   ⑤连接：两个界面单元的连接点默认共享自由度，这意味着连接时刚性的，这个连接点可以通过连接功能自定义。
   ![](%E6%B8%97%E9%80%8F%E8%AE%BE%E7%BD%AE.png)

6. 点对点锚杆
   	   点对点锚杆不能放置在已经分布了feature的line

7. 锚定杆
       锚定杆可以用来模拟简单的pile，不考虑pile-soil相互作用，也可模拟锚杆或者props支撑挡土墙retaining wall。

8. 隧道

    隧道创建只能在隧道设计器进行创建，lining衬里、负向界面等都只能在隧道设计器里面设置创建，一旦创建完成，不能在frame阶段修改。
    
    隧洞设计器里面有两个frame，截面（设置隧洞截面形状的界面）和属性（设置linning负向界面等的界面，还可设置reinforcement设施）
    
    截面下有三个子界面
    
    - general设置：1.截面形态类型：①自由（可以自己选择绘制线或者弧，按自己需要的隧道截面设置即可）②圆形。2.起始点偏移的情况
    - 线段：绘制隧洞的截面形状的各个线段信息，具体看情况选择，其中边栏有：延伸至对称轴选项，关闭对称轴选项，添加线段、插入、删除选项
    - 子阶段：可以在这里定义隧洞开挖的不同的阶段位置情况，侧边栏有：添加子阶段、插入、删除选项，从点添加子阶段，连接两点添加子阶段，添加直接子阶段等。还有相交选项（将线段相交点绘制出来）
    
    属性下有两个子界面
    
    - 一般：在这里添加linning衬里、负向界面、线荷载、线收缩、板和土工隔栅等结构。
    - reinforcement增强结构：创建里面多了一个创建reinforcements选项，可以绘制特定线段的加强结构（应该是类似喷锚支护）
    
    建立好之后就可以点击生成了，生成好之后就可关闭，关闭之后不可修改。
    
```
    可以设置任意性状的隧洞放置于几何模型，隧道形状包含了cross section（横截面）拱或者直线，还可以补充其他特性如plates、interface、load、prescibed displacement、contraction，groundwater flow boundary condition etc。可通过隧道浏览器窗口设置具体的隧道形状和属性
```
NATM：

新奥隧洞开挖法，又被称为顺序开挖法（SEM，Sequencial）或者喷射混凝土衬砌法（SCL，Sprayed concrete lining method），是一种现代隧洞设计和建造的方法。这项技术首先获得关注是1960年代澳大利亚的科学家的成果，命名是为了和之前老方法做出区分。最基本的区别是经济效益，新奥法利用隧洞周围岩体的可用的inherent strength来稳定隧洞，减少了建造所花费的经济和时间代价。新奥法被认为促进了现代隧洞行业的变革，许多现代隧洞都是使用这个方法开挖。从经济角度看，新奥法非常有吸引力，同时在岩溶条件下使用该方法更为合理。

新奥法整合了岩土体在荷载下的形状准则和建造过程中的监控。这个方法常被称为'design as you go'挖到那设计到哪，通过观察到的ground condition提供优化的support。更准确的说应该被描述为'design as you monitor'方法，基于观察到的衬里的收敛和发散以及主要岩石条件（prevailing rock conditions）的mappings。并不是特定的一组挖掘和支护技术。

##### 荷载和边界条件

1. 指定位移
    	    是一种特殊的条件，可以指定模型位移到指定的位置。有三种位移类型：指定，自由和固定。指定位移是设置最后阶段的总位移而不是附加的阶段位移，如果某一阶段没有指定位移增量，那么设定为指定位移的数值应该和上一阶段一样，如果将值设置为0，会在反方向产生一个位移抵消，导致最后阶段位移为0

2. 线收缩
    ![](%E7%BA%BF%E6%94%B6%E7%BC%A9.png)
    ![](%E7%BA%BF%E6%94%B6%E7%BC%A9%E6%8F%90%E7%A4%BA.png)

3. 模型边界条件

   ​	模型的边界条件可在模型浏览器中选择其中的子菜单进行模型的许多条件的设置，其中，变形、地下水流边界和动力条件子菜单可以设置相应的边界条件

4. 嵌入式排梁embeddedbeam row
       嵌入式排梁的性状behaviour选项有：
   
   1. pile（桩）
   
   2. rock bolt（锚杆）
   
   3. grout body（注浆体）

   	在选择对象浏览器里面可以进行修改，模型浏览器可以总管全部的结构和土体的各种设置。选择不一样的选项时，又会有其他不一样的属性设置。
   	前两种类型pile和锚杆，会多出来连接点connection point（桩：顶部和底部，锚杆：第一连接到线的点和第二）和连接方式connection options两个下拉选择框drop-down menu。主要用来模拟一排用来传递荷载到周围土体或岩石的长细结构成员，只在plane strain models可用，需求的信息需要包含单桩（或注浆体或锚杆）属性和平面外方向的spacing间距。
   	==提示==：嵌入式梁适合在安装过程中只对周围岩土体造成有限扰动的桩类型。这包含了一些bored pile但并不是所有的the technologies for replacement pile or displacement pile。桩的安装对soil stress ratio和pile skin resistance有很大影响，在这种情况，在模拟pile installation effect 时适当计算这些因素是有必要的。
   	==提示==：当嵌入式排梁位于有线弹性材料性状的ploygon cluster，指定的spacing和shaft resisitance被忽略。
   	==参数设置==：pile或rock bolt的几何特性，材料属性，平面外out-of-plane direction的间距，表面摩擦力skin friction，承载力bearing capacity以及界面刚度因子interface stiffness factor
	![](%E5%B5%8C%E5%85%A5%E5%BC%8F%E6%8E%92%E6%A2%81.png)

5. 分布荷载
        荷载的激活冻结在分布施工界面设置。荷载可以在选择对象浏览器种设置静荷载和动荷载。

    （荷载类型有四种，uniform每个位置都一致，linear成线性分布，perpendicular垂直的方向，设置数值为正时向下的荷载方向，垂直增量，垂直不断递增，不太懂创建出来的感觉都是一样的）
    
6. 集中荷载
        固定结构计算优先于荷载，因此荷载作用在固定的几何实体上是没用的。，如果只有一端固定是可以应用荷载的动荷载的设置有许多参数需要了解，按需选择。

#### Design Approach

plaxis2D可以为荷载和模型参数进行partial factor 处理，使得plaxis可以利用Eurocode、LRFD或者其他的基于partial factor的设计方法的框架结构去设计计算内容和步骤等。

主要的idea是一个项目首先是在没有使用设计方法的在Severceablity Limit State条件进行分析。这些输入值都被假定为特征值或者代表值，因此计算的结果就可能是变形应力和结构力的保守估计。

如果在正常使用极限状态下可以达到满足条件的值，可以考虑使用设计方法去处理Ultimate Limit State设计。为了进行设计的计算，除了正常使用极限状态的计算阶段外，还需要定义新的计算阶段，有两种方案去实现：

- 首先按顺序执行正常使用极限状态的各个计算阶段，然后再在每个正常使用状态下分别新建一个极限应力状态，parent phase就是每一个正常状态
- 正常使用极限状态和极限状态分别进行，互不干扰，各自都从initial phase开始计算。

对于岩土工程师，考虑到所有可能影响设计的条件是他们的责任，工程师的判断对于考虑在设计中的不同混合起着至关重要的作用。

不同的设计方法，本质上也就是一连串的partial factors，可以根据实际可用的design methods为荷载和模型参数定义这些partial factors。定义好之后，也可将其导出到全局数据集中，也可中全局数据集中导入设计方法。==Note==：设计方法在土层、结构、water condition、construction stage都可以选择。

##### 为荷载定义partial factors

可以定义design approach的荷载类型有：

- 线荷载
- 点荷载
- 指定位移

不同的partial factors可以应用到不同的荷载或者荷载组里面

荷载的design approach是在模型浏览器里面的phase definition mode进行设置。

==Note==：荷载的partial factors是用来multiplied reference values获取design values

##### 为材料参数定义partial factors

在设计方法里面的材料那一栏可以设置定义。

材料的设置在材料的设置里面，其中可以设置的参数旁边有个下拉框可以选择。

==Note==：材料的partial factors是用来除的，design values是reference values divided partial factors

==Note==：有几个高级模型不能用设计方法：NGI-ADP、UDCAM-S、Sekiguchi-Ohta和用户定义的模型

为了完成design calculation的定义，需要进入到construction stage定义的阶段选择设计方法，并荷载和材料需要选中为改设计方法才能生效。必须在阶段定义的设计方法和荷载材料里面的标签要对上，不然不能使用。

当使用高级模型和设计方法结合的时候，这些高级模型仍然保持高级特征和性状，和安全性计算不同的是，因为安全性计算时，高级模型会失去他们的高级特性并且转换为摩尔库伦模型。当比较安全性分析和使用了相同$c和\psi$的设计方法的目标值时，应该意识到由于这个原因结果可能不一样。

#### 水力条件

​	hydraulic condition作为一个基于钻孔和土聚合体的水条件自动生成孔压的替代者，孔压分布可以用来地下水流计算或者完全配对的flow-deformation analysis。这要求定义地下水流边界条件groundwater flow boundary condition，即水力条件。

水力条件同样包含特殊条件可以施加给模型去控制特定位置的孔压，在地下水流计算或者完全配对的flow-deformation analysis时的。通过右击geometric entity或者对象浏览器的内容然后去创建。

除了在这里考虑的特定边界条件，全局模型条件关于开放和封闭边界以及precipitation condition可以在每个计算阶段在模型浏览器的模型条件子菜单设置，指定的水力条件优先于全局模型条件。对于临时transient 水利流动和fully coupled flow-deformation analysis。

水力条件可以使用流函数将其定义为时间的函数。

1. 水井well

   ​    创建一个水井，这个结构创建类似创建line，是用来创建抽水井或者注水井表示该位置指定的flux（discharge）流入或流出，属性可以在对象浏览器进行修改。设置属性由1. behaviour：抽水或者排水2. Q：抽水或者注水流量3. h~min~水井的可能最小水头，当地下水水头低于该值，不再有水被抽出。
   ![](%E4%BA%95.png)

2. 排水drain

   ​    是用来在几何模型里面定义一条线来表示这个位置的额外的孔压缩减reduced pore pressure，和line创建方式一样。创建好drain后，需要设置参数，其参数设置和behaviour选择有关：1. normal：当选择这个参数，需要输入地下水头，这个选项只和地下水流计算groundwater flow calculation或fully coupled analysis。在这个计算下，drain所有节点node的孔压衰减到和给定水头一致，低于该水头的不受影响。在consolidation analysis，drain减小这个excess pore pressure to zero并且指定的水头被忽略。2. 真空vacuum：选择这个参数，要求输入groundwater head，这个水头是用来定义vacuum consolidation过程地下的underpressure，这个选项之和固结分析consolidation analysis、groundwater flow calculation 或fully coupled analysis。在这个计算中，drain的所有的节点孔压衰减到和给定水头一致，和上一个不一致的是，低于该水头的受drain影响。在分布计算激活冻结，可在对象浏览器进行修改。

3. 地下水流动边界

   ​     这个是用来设置地下水的流动边界情况，创建的时候和line类似，设置参数的时候behaviour有许多参数，不同的选择，边界条件和使用情况都不一样。

   ​     模型的边界可以很方便的在模型条件子菜单进行指定，默认情况模型底部设置为closed，others设置为open（seepage）。

   ​     在计算阶段，在水力条件定义的流动边界功能总是优先于模型条件。在fully coupled analysis，被布置到任何内部几何体的水条件都考虑到step0（计算开始阶段）plaxis忽略指定的水条件设置并基于之前步骤的结果进行flow calculation.闭合边界值适用于外部模型边界，如果closed赋给了内部的几何体，plaxis会忽略掉

   ​    水流边界类型：

   1. 渗漏seepage：渗漏边界时谁可以自由流入流出的边界条件，一般用在地表，在潜水面（phreatic level）上部或者在外部水面上部的，比如水坝和dike。如果边界被设置为seepage且完全在水面上方，则会设置为渗漏可自由流出流入。如果低于水面，该自由边界自动设置为水头边界（groundwater head condition），这个时候每个边界点的水头由该点距离水面的距离。水位线和几何体边界平面线的交点孔压为零，该transition line上部被认为上部边界，下部为下部边界不同条件可应用这个，因为plaxis是按节点进行计算，上部节点按上部边界，下部按下部边界，当有降水，seepage不会自动变为Infiltration边界

   2. closed闭合：不能发生流动，表现为地下水流动（groundwater flow calculation和fullycoupled  flow-deformation analysis）和额外孔压消散（在固结计算时），只用于模型外部边界，在土体内部需要一条不可渗透的线时，需要插入合适的界面

   3. 水头head：除了可以在模型条件设置一般潜水面设置水力边界，还可用这个选项进行设置。如果外部几何体边界给定水头，这个边界将会生成外部水压，变形分析程序将会把这个水压当作traction loads并一起考虑到土重和孔压里。这个选项还有三个可选参数：变化规律（设置水头的变化形式）、水头值、时间相关性（是否和时间有关，设置了之后可以自定义时间有段的流函数。只适用于transient flow和fully coupled flow-deformation analysis）

   4. inflow流入：和水头模式参数类似，只是设置有点不一样。设置流入模型边界

   5. outflow渗出：和流入类似

   6. Infiltration回灌：除了在模型条件的precipation降水条件中设置，还可在这里进行设置。参数有：q降水量，单位为单位长度除以单位时间，负值可用来模拟蒸发运输。还有两个参数为最大最小孔压水头，和边界的计算有关，长度单位。最后一个为与时间相关性：不变和随时间变化的流函数。

![](%E6%B0%B4%E6%B5%81%E8%BE%B9%E7%95%8C%E6%8F%90%E7%A4%BA.png)
![](%E6%B0%B4%E6%B5%81%E8%BE%B9%E7%95%8C%E5%8F%82%E6%95%B01.png)
![](%E6%B0%B4%E6%B5%81%E8%BE%B9%E7%95%8C%E5%8F%82%E6%95%B02.png)
![](%E6%B0%B4%E6%B5%81%E8%BE%B9%E7%95%8C%E5%8F%82%E6%95%B03.png)
![](%E6%B0%B4%E6%B5%81%E8%BE%B9%E7%95%8C%E5%8F%82%E6%95%B04.png)
![](%E6%B0%B4%E6%B5%81%E8%BE%B9%E7%95%8C%E5%8F%82%E6%95%B05.png)
![](%E6%B0%B4%E6%B5%81%E8%BE%B9%E7%95%8C%E5%8F%82%E6%95%B06.png)

#### 流函数Flow function

这个函数代表水头或者discharge随时间的变化函数。，可在水位和地下水流动边界进行设置定义（==不能定义到非水平水面==）。流函数的时间值代表的是全计算阶段的时间而不是每个阶段的时间间隔interval。这代表了每一个计算阶段的取值，只取在和该阶段时间相关的值上面。

   流函数参数：

   信号：该参数选择不同，接下来的参数也不同。

   1. 简谐：是指一个于简谐运动有关的时间函数，
   2. 表：从表中的特定的值和对应时间导入
   3. 线性：指随时间成线性变化，进行一些必要的值的设置即可

#### 热力学条件

在温度是土和结构性状的影响因素的情况下，plaxis可以将温度效应考虑进来。ground温度分布的计算基于thermal calculation。

由于地下水流动在ground的热能传输起重要作用，因此thermal计算伴随着groundwater flow计算（thermal-hydraulic（TH） coupling）

1. 热流动边界

#### ==结构材料模型设置==

##### 板材料属性设置



##### 土工隔栅材料属性设置



##### embedded beam row材料属性设置



##### 锚杆材料属性设置



### 3. 划分网格frame

当几何模型建立完毕之后，在计算开始之前，为了执行有限元计算需要进行有限元网格单元划分。网格需要充分详细可以获得准确的计算结果，但不必要过于精细，过精细会导致过多的计算时间。plaxis2D自动生成的网格，考虑了土层和结构荷载和边界条件等。

#### 网格划分

网格划分是自动的，自动根据用户选择的设置机型网的划分，也可以进行局部的细化或者糙化处理。

有限元元素elements的类型：15节点和6节点，也是用户自己选择设置，除了土层单元，也有结构的elements设置，但是这些会自动调整为兼容土体element。

target element dimension：计算element distribution的公式

$$l_e=r_e\times0.06\times\sqrt{(x_{max}-x_{min})^2+(y_{max}-y_{min})^2}$$

网格划分是元素分布选择有：

1. very coarse：r~e~=2.00（30-70elements）
2. coarse：r~e~=1.33（50-200elements）
3. medium：r~e~=1（90-350elements）
4. fine：r~e~=0.67（250-700elements）
5. very fine：r~e~=0.5（500-1250elements）

elements数量取决于几何模型的形状和可选的局部refinement有关，并不会受element type影响。

==note==：15节点单元会得到更详细的节点分布，因此结果更准确，但时间开销更大。当然，这个elements distribution可以使用专家设置自定义。

#### 网格细化和糙化设置

**local refinement：**

在较大的应力集中或者大的变形梯度地区，要求进行局部处理，这样可以获取更加准确的finite element mesh，但是其他部位不需要细化处理，这种情况一般发生在几何模型有edges 或corners或structural objects时。

local refinement是基于几何实体中可以定义的local coarse factor处理的。这个factor指示了相对于target element size定义的相对元素尺寸，由element distribution parameter决定的。默认情况，大多数几何实体的coarseness factor设置为1.0，structural object和loads设置为0.25。0.5是将element尺寸减小为目标尺寸的一半。这个factor可以选中几何实体在选择浏览器的coarseness factor进行设置，接受的范围0.03125-8.0，大于1的值是将网格进行糙化。

局部refine可以点击refine mesh按钮或者选中需要refine的几何实体右击然后选择finer mesh选项。局部糙化（以factor为$$\sqrt{2}$$）进行，也是和局部细化类似，选择按钮或者右击实体进行。同样的操作还有一个重置局部操作。

在网格frame整个土层材料设置无效，且整个模型为灰色，refine或者coarsened object显示为绿色和黄色。

Enhancd mesh refinement：为了更好的在structural elements、loads和prescribed displacement生成准确的网格，plaxis自动采用mesh refinement。适用的条件有：

1. 点点或者点线或者线线距离过于接近，要求更小的elements尺寸避免大的aspect ratios
2. geometry line之间的角度不是90的倍数，为了获取geometry discontinuity的更准确的应力，不同角度对应的local element size factor会按照表格的值去取。
![](factor%E5%8F%96%E5%80%BC.png)
==提示==：automatic refinement在mesh选项窗口不选,默认为关闭。

### 4. 渗流条件frame

#### 渗流设置

### 5. 分布施工frame

有限元计算可以分为几个连续的计算阶段，每个计算阶段对应特定的loading或者construction stage。construction stage可以在stage construction mode进行定义，不同计算阶段可在阶段浏览器查看。

#### 5.1 阶段浏览器

##### Toolbar

有五种按钮：

1. 添加按钮：添加一个计算阶段，选中参考阶段（父阶段）并点击，就在该父阶段下创建了新的阶段
2. 插入按钮：选择一个阶段并点插入按钮，插入一个新的阶段，以选中阶段的父阶段为父阶段，新建的简短为选中阶段的父阶段。这种操作可能会导致后面定义的简短发生变化，因此后面的阶段可能需要进行修改，因为开始阶段condition发生了变化。
3. 删除按钮：删除阶段，会自动进行阶段的父阶段的修改但还是要求进行重新定义后面的阶段。
4. 编辑按钮：进行阶段的具体设置和定义
5. 复制按钮：复制阶段的一般信息到clipboard。

##### 计算状态

每个定义好的阶段前面有一个指示器，显示此时阶段的状态，有六种状态图标：

1. 播放图标：需要进行计算的阶段
2. 空白图标：不进行计算的阶段
3. 对勾图标：就算完毕的阶段，没有发生错误
4. 感叹号图标：计算完毕，但是中途将程序进行了猜测让计算继续进行，可在日志进行查看
5. 叉图标：计算失败，具体信息日志查看
6. 问号图标：计算失败，但是子阶段still possible，信息在日志查看

##### 阶段识别

每个定义好的阶段有他自己的ID，在阶段浏览器展示，由caption和name组成，name程序自动生成，可修改，caption of the可在阶段窗口进行修改

##### 计算类型标识

荷载类型、孔压计算类型由阶段浏览器阶段ID旁边的标识表示

#### 5.2 计算阶段的顺序

计算阶段的顺序可以通过选择reference phase然后再添加阶段或者再阶段窗口选择reference phase。默认情况，先前选中的阶段式parent phase。注意之后出现再阶段列表的该阶段不可再被选取

##### 影响顺序的特殊情况

在某些特殊情况下，计算阶段的顺序并不是直接顺序往下走的:

1. 如果不同的荷载或荷载结果在同一个项目被分别考虑，initial phase可被选择为reference phase不止一次。
2. 特定的情形下，荷载逐渐增大直到失效来决定safety margin，当继续建造过程，下一个阶段将应该从先前的建造阶段开始而不是从失效情形继续。
3. 当在建造阶段中需要进行safety analysis，这个计算阶段是Safety。一般来说，这个阶段的计算结果是出于failure状态。当继续建造过程，下一阶段应该从先前的计算阶段开始而不是安全性计算结果继续。相应的，如果安全性分析是在计算过程的末尾机型，这种情况，reference phase应该选择相应的建造阶段。

#### 5.3 phase window

每一个计算包含一系列参数用来控制计算过程的，这些参数的修改定义都是在阶段窗口中进行，双击阶段或者右键修改唤出该窗口。

在这个窗口，用户至少需要为每一个阶段选择计算类型和荷载类型，plaxis为大多数的计算控制参数提供了方便的默认设置，用户可以修改这些值，最右边界面展示了相关计算类型和参数的描述。

##### 初始阶段计算类型

该阶段的计算类型选择是非常重要的，后续的阶段计算大多都是基于此阶段开始之后的计算

1. K0 过程
   直接生成初始有效应力、孔压力、和状态参数。不能保证平衡。需要设置==∑Mweight==参数，用来设定初始应力状态的

2. 场应力
   直接生成初始有效应力、孔压力、和状态参数。不能保证平衡。

3. 重力加载
   从有限元单元计算初始化应力。将被用于非水平土层。需要设置==∑Mweight==参数

4. 仅渗流
   没有有效应力计算。用孔压力计算类型定义流

##### 计算类型：

1. 塑性
   弹塑性排水或不排水分析。不考虑固结

2. 固结
   变形和超孔隙压力的时间相关分析。需要输入土的渗透率和使用非零时间间隔。

3. 安全性
   用强度折减发计算总安全因数. 在安全分析中网格没有被更新。

4. 动力
   在时间域内进行动力分析，用非零时间间隔。

5. 渗流与变形完全吻合
   总孔隙水压力和变形的时间相关分析。需要输入渗透率和使用非零时间间隔。

6. Dynamic with consolidation
   Dynamic drained analysis in the time domain. Use non-zero time interval.

#### 5.4 分析计算类型

plaxis分析的第一步是在phase window的计算类型下拉菜单中选择定义阶段的计算类型。可选的参数有：在初始阶段的K~0~ procedure and Gravity loading来生成初始土壤的应力状态。Flow only选项尽在需要进行地下水流动分析的时候选择。Deformation analysis选项如：塑性、固结、安全性计算、动力、Dynamic with consolidation和渗流变形完全吻合。

##### 5.4.1 初始应力生成

许多的岩土工程分析要求指定一系列的初始应力，土体的初始应力状态受土体的重量和形成历史的影响。初始应力状态通常由初始vertical 有效应力决定，初始horizontal有效应力和vertical有效应力有关，$$\sigma^{'}_{h,0}=K_0\times\sigma^{'}_{v,0}$$，K~0~是cofficient of lateral earth pressure，土壤侧压力系数。

在plaxis中，有效应力可以用K~0~ procedure、Gravity loading或场地应力选项来生成，==这些选项仅在初始阶段才有，建议先生成初始应力之后并inspect结果之后再进行计算阶段的定义和计算。==

==提示==：K~0~ procedure 不同于Gravity loading是由于field equilibrium场地平衡在初始阶段没有被检查。K~0~尤其适合水平地表和土层与phreatic level潜水面平行地表的情况，在这个情况下，平衡自动满足（平衡条件：vertical stresses = gravity weight，horizontal stresses = lateral reaction forces along the model boundaries）对于其他的所有情况，K~0~的使用可能导致out-of-balance forces

初始阶段可能包含pre-loading或者over-consolidation，特别的，高级土体模型可能会考虑over-consolidation，这需要over-consolidation ratio（ocr）或者pre-overburden pressure（pop），这些参数数据可在钻孔设置定义，也可在材料数据定义设置。

作为部分initial stress generation，principal stress history parameter（$$\sigma^{'}_{1,max}$$）需要initialised。The principal stress history （$$\sigma^{'}_{1,max},\sigma^{'}_{2},\sigma^{'}_{3}$$）用来计算对等的preconsolidation pressure $$P_{p}^{eq}$$，这个参数被用在高级土体模型中initialise a cap-type yield surface。

###### 5.4.1.1 K~0~ 过程
直接生成初始有效应力、孔压力、和状态参数。不能保证平衡。需要设置==∑Mweight==参数（代表激活的土重），用来设定初始应力状态。

是plaxis特殊的计算方法，考虑了土体的loading history。初始应力生成过程要求的参数在材料数据的初始tabsheet中进行设置，只是有一个K~0~值可以指定。

$$K~_{0,x}=\sigma_{xx}^{'}/\sigma_{yy}^{'}$$，$$K_{0,z}=K_{0,x}=\sigma_{zz}^{'}/\sigma^{'}_{yy}$$。

实际上，正常固结土的K~0~值通常被认为和摩擦角有关。Jaky's empirical expression ：$K_0=1-sin\psi$。对于超固结土，值可能比这个值大。

对于摩尔库伦模型，默认的K~0~值基于Jaky's formula计算的。对于高级模型（硬化土体模型、小应变硬化土体模型、软土模型、软土蠕变模型、modified Cam-Clay 模型、Sekiguchi-Ohta模型），默认值基于K~0~^nc^参数同样受到超固结比和前期固结应力POP：

$K_{0,x}=K_0^{nc}.OCR-\frac{\nu_{ur}}{1-\nu_{ur}}(OCR-1)+\frac{K_0^{nc}POP-\frac{\nu_{ur}}{1-\nu_{ur}}POP}{|\sigma_{yy}^0|}$

使用很小或者很大的K~0~值可能会导致应力违反摩尔库伦失效条件failure condition。这种情况下，plaxis自动减小侧面应力到满足failure condition。此后，这些应力点处于塑性状态并被看成塑性点，尽管修正的应力状态满足，可能会导致应力场不相等。一般来说，生成不包含摩尔库伦塑性点的应力场才是preferable。==提示==：在初始有效应力的输出程序选择应力菜单选项的Plastic点选项查看plastic point plot。

对于cohesionless material，为了避免摩尔库伦plasticity，K~0~取值范围：$\frac{1-sin\psi}{1+sin\psi}<K_0<\frac{1+sin\psi}{1-sin\psi}$

当采用K~0~过程，plaxis自动生成等同于土重的垂直应力，水平应力则根据K~0~计算。如果值选在了plasticity不发生的情况，这个K~0~过程并不能确保最终的应力场是equilibrium的，因为并不产生剪应力。完全equilibrium只在水平地表下地层平行地表和水平潜水面。因此：==K~0~过程并不建议处理非水平地表，非水平地表需要剪应力去形成equilibrium stress filed==

如果应力场仅需要很小的equilibrium correction，可以采用K~0~过程跟着Plastic nil-phase（就是不加额外荷载的塑性计算类型），如果应力out of equilibrium，就不能采用而应选择Gravity loading procedure。

K~0~过程的最后，完全土重被激活$\sum{Mweight}=1$

###### 5.4.1.2 场应力
直接生成初始有效应力、孔压力、和状态参数。不能保证平衡。仅在初始阶段可以使用，在计算类型中可选择。

场应力选项允许在模型中设置honmegenous initial stress state，考虑主应力（principal stress）rotation。这个选项与深土层或者岩石层的在地质历史的形成过程造成了principal stress rotation（shearing）这种情况比较相关。

在计算类型中选择了这个选项后，还需要指定三个主应力的大小（$\sigma_1、\sigma_2、\sigma_3$）以及主应力方向的朝向（Axis1），$\sigma_1、\sigma_2$是平面内主应力方向，$\sigma_3$平面外方向应力。==NOTE==：compression is negative。压力是负值。

三个应力值不需要指定，默认$\sigma_1是最大，\sigma_3最小$，此外，（x,y)coefficient不必要是单位矢量（vector）。在输出程序查看主应力结果时，展示的最大最小不仅包含平面内也包含平面外。

作为模型条件场应力的全局定义的替代选项，用户还可利用它定义每个soil ploygon的场地应力条件。在土层模式下，选中土块右击选中create filed stress然后在对象浏览器中clusterfiledstress进行设置相关数值即可。这种给不同的cluster定义应力状态可用来模拟有disturbance zone，在这里，软弱层可能有着和disturbance zone外部岩块不同的应力状态。

在进行场应力初始计算后，用户仍然需要应用nil-phase去处理模型中任何的不平衡去获得正确的应力状态。

这个计算不考虑由于重力引起的随深度增加的应力，因此，$\sum{Mweight}$设置为0。然而，定义在材料属性设置时的是非零的，因为为了在dynamic计算生成正确的mass和inertia。==需注意==：跟随场应力计算之后的计算阶段都需要设置为0去避免突然unbalance发生。

除了输入场应力条件，还需要将模型边界设置为fully fixed，需要在模型浏览器变形菜单设置边界为fixed。==NOTE==：场应力选项可能产生shear stress，错误的边界条件并不能承受，因此需要设置正确的模型边界。

这个计算类型的结果是根据预定义的应力大小和主应力方向的矢量方向产生的。如果采用这个类型，应力状态不仅产生在volume elements，同样生成在interface elements。==注意==：场应力选项不影响结构单元同样不考虑外部荷载和边界条件，和K~0~过程类型一样。因此，在这个计算类型下的初始阶段，需要冻结这些单元。

###### 5.4.1.3 重力加载
从有限元单元计算初始化应力。将被用于非水平土层。需要设置==∑Mweight==参数

重力加载是塑性计算的一种，初始应力基于土体体积的重量产生。如果采用重力加载，通过在第一个计算阶段利用土体自重生成初始应力。这通过设置$\sum{Mweight}=1.0$完成。当采用像摩尔库伦模型的elastic perfectly-plastic soil model，K~0~主要取决于设定的泊松比$\nu$，因此选择合适的泊松比获得真实的K~0~很重要。如果有必要，各自的材料属性集也需设置各自的泊松比以在重力加载计算中提供合适的K~0~值。这些设置可能在后续被其他材料数据集代替，对于一维压缩，让弹性计算公式：$K_0=\frac{\nu}{1-\nu}$

如果设置K~0~为0.5，需要指定泊松比值为0.333.由于泊松比小于0.5，重力加载阶段不可能产生大于1的K~0~。如果需要大于1的K~0~，模拟加载历史并在加载卸载使用不同的泊松比或者使用K~0~ procedure。

当使用高级土体模型，重力加载后的K~0~和材料集的$K_0^{nc}$参数有关。

==提示：==为了确保模拟中采用了不排水材料的初始有效应力，忽略排水性状的选项需要勾选。==如果采用的重力加载生成了初始应力，下个阶段的开始需将位移设置为0。==这消除了初始应力生成过程对随后计算阶段产生的位移的影响，应力始终维持。重力加载过程中忽略OCR和POP参数。

在某些情况下，重力加载过程中可能产生塑性点，对于one-dimensional compression一维压缩的无粘性土cohesionless soil，如塑性摩尔库伦点，除非满足不等式：$\frac{1-sin\psi}{1+sin\psi}<\frac{\nu}{1-\nu}<1$

###### 5.4.1.4初始应力生成的结果

在初始应力结果产生后，可查看初始有效应力的结果图，同样可以查看塑性点的图。

使用本质上和单元有差异的K~0~值有时候可能导致产生的应力状态违反了摩尔库伦criterion，如果图标显示了很多的红点，K~0~值应该选择为接近1.0的值。

如果有少量的plastic point，采用plastic nil-phase是可取的。当使用硬化土体模型且定义了正常固结的初始应力状态（OCR=1,POP=1），图表会产生许多的hardening points，不应该关注这些点，因为他们只是显示了正常固结的应力状态。

###### 5.4.1.5 仅渗流
没有有效应力计算。用孔压力计算类型定义流

这个选项允许纯粹的饱和和不饱和条件下的地下水渗流计算，只在初始计算阶段可被选择。这个选项会影响接下来的阶段。这个选项下，接下来的额计算类型将被自动选择为Flow Only，且不能做修改。如果初始阶段没有选择flow only，是不可能选择flow only计算阶段的。

当选择了这个，在孔压计算类型只有稳定状态地下水渗流选项可选。

###### 5.4.1.6塑性 nil-phase

如果K~0~ procedure生成了不平衡的初始应力场或者出现了摩尔库伦塑性点，需要采用这个阶段进行修正。它是一个不施加额外荷载的塑性计算阶段，在这个计算阶段完成后，应力场将会是平衡的并且所有的应力遵循failure condition。

如果产生的应力场过于不平衡，这个阶段可能救不回来。发生情况：K~0~运用到steep slope上，这种情况，应采用gravity loading procedure。

确保在这个阶段计算产生的位移不会影响后续计算很重要，通过在后面的计算阶段中选择Reset displacement to zero参数即可。

##### 5.4.2塑性计算

塑性计算是用来开展弹塑性变形分析，这个==没必要考虑孔压随时间的变化==，如果Updated mesh参数没有被选择，计算根据小变形理论进行。正常塑性阶段的stiffness matrix基于original undeformed geometry，这个计算类型在大多数的实际岩土应用都合适。

尽管可以指定时间间隔，塑性计算并不考虑时间的效应，除了使用软土蠕变模型考虑饱和粘土的快速加载，塑性计算可以使用完全不排水性状的限制性条件，使用undrained（A\B\C）在材料数据集设置。另一方面，进行完全排水分析可以评估长期的沉降，这可以给出最终情形的较为准确的预测，尽管没有准确的荷载历史并且没有详细的处理固结过程。

弹塑性变形分析，需要暂时忽略排水性状（A\B不排水），可以选择Ignore undr参数，这种情况下，不考虑stiffness of water。这个参数并部影响设置了C类排水的材料

改变geometry configuration 同样可以重新定义水边界条件并重新计算孔压，在scientific Manual有相关理论介绍和细节。

在塑性计算中荷载可以通过改变load combination，stress state，weight，strength或stiffness of elements修改，通过改变荷载和geometry configuration或孔压分布在staged construction。在这种情况下，最终阶段达到的总的load level 通过指定新的geometry 和load configuration或pore pressure distribution在staged construction mode。

孔压计算类型：

- phreatic
- use pressures from previous phase
- steady state groundwater flow

##### 5.4.3固结分析计算

当有必要分析==饱和粘土的随时间的excess pore pressures的发展和dissipation==时，采用这个分析类型。plaxis可以进行真的弹塑性固结分析。一般来说，没有附加荷载的固结分析在不排水的塑性计算之后进行固结分析时施加荷载也是可行的。但是，需要注意当failure situation 达到，since the iteration process may not converge in such a situation。

固结分析要求额外的边界条件生成excess pore pressures。

==提示：==在plaxis中，孔压被分成steady-state pore pressure和excess pore pressures。稳定状态孔压根据每个阶段设置到到土层的水条件产生，而超净孔压是由于不排水A\B或者固结产生的。plaxis的固结分析仅会超净孔压。除了考虑排水类型设定，固结分析还考虑相应的定义在材料数据集地下水tab的permeability。固结分析并不影响undrained（C），因为不允许产生超净孔压。

固结分析的可选选项：

- 分布施工：固结和simultaneous loading从改变荷载组合，应力状态，重量，强度或者单元刚度来看，在分布施工阶段通过改变荷载和几何体配置来激活。指定时间间隔参数是有必要的，这个时间间隔指定了应用在当前计算阶段的整个固结阶段。荷载在这个时间间隔下线性增加到指定的大小。开始增加时间时基于在数值控制参数Numerical control子菜单的First time step 参数。分布施工阶段在没有额外荷载的特定的固结时期时可选择。

- 没有额外荷载的固结，直到超净孔压衰减到指定的最小值，通过最小超净孔压参数指定的。默认最小孔压设置为1 stress unit，也可自己设定，这个值是一个绝对值，可以设置为压力 也可拉力tensile stress。时间间隔选项不可设置，不确定什么时候衰减到。applied first time increment在numerical control参数子菜单的first time step参数。

- 没有额外荷载的固结，直到达到特定的固结程度，通过指定固结度参数。默认固结度设置为90%，可改变。时间间隔参数不可设置。同上最后一句。==提示==：固结度官方定义目标沉降除以最终沉降，但是plaxis定义为目标最小孔压除以初始最大孔压。

##### 5.4.4渗流-变形同步分析

当有必要分析==饱和和部分饱和的土体由于水力边界条件随时间改变而引起的变形和孔压的同步发展==时，可以进行这个分析。使用这个分析的例子有：大坝后部库水位的draw down，容易受到tidal wave和partially draind excavation 影响的坝岸以及建筑场地的排水。和固结分析直接影响超净孔压不同的是，该分析直接作用于总的孔隙水压力（稳定孔压 + 超净孔压）然而，为了和其他计算类型一致，稳定状态孔压基于计算阶段的后期的水力条件进行计算，计算阶段后期enables the back-calculation of excess pore pressures from the total pore water pressures。

该分析考虑潜水面上部非饱和区的非饱和土的性状和suction。这是最高级和真实的分析类型，考虑了非饱和区的reduced permeability 和饱和度degree of saturation，因此忽略suction选项在这个计算类型下不可选。

一般来说，该分析考虑潜水面上部的非饱和区的非饱和性状和suction。然而非饱和区的positive pore stresses可以通过使用*Ignore suction*选项

当该分析阶段和temperature calculation，fully coupled Thermo-Hydro-Mechanical（THM）分析是可选的

提示：在这个分析阶段不能使用updated mesh 选项

##### 5.4.5安全计算分析

在plaxis安全计算分析是计算global 安全系数factor。这个选项可以在一般tabsheet中选择为单独的计算阶段。

在这个安全性计算方法中，土体剪切强度参数$tan\phi$和C以及抗拉强度连续衰减直到结构失稳发生。膨胀角原则上来说不会被这个应力折减（phi/c reduction procedure）过程影响。但是膨胀角不能大于摩擦角，当摩擦角减少到和膨胀角一样大时，再减少就会随着摩擦角的减小而同等减小。如果使用了界面，界面强度以同样的方式减小。结构对象如plates和锚杆同样可以减小。

total multiplier$\sum{Msf}$用来在分析中给定阶段去定义土体强度参数的值：

$\sum{Msf}=\frac{tan_{\psi input}}{tan\psi reduced}=\frac{c_{input}}{c_{reduced}}=\frac{S_{u,input}}{S_{u,reduced}}=\frac{Tensile strength_{input}}{Tensile strength_{reduced}}$

其中带有input下标的参考的是在材料数据集中设置的属性，带有reduced下标的参考的是在分析过程中是同的衰减值。$\sum{Msf}$计算开始时设置为1用来设置材料强度为输入值。

安全计算分析使用*load advancement number of steps procedure*来进行。incremental multiplier Msf是用来指定第一计算步的强度衰减的变化值，默认设置为0.1，这是一个比较合适的开始值。强度参数自动连续的衰减直到所有的Additional steps计算完毕。默认下计算步设置为100，如果有必要也可给定10000。必须总是检查是否最终步是否导致了完全发展的failure mechanism。如果满足条件，则factor of safety通过下面公式计算：

$SF=\frac{available \ strength}{strength\ at\ failure}=value\ of\ \sum{Msf}\ at\ failure$

安全系数计算主要的结果是failure mechanism和相应的$\sum{Msf}$这就是安全系数。随着输出程序Project 菜单的不同选项选中，每个计算步的$\sum{Msf}$值在计算信息窗口显示。建议使用曲线选择窗口去查看整个阶段的$\sum{Msf}$的发展，在这个曲线查看器中，可以查看在变形不断发展时，是否可以达到一个恒定不变的值，换种方式说就是failure mechanism是否完全发展，如果没有达到，则这个计算需要加大计算步进行再次计算

==提示==：在Numerical control参数子菜单定义的数值参数不应该对principal results产生较大影响。然而，在安全分析过程安全系数接近一的情况，其他的输出结果（结构内力）可能对数值参数很敏感，特别是：Desired min and max number of iteration这两个参数

为了抓取更准确的结构失效，需要使用Arc-control参数，同时需要使用不超过1%Tolerated error。当使用默认iteration 参数，所有要求complied with。

==提示==：当使用没有Arc-length control的安全计算时，reduction factor 不能降低，会导致过大的安全系数出现。为了准确获取结构的failure，需要使用足够fine mesh。当有高级土体模型混合了安全计算，这些模型将都会表现为标准的摩尔库伦模型，因为stress-dependent stiffness behaviour和硬化效应在这个分析里面不考虑。in this case，在计算阶段的开始基于起始刚度计算刚度并保持常量不变直到计算阶段完成。==注意==：当使用修改Cam-clay模型和Sekiguchi-Ohta模型时，强度一点也不会衰减，因为这两个模型没有cohesion和friction angle。

在安全计算采用的强度折减方法计算的安全系数和基于Limit Equilibrium Method（LEM）slip-circle analysis的传统稳定性分析方法相同类似。想要了解更详细的强度折减方法（phi/c reduction），可以参考Brinkgreve&Bakker的文章。

==提示==：在Jointed Rock model的条件下，所有plane平面的强度将会按$\sum{Msf}$减少。在NGI-ADP模型和UDCAM-S模型条件下，所有不排水强度are reduced by $\sum{Msf}$。在Cam-clay模型和Sekiguchi-Ohta模型条件，强度一点也不会衰减。安全分析使用用户定义的土体模型时，参数衰减值需要in the code为用户定义模型详细指定

==提示==：当考虑suction时，可以为Fully coupled flow-deformation analysis获取更加贴合实际的安全系数值。这个值一般涞水比忽略suction的常规安全系数更高，因此解读这个值时需要注意

==提示==：一般来说，不建议有使用了residual （残余的，剩余的）强度的structural elements的模型进行安全计算。在安全计算过程中，有structural elements的模型会进行stresses和forces的重新分布，会导致structural forces的增加。当结构的强度达到了，而且这个结构有一个低于正常值的residual strength（残余强度），结构force将会减小到residual strength。这可能会导致不平衡的情况，进而导致安全系数不合实际的减小。

在安全计算以下的参数时可选的：

- 逐渐减小强度参数来进行的安全分析。默认first calculation step的衰减增量为0.1，可修改。
- 衰减强度参数直到目标的total multiplier达到进行的安全分析。程序首先会尝试探寻一个安全值（大于目标值）然后在passing the target之前把值给last step，然后将做最后一个step去达到目标值。

###### **enhanced safety analysis**

对于安全计算，所有的soil cluster和interface都自动考虑进来。增强的安全分析使得施加强度折减到soil cluster 和结构变得可能，此外，还可以指定特定的soil cluster和结构不参与应力折减过程。

安全计算通过强度折减的multiplier来控制。进行enhanced safety analysis的目的如下：

- 沿着边坡的表层soil sluster可以不进行强度折减过程来避免不真实的浅层failure mechanism
- 和soil cluster一样，倘若这些结构表现为elastoplatic并且有limit strength，所有的结构可以选中进行应力折减。
- 需要慎重的为不同阶段手动选择特定的soil cluster和structure，进行enhanced safety analysis。

#### 5.5计算阶段定义

在工程实际运用中，一个项目被分成多个项目阶段，在plaxis的模拟中，也将计算过程分成多个计算阶段。

计算阶段可以是：

1. 特定时间的特定荷载的激活

2. 建造阶段的模拟

3. 固结阶段的引入

4. 安全系数的计算

每个计算阶段可以被分成多个计算步，由于土体的非线性性状要求荷载小部分阶段的施加（被称为荷载step）。在大多数情况，指定计算阶段最终达到的情形是sufficient，plaxis会考虑合适的荷载step到sub-division。

construction stages可以在flow condition和staged construction modes选择。

==first calculation phase（initial phase）是初始模型的初始应力场的计算，通过Gravity loading或者K~0~来计算==。相应的，也可指定计算只在考虑地下水流动。初始阶段之后，之后的阶段可以用户自定义，每个阶段计算类型是必选的。

#### 5.6塑性计算





## 后处理