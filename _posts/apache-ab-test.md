title: Apache ab参数--压力测试
date: 2016-05-10 23:51:31
tags:
---
[Apache](https://blog.linuxeye.com/tag/apache/)附带的ab，它非常容易使用，ab可以直接在Web服务器本地发起测试请求。这至关重要，因为我们希望测试的服务器的处理时间，而不包含数据的网络传输时间以及用户PC本地的计算时间。

需要清楚的是，ab进行一切测试的本质都是基于HTTP，所以可以说它是对于Web服务器软件的黑盒性能测试，它获得的一切数据和计算结果，都可以通过HTTP来解释。

 

如果没有安装，在运行时会提示安装。

- 查看ab版本：

```
wangkongming@Vostro /etc/apache2 $ ab -V
This is ApacheBench, Version 2.3 <$Revision: 1528965 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/
```

- 举个例子：

```
wangkongming@Vostro /etc/apache2 $ ab -n 10 -c 10 http://www.baidu.com/
This is ApacheBench, Version 2.3 <$Revision: 1528965 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking www.baidu.com (be patient).....done


Server Software:        Apache-Coyote/1.1
Server Hostname:        www.baidu.com
Server Port:            80

Document Path:          /
Document Length:        521 bytes　// 请求的页面大小

Concurrency Level:      10 //并发量
Time taken for tests:   3.467 seconds //测试总共耗时
Complete requests:      10 //完成的请求
Failed requests:        9　//失败的请求
   (Connect: 0, Receive: 0, Length: 9, Exceptions: 0)
Total transferred:      880759 bytes　//总共传输数据量
HTML transferred:       871360 bytes
Requests per second:    2.88 [#/sec] (mean) //每秒钟的请求量。（仅仅是测试页面的响应速度）
Time per request:       3466.517 [ms] (mean) //等于 Time taken for tests/(complete requests/concurrency level) 即平均请求等待时间（用户等待的时间）
Time per request:       346.652 [ms] (mean, across all concurrent requests) //等于 Time taken for tests/Complete requests 即服务器平均请求响应时间 在并发量为1时 用户等待时间相同
Transfer rate:          248.12 [Kbytes/sec] received //平均每秒多少K，即带宽速率

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:       31   34   2.6     35      39
Processing:     2 1962 909.4   2298    3432
Waiting:        2  336 528.4     67    1528
Total:         33 1996 910.9   2337    3466

Percentage of the requests served within a certain time (ms)
  50%   2337
  66%   2467
  75%   2497
  80%   2588
  90%   3466
  95%   3466
  98%   3466
  99%   3466
 100%   3466 (longest request)
```

- 参数说明：

-n 10 表示总请求数为10，共发出了10次请求
-c 10 表示并发用户数为10,同时有10个用户访问
http://www.baidu.com/ 表示这些请求的目标URL (注意，目标地址后面一定要加结束的反斜杠/)

 

- 关注的参数：

```

Requests per second：每秒的请求量，所谓的吞吐率。【这个值越小越好

Time per request: 3466.517 [ms] (mean) 　即平均请求等待时间，也是吞吐率（用户等待的时间）　mean表示平均值

Time per request: 346.652 [ms] (mean, across all concurrent requests) //服务器平均请求响应时间 在并发量为1时 用户等待时间相同　【这个值越大越好】

```
简单总结下：

Requests per second　的值越小越好，Time per request　的值越大越好

 

 

参考资料：

[http://blog.itpub.net/29773961/viewspace-1470071/](http://blog.itpub.net/29773961/viewspace-1470071/)

[https://blog.linuxeye.com/124.html](https://blog.linuxeye.com/124.html)

 
