title: PHP实现进程管理
date: 2016-07-15 00:07:40
tags:
---
> php如何实现简单的进程管理?

1. 首先定义一个要执行的文件`work.php`，比较简单，死循环输入当前的时间．当然你也可以自己写一个．
```
<?php
while (true) {
        echo date('Y-m-d H:i:s').PHP_EOL ;
        sleep(3);

    }
```

2. 开始写进程管理文件`workProcess.php`，根据自己的需要进行优化．
```
<?php
/**
 * 管理php进程 
 */

class workProcess {
    const PHP_PATH = '/usr/bin/php';
    const PHP_LOG = './php.log';

    private static $process = null;

    //命令必须在这个里面
    private static $cmd_str = array('start','stop','restart');

    //脚本执行次数
    private static $times = 0;

    /**
     * main 
     * 主要程序,用来控制php进程开启，结束，重启
     * @param mixed $argv 
     * @static
     * @access public
     * @return void
     */
    public static function main($argv) {
        //需要执行的php文件
        $filename = isset($argv[1]) ? $argv[1] : '';
        $cmd = isset($argv[2]) ? $argv[2] : '';
        $num = isset($argv[3]) ? $argv[3] : 0;
        if(empty($filename)) {
            echo "process is not null \n";
            exit;
        }
        if(empty($cmd)) {
            echo "cmd not exists \n";
            exit;
        }

        self::$process = $filename;
        self::$times = $num;
        //检测命令
        if(!in_array($cmd,self::$cmd_str)) {
            echo "cmd is not wrong! \n";
            exit;
        }
        self::$cmd();
    }

    /**
     * start
     * 启动程序
     * @static
     * @access public
     * @return void
     */
    public static function start() {
        for($i=0; $i <=self::$times; $i++ ) {
            self::runWork();
        }
        echo "start done~!\n";
        exit;
    }

    /**
     * stop
     * 终止程序
     * @static
     * @access public
     * @return void
     */
    public static function stop() {
        $pids = self::getPid();
        foreach($pids as $pid){
            self::killWork($pid);
            sleep(1);
        }
        echo "stop done~!\n";
        exit;
    }

    /**
     * restart 
     * 重启一个php程序
     * @static
     * @access public
     * @return void
     */
    public static function restart() {

        $pids = self::getPid();
        $count = count($pids);
        for($i=0; $i < $count; $i++ ) {
            self::killWork($pids[$i]);
            self::runWork();
            sleep(1);
        }
        $times = self::$times;
        if( $count < $times) {
            for($j=0; $j< $times-$count; $j++) {
                self::runWork();
                sleep(1);
            }
        }
        echo "restart done~!";
        exit;
    }

    /**
     * runWork
     * 运行程序
     * @static
     * @access private
     * @return void
     */
    private static function runWork() {
        $cmd_str = sprintf("%s %s >> %s &", self::PHP_PATH, self::$process, self::PHP_LOG);
        echo $cmd_str."\n";
        shell_exec($cmd_str);
    }

    /**
     * killWork
     * 杀死一个程序
     * @static
     * @access private
     * @return void
     */
    private static function killWork($pid) {
        $cmd_str = "kill -9  ".$pid;
        shell_exec($cmd_str);
        echo "kill {$pid} \n";
    }

    /**
     * getPid
     * 获取进程id
     * @static
     * @access private
     * @return void
     */
    private static function getPid(){
        $cmd_str  = "ps -ef |grep ".basename(self::$process)." |grep -v grep |grep -v ".basename(__FILE__)." |awk '{print $2}'";
        $pid = shell_exec($cmd_str);

        $pids = array_filter(explode("\n",$pid));
        return $pids;
    }
}

workProcess::main($argv);

```

3. 程序执行
    - 启动5个进程：php workProcess.php work.php start 5
    - 重启进程：php workProcess.php work.php restart
    - 停止进程：php workProcess.php work.php stop

参考来源：
看了阿印的博客，觉得思路不错，自己也模仿写了一个，嘿嘿．．
