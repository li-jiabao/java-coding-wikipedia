# CSS样式设计



## 元素的间距

### 同级元素之间的间距

设置使用margin，margin还可以具体指定是上下左右哪一部分的间距

```css
  margin: 0 auto;
/* 上面这一条命令用来设置某个元素的水平居中显示*/
/*下面的几个是用来设置同级元素之间的间距
  margin-bottom: 50px;
  margin-left: 10px;
  margin-right: 10px;
  margin-top: 10px;
  margin-inside: 10px;
  margin-outside: 10px;
```

### 父子间距

padding设置块级元素的上下左右的间隔

```css
padding:0px 0px 100px 100px
/* 表示和上一级元素的上右下左的间隔大小，也就是上部间隔0，右边间隔0，下部间隔100，左边间隔100*/


/* x偏移量 | y偏移量 | 阴影模糊半径 | 阴影扩散半径 | 阴影颜色 */
box-shadow: 2px 2px 2px 1px rgba(0, 0, 0, 0.2);
```

## CSS设置footer固定在底部

1. 使用relative和botoom 0px这一个来进行设置
2. 使用flex布局方式来设置
3. 使用min-height和height 以及 margin-top :-height来进行设置