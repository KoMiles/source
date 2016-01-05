title: 记一次switch/case在判断时导致的bug.
date: 2016-01-05 20:03:59
tags: php
---
如何使用，请参考PHP官网 [switch的用法](http://php.net/manual/zh/control-structures.switch.php)
我在开发过程中遇到了下面的问题。
## 请看下面这段代码：
```
<?php
$type = 0;
switch($type) {
    case $type >= 0 && $type < 2:
        $price = 100;
        break;
    case $type>= 3 && $type < 5:
        $price = 200;
        break;
    case $type>= 6 && $type < 9:
        $price = 300;
        break;
    default:
        $price = 800;
        break;
}
echo $price;
```
乍一看，很简单呀，因为满足条件 ``$type >= 0 && $type < 2`` ,所以答案是100 呀；
其实不是这样子的,返回的是200.是不是很坑啊？

## 分析代码
- 官网中特别提示，switch/case作的是松散比较，什么是松散比较，请参考[__松散比较__](http://php.net/manual/zh/types.comparisons.php#types.comparisions-loose)
- ``$type >= 0 && $type < 2`` 这个返回 true;
- ``$type>= 3 && $type < 5`` 这个返回 false;
so,上面代码可以转化为这样：
```
<?php
$type = 0;
switch(0) {
    case true:
        $price = 100;
        break;
    case false:
        $price = 200;
        break;
    case false:
        $price = 300;
        break;
    default:
        $price = 800;
        break;
}
echo $price;
```
- 这样你就容易理解了。
看到这里，你就明白为什么返回是200。
but,我想要的是100呀，怎么办呢？

## 解决问题
- 可以按照下面这样的方式解决：
```
<?php
$type = 0;
switch(true) {
    case $type >= 0 && $type < 2:
        $price = 100;
        break;
    case $type>= 3 && $type < 5:
        $price = 200;
        break;
    case $type>= 6 && $type < 9:
        $price = 300;
        break;
    default:
        $price = 800;
        break;
}
echo $price;
```
- 看到了吧，switch 后边的变量改成true后，程序就正常了，终于输出了自己想要的100.
泪奔 啊~~
