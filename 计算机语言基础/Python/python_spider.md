---
title: python_spider
author: lijiabao
date: 2020-11-13 10:31:22.349762100 +0800
category: python_notebook
categories: python_notebook
tags: python_notebook
---
# python爬虫知识

## python爬虫解析方法
### 1.`bs4`的`BeautifulSoup`:
```python
from bs4 import BeautifulSoup


soup = BeautifulSoup('html', 'lxml')

soup.prettify()   # 将html内容进行美化

# 一、beautifulsoup有四大种类，Tag，NavigableString,BeautifulSoup，Comment

a = soup.tag_name   # 1.利用tag_name来进行查找
a.name,a.attrs  # 分别得到Tag的名字和属性，后者为一个字典类型，soup的name是[document]获取属性可用a['class']或者a.get('class'),还可对其进行修改

a.string    # 2. 是用来获取标签内的文本

# 3. beautifulsoup是特殊的tag对象

# 4. comment是特殊类型的navigablestring，可以通过type(a.string) == bs4.element.Comment来判断是否为注释对象

# 二、遍历文档树
a.contents   # 1. 将tag的子节点以列表方式输出
a.children   # 返回的是所有子节点，可以遍历获取所有子节点

a.descendants   # 2. 返回所有子孙节点，遍历获取

a.string    # 3. 返回节点内容，只当节点tag唯一才可获取，多个tag时返回None

a.strings   # 4. 获取多个内容，遍历获取
a.stripped_strings   # 获取多个内容，且为去除多余空白的字符串

a.parent    # 5. 父节点

a.parents   # 6. 所有父节点

a.next_sibling, a.previous_sibling  # 7. 兄弟节点

a.next_siblings,a.previous_siblings   # 8. 所有兄弟节点

a.next_element, a.precious_element  # 9. 不分层次的下一节点
a.next_elements,a.previous_elements  # 所有的下一节点，遍历即可

# 三、搜索文档树
soup.find_all(name, attrs, recusive, text, **kwargs)
# 1. name:查所有名字为name的tag
#可传字符串
# 正则表达式（查找匹配正则的标签）
# 列表（查找列表内的标签），True（查找所有节点标签）
#方法，方法接受的是标签，函数返回True则选择，为False则跳过，# tag.has_attr()返回bool

# 2. keyword参数，如果指定名字参数不是内置参数名，搜索时会将该参数当作指定名字的tag属性来搜索
soup.find_all(id='hh')   # 会搜索tag的id属性满足条件的tag

# 3. text参数可用来搜索文档的字符串内容，与name参数可选值一样

# 4. limit参数限制搜索的结果数量

# 5. recursive默认检索当前tag的所有子孙节点，如果只搜索直接子节点，可设置该参数为False

soup.find()  # 和find_all一样只是直接返回第一个结果
soup.find_parents(),soup.find_parent()  # 查找的时所有父辈节点
soup.find_next_siblings()   # 找所有后面的兄弟节点
soup.find_previous_siblings()   # 查找所有之前的兄弟节点
soup.find_all_next(),soup.find_next()  # 查找当前节点后面的所有节点
soup.find_all_previous(),soup.find_previous()   # 之前的所有节点

# 四、css选择器
soup.select()    # 查找的内容都是列表形式，获取节点内容用get_text()方法
soup.select('p')   # 标签名查找
soup.select('.sister')   # 类名查找
soup.select('#link')    # id名查找
soup.select('p#link')   # 组合查找， 不在同一节点空格分开
soup.select('head > title')   # 直接子节点查找
soup.select('p[class="sister"]')   # 属性查找
```
*********

### 2. `xpath`解析方法:
```python
import lxml
from lxml import etree


html_text = '<html>'
html = lxml.etree.HTML(html_text)
result = html.xpath('//div/text()')  # 选择div节点的文字,,xpath的text()选取文本

# 一、节点选择：
# 1.  /  从根节点选取，选择直接子节点，//选子孙节点
# 当子节点没有文本而子孙节点有，使用/a/text()并不能选取子孙节点的文本
# 可用这个过滤不需要的文本，/a//text()则可获取子孙节点的文本

# 2.  //  从匹配的当前节点选择节点不考虑他们的位置
# 3.   .   选取当前节点  ...  选择当前节点的父节点
#  4.   @   选取属性

#  div   表示选取div节点的所有子节点
# /div    选取根元素div
#  /div/a   选取所有div子元素的所有a节点
#  //a    选取所有的a节点，不管具体位置
#  div//a  选取所有div子节点的所有a节点不管位置
#   //@lang   选取lang的属性值

# 二、 谓语用来查找特定节点或者含有特定值的节点

# /div/a[1]   选取第一个a节点
# /div/a[last()]   选取最后一个元素
#  /div/a[last()-1]  选取倒数第二个a节点
# /div/a[position()>2]   选取位置第二个之后的节点
# //title[@lang]  选取有lang属性的title
#  //title[@lang="zh"]  选取lang属性为zh的title
#  //title[contains(@lang, 'en')]  选取lang属性包含en的节点
# //title[contains(@lang, 'en') and @title='hhh']   多属性条件匹配还有or，mod等运算符可以使用
# /div/li[price > 35]   选取属div的a的price元素大于35的

# 三、选取未知节点，可使用通配符

# *匹配任何节点  @*匹配任何属性的节点  node()匹配任何类型的节点
# 利用  |  符号可以选取满足多个条件的内容如：
#  //div/a[@*]/* | //div/span/@title,,,选择满足两者之一的就选择出来

# 四、节点轴的利用进行选择

# //div[@class='sister']/ancestor::*选择满足条件的div节点的祖先节点，双冒号后再接一个选择器
# //div/attribute::*   返回节点的所有属性
# /div/child::*  获取所有子节点，或者::a[@class='link'],选择子节点a属性为link的a
# div/a/descendant::*  获取所有子孙节点
#  div/a/following::*  选取所有a之后的节点
# div/a/follwing-sibling::*  所有之后的同级节点
```
| 轴名称             |                           结果                           |
| :----------------- | :------------------------------------------------------: |
| ancestor           |          选取当前节点的所有先辈（父、祖父等）。          |
| ancestor-or-self   |  选取当前节点的所有先辈（父、祖父等）以及当前节点本身。  |
| attribute          |                 选取当前节点的所有属性。                 |
| child              |                选取当前节点的所有子元素。                |
| descendant         |         选取当前节点的所有后代元素（子、孙等）。         |
| descendant-or-self | 选取当前节点的所有后代元素（子、孙等）以及当前节点本身。 |
| following          |       选取文档中当前节点的结束标签之后的所有节点。       |
| namespace          |             选取当前节点的所有命名空间节点。             |
| parent             |                  选取当前节点的父节点。                  |
| preceding          |       选取文档中当前节点的开始标签之前的所有节点。       |
| preceding-sibling  |             选取当前节点之前的所有同级节点。             |
| self               |                      选取当前节点。                      |

| 例子                   |                             结果                             |
| :--------------------- | :----------------------------------------------------------: |
| child::book            |          选取所有属于当前节点的子元素的 book 节点。          |
| attribute::lang        |                  选取当前节点的 lang 属性。                  |
| child::*               |                  选取当前节点的所有子元素。                  |
| attribute::*           |                   选取当前节点的所有属性。                   |
| child::text()          |                选取当前节点的所有文本子节点。                |
| child::node()          |                  选取当前节点的所有子节点。                  |
| descendant::book       |                选取当前节点的所有 book 后代。                |
| ancestor::book         |                选择当前节点的所有 book 先辈。                |
| ancestor-or-self::book | 选取当前节点的所有 book 先辈以及当前节点（如果此节点是 book 节点） |
| child::*/child::price  |              选取当前节点的所有 price 孙节点。               |

| 运算符 | 描述           | 实例                      |                            返回值                            |
| :----- | :------------- | :------------------------ | :----------------------------------------------------------: |
| \|     | 计算两个节点集 | //book \| //cd            |             返回所有拥有 book 和 cd 元素的节点集             |
| +      | 加法           | 6 + 4                     |                              10                              |
| -      | 减法           | 6 - 4                     |                              2                               |
| *      | 乘法           | 6 * 4                     |                              24                              |
| div    | 除法           | 8 div 4                   |                              2                               |
| =      | 等于           | price=9.80                | 如果 price 是 9.80，则返回 true。如果 price 是 9.90，则返回 false。 |
| !=     | 不等于         | price!=9.80               | 如果 price 是 9.90，则返回 true。如果 price 是 9.80，则返回 false。 |
| <      | 小于           | price<9.80                | 如果 price 是 9.00，则返回 true。如果 price 是 9.90，则返回 false。 |
| <=     | 小于或等于     | price<=9.80               | 如果 price 是 9.00，则返回 true。如果 price 是 9.90，则返回 false。 |
| >      | 大于           | price>9.80                | 如果 price 是 9.90，则返回 true。如果 price 是 9.80，则返回 false。 |
| >=     | 大于或等于     | price>=9.80               | 如果 price 是 9.90，则返回 true。如果 price 是 9.70，则返回 false。 |
| or     | 或             | price=9.80 or price=9.70  | 如果 price 是 9.80，则返回 true。如果 price 是 9.50，则返回 false。 |
| and    | 与             | price>9.00 and price<9.90 | 如果 price 是 9.80，则返回 true。如果 price 是 8.50，则返回 false。 |
| mod    | 计算除法的余数 | 5 mod 2                   |                              1                               |
*********


###  3. `css`使用`pyquery`:
```python
from pyquery import PyQuery as pq
doc = pq('html')   # 还可pq(url='')或者pq(filr_path)
# 然后就可以利用doc进行css的选择
result = doc('p a#link')
for i in result.items():
  i.text()  # 返回的是标签内部的内容
  i.attr('attr_name')   # 返回属性名或者i.attr.attr_name
  i.html()
# div,p  选择div和p元素
# div p  选取div内的所有p
# div>p  选取所有父元素为div的p
```

| 选择器                                                       | 例子                  | 例子描述                                            | CSS  |
| :----------------------------------------------------------- | :-------------------- | :-------------------------------------------------- | :--- |
| [.*class*](https://www.w3school.com.cn/cssref/selector_class.asp) | .intro                | 选择 class="intro" 的所有元素。                     | 1    |
| [#*id*](https://www.w3school.com.cn/cssref/selector_id.asp)  | #firstname            | 选择 id="firstname" 的所有元素。                    | 1    |
| [*](https://www.w3school.com.cn/cssref/selector_all.asp)     | *                     | 选择所有元素。                                      | 2    |
| [*element*](https://www.w3school.com.cn/cssref/selector_element.asp) | p                     | 选择所有 <p> 元素。                                 | 1    |
| [*element*,*element*](https://www.w3school.com.cn/cssref/selector_element_comma.asp) | div,p                 | 选择所有 <div> 元素和所有 <p> 元素。                | 1    |
| [*element* *element*](https://www.w3school.com.cn/cssref/selector_element_element.asp) | div p                 | 选择 <div> 元素内部的所有 <p> 元素。                | 1    |
| [*element*>*element*](https://www.w3school.com.cn/cssref/selector_element_gt.asp) | div>p                 | 选择父元素为 <div> 元素的所有 <p> 元素。            | 2    |
| [*element*+*element*](https://www.w3school.com.cn/cssref/selector_element_plus.asp) | div+p                 | 选择紧接在 <div> 元素之后的所有 <p> 元素。          | 2    |
| [[*attribute*\]](https://www.w3school.com.cn/cssref/selector_attribute.asp) | [target]              | 选择带有 target 属性所有元素。                      | 2    |
| [[*attribute*=*value*\]](https://www.w3school.com.cn/cssref/selector_attribute_value.asp) | [target=_blank]       | 选择 target="_blank" 的所有元素。                   | 2    |
| [[*attribute*~=*value*\]](https://www.w3school.com.cn/cssref/selector_attribute_value_contain.asp) | [title~=flower]       | 选择 title 属性包含单词 "flower" 的所有元素。       | 2    |
| [[*attribute*\|=*value*\]](https://www.w3school.com.cn/cssref/selector_attribute_value_start.asp) | [lang\|=en]           | 选择 lang 属性值以 "en" 开头的所有元素。            | 2    |
| [:link](https://www.w3school.com.cn/cssref/selector_link.asp) | a:link                | 选择所有未被访问的链接。                            | 1    |
| [:visited](https://www.w3school.com.cn/cssref/selector_visited.asp) | a:visited             | 选择所有已被访问的链接。                            | 1    |
| [:active](https://www.w3school.com.cn/cssref/selector_active.asp) | a:active              | 选择活动链接。                                      | 1    |
| [:hover](https://www.w3school.com.cn/cssref/selector_hover.asp) | a:hover               | 选择鼠标指针位于其上的链接。                        | 1    |
| [:focus](https://www.w3school.com.cn/cssref/selector_focus.asp) | input:focus           | 选择获得焦点的 input 元素。                         | 2    |
| [:first-letter](https://www.w3school.com.cn/cssref/selector_first-letter.asp) | p:first-letter        | 选择每个 <p> 元素的首字母。                         | 1    |
| [:first-line](https://www.w3school.com.cn/cssref/selector_first-line.asp) | p:first-line          | 选择每个 <p> 元素的首行。                           | 1    |
| [:first-child](https://www.w3school.com.cn/cssref/selector_first-child.asp) | p:first-child         | 选择属于父元素的第一个子元素的每个 <p> 元素。       | 2    |
| [:before](https://www.w3school.com.cn/cssref/selector_before.asp) | p:before              | 在每个 <p> 元素的内容之前插入内容。                 | 2    |
| [:after](https://www.w3school.com.cn/cssref/selector_after.asp) | p:after               | 在每个 <p> 元素的内容之后插入内容。                 | 2    |
| [:lang(*language*)](https://www.w3school.com.cn/cssref/selector_lang.asp) | p:lang(it)            | 选择带有以 "it" 开头的 lang 属性值的每个 <p> 元素。 | 2    |
| [*element1*~*element2*](https://www.w3school.com.cn/cssref/selector_gen_sibling.asp) | p~ul                  | 选择前面有 <p> 元素的每个 <ul> 元素。               | 3    |
| [[*attribute*^=*value*\]](https://www.w3school.com.cn/cssref/selector_attr_begin.asp) | a[src^="https"]       | 选择其 src 属性值以 "https" 开头的每个 <a> 元素。   | 3    |
| [[*attribute*$=*value*\]](https://www.w3school.com.cn/cssref/selector_attr_end.asp) | a[src$=".pdf"]        | 选择其 src 属性以 ".pdf" 结尾的所有 <a> 元素。      | 3    |
| [[*attribute**=*value*\]](https://www.w3school.com.cn/cssref/selector_attr_contain.asp) | a[src*="abc"]         | 选择其 src 属性中包含 "abc" 子串的每个 <a> 元素。   | 3    |
| [:first-of-type](https://www.w3school.com.cn/cssref/selector_first-of-type.asp) | p:first-of-type       | 选择属于其父元素的首个 <p> 元素的每个 <p> 元素。    | 3    |
| [:last-of-type](https://www.w3school.com.cn/cssref/selector_last-of-type.asp) | p:last-of-type        | 选择属于其父元素的最后 <p> 元素的每个 <p> 元素。    | 3    |
| [:only-of-type](https://www.w3school.com.cn/cssref/selector_only-of-type.asp) | p:only-of-type        | 选择属于其父元素唯一的 <p> 元素的每个 <p> 元素。    | 3    |
| [:only-child](https://www.w3school.com.cn/cssref/selector_only-child.asp) | p:only-child          | 选择属于其父元素的唯一子元素的每个 <p> 元素。       | 3    |
| [:nth-child(*n*)](https://www.w3school.com.cn/cssref/selector_nth-child.asp) | p:nth-child(2)        | 选择属于其父元素的第二个子元素的每个 <p> 元素。     | 3    |
| [:nth-last-child(*n*)](https://www.w3school.com.cn/cssref/selector_nth-last-child.asp) | p:nth-last-child(2)   | 同上，从最后一个子元素开始计数。                    | 3    |
| [:nth-of-type(*n*)](https://www.w3school.com.cn/cssref/selector_nth-of-type.asp) | p:nth-of-type(2)      | 选择属于其父元素第二个 <p> 元素的每个 <p> 元素。    | 3    |
| [:nth-last-of-type(*n*)](https://www.w3school.com.cn/cssref/selector_nth-last-of-type.asp) | p:nth-last-of-type(2) | 同上，但是从最后一个子元素开始计数。                | 3    |
| [:last-child](https://www.w3school.com.cn/cssref/selector_last-child.asp) | p:last-child          | 选择属于其父元素最后一个子元素每个 <p> 元素。       | 3    |
| [:root](https://www.w3school.com.cn/cssref/selector_root.asp) | :root                 | 选择文档的根元素。                                  | 3    |
| [:empty](https://www.w3school.com.cn/cssref/selector_empty.asp) | p:empty               | 选择没有子元素的每个 <p> 元素（包括文本节点）。     | 3    |
| [:target](https://www.w3school.com.cn/cssref/selector_target.asp) | #news:target          | 选择当前活动的 #news 元素。                         | 3    |
| [:enabled](https://www.w3school.com.cn/cssref/selector_enabled.asp) | input:enabled         | 选择每个启用的 <input> 元素。                       | 3    |
| [:disabled](https://www.w3school.com.cn/cssref/selector_disabled.asp) | input:disabled        | 选择每个禁用的 <input> 元素                         | 3    |
| [:checked](https://www.w3school.com.cn/cssref/selector_checked.asp) | input:checked         | 选择每个被选中的 <input> 元素。                     | 3    |
| [:not(*selector*)](https://www.w3school.com.cn/cssref/selector_not.asp) | :not(p)               | 选择非 <p> 元素的每个元素。                         | 3    |
| [::selection](https://www.w3school.com.cn/cssref/selector_selection.asp) | ::selection           | 选择被用户选取的元素部分                            |      |

************

### 4. 正则表达式解析：

#### re.compile 函数

compile 函数用于编译正则表达式，生成一个正则表达式（ Pattern ）对象，供 match() 和 search() 这两个函数使用。

语法格式为：

```
re.compile(pattern[, flags])
```

参数：

- **pattern** : 一个字符串形式的正则表达式
- **flags** : 可选，表示匹配模式，比如忽略大小写，多行模式等，具体参数为：
  1. **re.I** 忽略大小写
  2. **re.L** 表示特殊字符集 \w, \W, \b, \B, \s, \S 依赖于当前环境
  3. **re.M** 多行模式
  4. **re.S** 即为 **.** 并且包括换行符在内的任意字符（**.** 不包括换行符）
  5. **re.U** 表示特殊字符集 \w, \W, \b, \B, \d, \D, \s, \S 依赖于 Unicode 字符属性数据库
  6. **re.X** 为了增加可读性，忽略空格和 **#** 后面的注释
#### 修饰符：

| 修饰符 | 描述                                                         |
| :----- | :----------------------------------------------------------- |
| re.I   | 使匹配对大小写不敏感                                         |
| re.L   | 做本地化识别（locale-aware）匹配                             |
| re.M   | 多行匹配，影响 ^ 和 $                                        |
| re.S   | 使 . 匹配包括换行在内的所有字符                              |
| re.U   | 根据Unicode字符集解析字符。这个标志影响 \w, \W, \b, \B.      |
| re.X   | 该标志通过给予你更灵活的格式以便你将正则表达式写得更易于理解。 |

#### re.match函数：

re.match 尝试从字符串的起始位置匹配一个模式，如果不是起始位置匹配成功的话，match()就返回none。

**函数语法**：

```
re.match(pattern, string, flags=0)
```

#### re.search方法：

re.search 扫描整个字符串并返回第一个成功的匹配。

函数语法：

```
re.search(pattern, string, flags=0)
```

#### re.match与re.search的区别

re.match只匹配字符串的开始，如果字符串开始不符合正则表达式，则匹配失败，函数返回None；而re.search匹配整个字符串，直到找到一个匹配。

#### 检索和替换：

Python 的 re 模块提供了re.sub用于替换字符串中的匹配项。

语法：

```
re.sub(pattern, repl, string, count=0, flags=0)
```

参数：

- pattern : 正则中的模式字符串。
- repl : 替换的字符串，也可为一个函数。
- string : 要被查找替换的原始字符串。
- count : 模式匹配后替换的最大次数，默认 0 表示替换所有的匹配。

#### findall：

在字符串中找到正则表达式所匹配的所有子串，并返回一个列表，如果没有找到匹配的，则返回空列表。

**注意：** match 和 search 是匹配一次 findall 匹配所有。

语法格式为：

```
findall(string[, pos[, endpos]])
```

参数：

- **string** : 待匹配的字符串。
- **pos** : 可选参数，指定字符串的起始位置，默认为 0。
- **endpos** : 可选参数，指定字符串的结束位置，默认为字符串的长度。

#### re.finditer：

和 findall 类似，在字符串中找到正则表达式所匹配的所有子串，并把它们作为一个迭代器返回。

```
re.finditer(pattern, string, flags=0)
```

#### re.split：

split 方法按照能够匹配的子串将字符串分割后返回列表，它的使用形式如下：

```
re.split(pattern, string[, maxsplit=0, flags=0])
```

参数：

| 参数     | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| pattern  | 匹配的正则表达式                                             |
| string   | 要匹配的字符串。                                             |
| maxsplit | 分隔次数，maxsplit=1 分隔一次，默认为 0，不限制次数。        |
| flags    | 标志位，用于控制正则表达式的匹配方式，如：是否区分大小写，多行匹配等等。参见：[正则表达式修饰符 - 可选标志](https://www.runoob.com/python/python-reg-expressions.html#flags) |

#### 正则表达式对象

##### re.RegexObject

re.compile() 返回 RegexObject 对象。

##### re.MatchObject

group() 返回被 RE 匹配的字符串。

- **start()** 返回匹配开始的位置
- **end()** 返回匹配结束的位置
- **span()** 返回一个元组包含匹配 (开始,结束) 的位置

匹配成功re.match方法返回一个匹配的对象，否则返回None。

我们可以使用group(num) 或 groups() 匹配对象函数来获取匹配表达式。

| 匹配对象方法 | 描述                                                         |
| :----------- | :----------------------------------------------------------- |
| group(num=0) | 匹配的整个表达式的字符串，group() 可以一次输入多个组号，在这种情况下它将返回一个包含那些组所对应值的元组。不输入num返回所有匹配字符组成的字符串 |
| groups()     | 返回一个包含所有小组字符串的元组，从 1 到 所含的小组号       |
|      groupdict()        |          返回所有命名分组的字典                                                    |

#### 正则表达式模式

模式字符串使用特殊的语法来表示一个正则表达式：

字母和数字表示他们自身。一个正则表达式模式中的字母和数字匹配同样的字符串。

多数字母和数字前加一个反斜杠时会拥有不同的含义。

标点符号只有被转义时才匹配自身，否则它们表示特殊的含义。

反斜杠本身需要使用反斜杠转义。

由于正则表达式通常都包含反斜杠，所以你最好使用原始字符串来表示它们。模式元素(如 r'\t'，等价于 '\\t')匹配相应的特殊字符。

下表列出了正则表达式模式语法中的特殊元素。如果你使用模式的同时提供了可选的标志参数，某些模式元素的含义会改变。

| 模式        | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| ^           | 匹配字符串的开头                                             |
| $           | 匹配字符串的末尾。                                           |
| .           | 匹配任意字符，除了换行符，当re.DOTALL标记被指定时，则可以匹配包括换行符的任意字符。 |
| [...]       | 用来表示一组字符,单独列出：[amk] 匹配 'a'，'m'或'k'          |
| [^...]      | 不在[]中的字符：[^abc] 匹配除了a,b,c之外的字符。             |
| re*         | 匹配0个或多个的表达式。                                      |
| re+         | 匹配1个或多个的表达式。                                      |
| re?         | 匹配0个或1个由前面的正则表达式定义的片段，非贪婪方式         |
| re{ n}      | 精确匹配 n 个前面表达式。例如， **o{2}** 不能匹配 "Bob" 中的 "o"，但是能匹配 "food" 中的两个 o。 |
| re{ n,}     | 匹配 n 个前面表达式。例如， o{2,} 不能匹配"Bob"中的"o"，但能匹配 "foooood"中的所有 o。"o{1,}" 等价于 "o+"。"o{0,}" 则等价于 "o*"。 |
| re{ n, m}   | 匹配 n 到 m 次由前面的正则表达式定义的片段，贪婪方式         |
| a\| b       | 匹配a或b                                                     |
| (re)        | 对正则表达式分组并记住匹配的文本                             |
| （?P<name>） | 对分组进行命名并开始该分组的匹配  |
|  （?P=name）  | 匹配与命名为name的分组匹配的相同的内容 |
| (?imx)      | 正则表达式包含三种可选标志：i, m, 或 x 。只影响括号中的区域。表示开启这种特殊匹配 |
| (?-imx)     | 正则表达式关闭 i, m, 或 x 可选标志。只影响括号中的区域。     |
| (?: re)     | 类似 (...), 但是不表示一个组                                 |
| (?imx: re)  | 在括号中使用i, m, 或 x 可选标志                              |
| (?-imx: re) | 在括号中不使用i, m, 或 x 可选标志                            |
| (?#...)     | 注释.                                                        |
| (?= re)     | 前向肯定界定符。如果所含正则表达式，以 ... 表示，在当前位置成功匹配时成功，否则失败。但一旦所含表达式已经尝试，匹配引擎根本没有提高；模式的剩余部分还要尝试界定符的右边。当re表达式在后面字符串存在时才从该处开始匹配 |
| (?! re)     | 前向否定界定符。与肯定界定符相反；当所含表达式不能在字符串当前位置匹配时成功 |
| (?> re)     | 匹配的独立模式，省去回溯。(?<=\+86)表示字符串前面包含+86才开始去匹配,(?<!\+86)表示前面没有+86才开始匹配 |
| \w          | 匹配字母数字及下划线                                         |
| \W          | 匹配非字母数字及下划线                                       |
| \s          | 匹配任意空白字符，等价于 [\t\n\r\f].                         |
| \S          | 匹配任意非空字符                                             |
| \d          | 匹配任意数字，等价于 [0-9].                                  |
| \D          | 匹配任意非数字                                               |
| \A          | 匹配字符串开始                                               |
| \Z          | 匹配字符串结束，如果是存在换行，只匹配到换行前的结束字符串。 |
| \z          | 匹配字符串结束                                               |
| \G          | 匹配最后匹配完成的位置。                                     |
| \b          | 匹配一个单词边界，也就是指单词和空格间的位置。例如， 'er\b' 可以匹配"never" 中的 'er'，但不能匹配 "verb" 中的 'er'。 |
| \B          | 匹配非单词边界。'er\B' 能匹配 "verb" 中的 'er'，但不能匹配 "never" 中的 'er'。 |
| \n, \t, 等. | 匹配一个换行符。匹配一个制表符。等                           |
| \1...\9     | 匹配第n个分组的内容。                                        |
| \10         | 匹配第n个分组的内容，如果它经匹配。否则指的是八进制字符码的表达式。 |

*************

## JavaScript逆向

### Ajax入口的寻找
有两种Ajax的逆向寻求入口的方式：
1. 全局搜索标志字符串
2. 设置Ajax断点
#### 全局搜索标志字符串
一些关键字符串通常可以用作寻找JavaScript混淆入口的依据，我们可以利用全局搜索的方式来找寻，通过关键字的搜寻到的结果取找寻是否满足入口条件，一般通过Ajax请求的请求参数里面去找关键字符，通过大体的观察参数重要性和参数的复杂度确定关键参数，然后进行全局多的搜索。往往这种加密混淆的入口都在js文件中。
********
#### XHR断点
有时候在js文件中，关键参数也是经过加密混淆或者特殊的编码处理之类的，这时候第一种方法就容易不奏效。XHR断点可以解决这种状况。

XHR断点就是在进行XHR请求的时候进入断点调试模式，JavaScript就会在发起Ajax请求的时候停住，然后就可以利用调用栈的逻辑一步一步的去找寻入口。

设置断点的方式：在`source`的`debugger`处找到`XHR/fetch Breakpoint`，点击`+`添加匹配的url内容。由于Ajax的接口形式是`api/movie/?limit=0`之类的，所以选取一段填入即可。

断点添加完毕之后刷新页面进入调试模式，然后查看需要找寻的特殊参数在何处。在`Call Stack`处记录了JavaScript方法的逐层调用的过程，可以在这个过程去找寻想要找寻的特殊参数，逐步查找最后也可以找到参数的构造位置。

XHR断点查找方法还是很好用的，通过对你想要判断的url的部分网址添加进断点，等运行到该处会停下，然后可在call stack处查看具体的运行过程，在这其中去找寻你需要找寻的特殊参数或者网站密码的加密逻辑。

***********
#### hook关键函数进行入口的找寻

***********
### 加密逻辑的找寻
在上一步的入口找寻找到特定参数的构造逻辑之后可以在js中去打断点看看生成逻辑和方法，之前打的XHR断点取消掉，因为只有一个断点。

打完新断点之后就可以查看其中可能的相关的变量的情况，通过查看变量的具体信息可以找到具体的位置和基本参数情况之类的。也可通过debugger里面的watch添加你想要看的变量名字也可以出现具体的参数情况和位置等信息。

通过里面的一些详细信息可以找到具体的构造逻辑，然后利用JavaScript的知识结合找出最终的构造逻辑。找出最终的构造逻辑之后就可以开始语言的爬取实现。

**********
### python语言的逻辑重现并爬取
利用之前找出来的逻辑利用python语言重现，然后获取到最后的目标信息，然后利用最终获取的目标信息取爬取想要的内容。

**********
## B站的密码加密逻辑
首先访问`https://passport.bilibili.com/login?act=getkey&r=Math.random()`获取`public_key`和`hash`值，然后`JSEncrypt`设置获取的`public_key`，再用获取到的`hash`值与密码进行拼接并进行`JSEncrypt`加密处理。最后再访问`https://passport.bilibili.com/web/login/v2`。表单传入需传入的参数访问即可。
```python
data = {
'captchaType': 6,
'username': 用户名,
'password': 加密之后的密码,
'keep': true,
'key': this.key,# 可不用的参数
'goUrl': 'https://www.bilibili.com'
}
```