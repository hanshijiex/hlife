# xhprof性能分析

标签（空格分隔）： php

---
###介绍

#### XHprof是一个分层PHP性能分析工具。它报告函数级别的请求次数和各种指标，包括阻塞时间，CPU时间和内存使用情况。并且带有基于浏览器的性能分析用户界面。

### 安装

1、下载并解压
下载地址：http://pecl.php.net/package/xhprof
截止目前，最新版本依旧是2013年发布的0.9.4，下载解压命令如下：
wget http://pecl.php.net/get/xhprof-0.9.4.tgz
tar -zxf xhprof-0.9.4.tgz
解压后，会解压在当前目录下的子目录：xhprof-0.9.4

2、编译和安装
cd xhprof-0.9.4/extension/
使用phpize配置扩展，
yourpath/php/bin/phpize
使用php-config进行配置
./configure --with-php-config=yourpath/php/bin/php-config
make && make install
将xhprof.so拷贝到扩展目录

3、启用xhprof扩展
找到php.ini文件，添加内容如下：
[xhprof]
extension=xhprof.so
xhprof.output_dir=yourpath/xhprof
xhprof.output_dir目录可以任意指定，是用来存放xhprof生成的数据文件
重启php-fpm: php/sbin/php-fpm restart
php -m | grep xhprof 检查是否安装成功

4、php.conf中添加 location
把解压目录xhprof-0.9.4下的子目录 xhprof_html 和 xhprof_lib，复制到 webroot/xhprofApi 下

### 配置入口
在要监控的项目代码的入口文件，添加xhprof配置
```
xhprof_enable(XHPROF_FLAGS_CPU + XHPROF_FLAGS_MEMORY);
/* 你的代码入口 */
$xhprof_data = xhprof_disable();
 添加第4步里复制到 /webroot/xhprofApi 下的代码引用 
include_once xhprofApi . "utils/xhprof_runs.php";
include_once  xhprofApi . "utils/xhprof_lib.php";
$xhprof_runs = new XHProfRuns_Default();
$run_id = $xhprof_runs->save_run($xhprof_data, "xhprof");
```

### 通过浏览器访问
http://your_host:your_port/xhprofApi/xhprof_html/index.php

### XHProf输出字段说明
```
Overall Summary：
Total Incl. Wall Time (microsec): 执行后总共用的时间
Total Incl. CPU (microsecs)：执行后CPU占用的时间
Total Incl. MemUse (bytes)：执行后不包含内部函数所占内存占用的容量
Total Incl. PeakMemUse (bytes): 执行后包含内部函数占用内存的容量
Number of Function Calls：执行后调用方法（函数）的次数
函数粒度输出：
Incl. Wall Time(microsec)包含内部函数的时间，单位微秒
Excl. Wall Time(microsec)不包含内部函数的时间，单位微秒
Incl. CPU (microsecs)：包含内部函数 CPU 花费的时间，单位微秒
Excl. CPU (microsec)：不包含内部函数 CPU 花费的时间，单位微秒
Incl. MemUse (bytes)：包含内部函数所占内存，单位字节
Excl. MemUse (bytes)：不包含内部函数所占内存，单位字节
Incl. PeakMemUse (bytes)：包含内部函数所占内存峰值，单位字节
Excl. PeakMemUse (bytes)：不包含内部函数所占内存峰值，单位字节及所占百分比(%)
```
还有更直观的图形分析
