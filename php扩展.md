# 为什么学习php扩展
 
 * 提升性能——c是静态编译的，对于复杂的运算，要比php执行效率高
 * 封装c类库——如果想用一些c、c++的类库，并且没有现成的php版本的轮子，就可以考虑自己封装一个
 * 帮助熟悉php的内核 

# 步骤
* ext_skel 
执行初始化，生成config.m4, xxx.c, php_xxx.h 等文件
使用./ext_skel --extname=${ext_name}
* config.m4 
用于配置依赖和安装方式的配置文件
* phpize
根据config.m4生成configure文件

# 生成一个打印hello world扩展
./ext_skel --extname=myfirst

```
To use your new extension, you will have to execute the following steps:

1.  $ cd ..
2.  $ vi ext/myfirst/config.m4
3.  $ ./buildconf
4.  $ ./configure --[with|enable]-myfirst
5.  $ make
6.  $ ./sapi/cli/php -f ext/myfirst/myfirst.php
7.  $ vi ext/myfirst/myfirst.c
8.  $ make
```
执行命令之后，生成myfirst目录，目录里面有自动生成的扩展的文件。
最主要的是config.m4、myfirst.c、php_myfirst.h

### config.m4
需要把下面PHP_ARG_ENABLE前面的dnl删掉

```
PHP_ARG_ENABLE(myfirst,
    [Whether to enable the "myfirst" extension],
    [  enable-walu        Enable "myfirst" extension support])

if test $PHP_MYFIRST != "no"; then
    PHP_NEW_EXTENSION(myfirst, myfirst.c, $ext_shared)
fi
```

myfirst.c

php执行的生命周期，如下图
![avatar](http://www.php-internals.com/images/book/chapt02/02-01-01-cgi-lift-cycle.png)
myfirst.c中的几个函数PHP_MINIT、PHP_MSHUTDOWN、PHP_RINIT、PHP_RSHUTDOWN、PHP_MINFO，分别对应
上图中的生命周期。

执行phpize

执行configure

执行make

然后module目录里面就会生成hext.so


# 常用函数
```
zend_parse_parameters

ZEND_FUNCTION(sample_hello_world)
{
    char *name;
    int name_len;
 
    if (zend_parse_parameters(ZEND_NUM_ARGS() TSRMLS_CC, "s",&name, &name_len) == FAILURE)
    {
        RETURN_NULL();
    }
	
    php_printf("Hello world");
    PHPWRITE(name, name_len);
    php_printf("!\n");
}

```

 
