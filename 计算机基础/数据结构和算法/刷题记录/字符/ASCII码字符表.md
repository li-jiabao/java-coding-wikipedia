# ASCII字符表

## 小写字母

a-z：数字范围97-122

## 大写字母

A-Z：数字范围65-90

## 数字字符

0-9：数字范围48-57

## 字符的比较和运算

字符在Java中是可以直接进行大小的比较和加减乘除的运算的，其实就是ASCII子码表，不同的字符对应不同的一个数组，范围0-127

判断是否是大写字母：`c>='A' && c<='Z'`，大写字母转换为小写字母：`(char) (c+32)`

判断是否是小写字母：`c>='a' && c<='z'`，小写字母转换为大写字母：`(char) (c-32)`

判断是否是数字：`c>='0' && c<='9'`

