title: 如何根据文件路径找到文件的目录
date: 2016-12-10 21:41:00
tags: php

---
### 1. 有下面这样的一个需求
把下面的两个路径转换为正确的文件目录
```
/mfw_project/test/demo/demo/../../demo.js
/mfw_project/test/./demo.js
```
转换成：
```
/mfw_project/test/demo.js
```

OK，开始搞！

- 第一个方案

{% codeblock lang:php %}
    $str = '/mfw_project/test/demo/demo/../../demo.js';
    //$str = '/mfw_project/test/./demo.js';
    $arr = explode('/',$str);
    //第一种情况
    $arr = array_filter($arr,function($value){
        if('.' === $value)
        {
            return false;
        }
        return true;
    });
    //第二种情况
    while(($index = array_search('..',$arr))!==false)
    {
        unset($arr[$index]);
        unset($arr[$index-1]);
        $arr = array_values($arr);
    }
    $str = implode('/',$arr);
    var_dump($str);

{% endcodeblock %}
- 第二个方案

{% codeblock lang:php %}
    $str = '/mfw_project/test/demo/demo/../../demo.js';
    //$str = '/mfw_project/test/./demo.js';
    function newStr($str)
    {
        //第一种情况
        $str = preg_replace('/([\w\d])+\/..\//','',$str);
        if(strpos($str,'..'))
        {
            return newStr($str);
        }
        //第二种情况
        if(strpos($str,'.'))
        {
            $str = str_replace("./",'',$str);
        }
        return $str;
    }
    $str = newStr($str);
    var_dump($str);
{% endcodeblock %}

以上两种方案都可以解决我们的需求。但是那种效率更高些呢？我们测试一下
### 测试执行效率

{% codeblock lang:php %}
<?php
$start_time = microtime(true);
$str = '/mfw_project/test/demo/demo/demo2/demo3/../../../../demo.js';
for($i=0;$i<100000;$i++)
{
    /*
    $arr = explode('/',$str);
    $arr = array_filter($arr,function($value){
        if('.' === $value)
        {
            return false;
        }
        return true;
    });
    while(($index = array_search('..',$arr))!==false){
        unset($arr[$index]);
        unset($arr[$index-1]);
        $arr = array_values($arr);
    }
    $str = implode('/',$arr);
    */
    $str = newStr($str);

}
    function newStr($str)
    {
        //第一种情况
        $str = preg_replace('/([\w\d])+\/..\//','',$str);
        if(strpos($str,'..'))
        {
            return newStr($str);
        }
        //第二种情况
        if(strpos($str,'.'))
        {
            $str = str_replace("./",'',$str);
        }
        return $str;
    }
echo $str."\n";

$end_time = microtime(true);
$run_time = $end_time - $start_time;
echo "程序运行".$run_time."秒";
exit;
{% endcodeblock %}
- 执行结果
{% codeblock lang:c %}
//递归方式
➜  ~ php 1.php
/mfw_project/test/demo.js
程序运行0.57571291923523秒
➜  ~ php 1.php
/mfw_project/test/demo.js
程序运行0.58426809310913秒
➜  ~ php 1.php
/mfw_project/test/demo.js
程序运行0.58362793922424秒

//截取方式
➜  ~ php 1.php
/mfw_project/test/demo.js
程序运行0.22979879379272秒
➜  ~ php 1.php
/mfw_project/test/demo.js
程序运行0.23150992393494秒
➜  ~ php 1.php
/mfw_project/test/demo.js
程序运行0.22574400901794秒
{% endcodeblock %}

可以看到代码，我们把程序循环执行了10万次，代码执行效率.
截取方式比递归方式，效率快了一倍多。
### 结论
- 尽量使用``PHP``自带函数
- ``PHP``中，用正则匹配比直接用``PHP``截取慢,不过第二种方式代码更美观一些
