


## 开发工具
* https://netbeans.org/
* eclipse
    * 调试（必须实现远程的命令行调试，类似c++的gdb命令调试）：
        + xdebug： http://www.xdebug.org   源码安装，参考README
        + /etc/php.ini  增加： zend_extension="/usr/local/lib/xdebug.so"
        + 参考：
            1. http://blog.csdn.net/huoyin/article/details/7540781
            2. http://blog.csdn.net/huoyin/article/details/7538738
            3. http://my.oschina.net/XYleung/blog/102739
* Vim+XDebug调试PHP
    * http://blog.csdn.net/dc_726/article/details/8809696
    * Linux 环境使用vim搭建php IDE： http://www.cnblogs.com/lufangtao/archive/2012/12/31/2840431.html
* PHP执行流程跟踪工具 phptrace

## 配置
* Nginx：
    * /usr/local/nginx/sbin/nginx -s reload
    * php-fpm
        * FastCGI 进程管理器（FPM）: http://www.php.net/manual/zh/install.fpm.php， FPM（FastCGI 进程管理器）用于替换 PHP FastCGI 的大部分附加功能，对于高负载网站是非常有用的。PHP5.3.3已经集成php-fpm了，不再是第三方的包了。PHP-FPM提供了更好的PHP进程管理方式，可以有效控制内存和进程、可以平滑重载PHP配置，比spawn-fcgi具有更多优点，所以被PHP官方收录了。在./configure的时候带 –enable-fpm参数即可开启PHP-FPM。
        * nginx php-fpm安装配置： http://www.nginx.cn/231.html
        * http://php-fpm.org/download/
        * yum install php-fpm
        * /etc/php-fpm.conf
        * /etc/php-fpm.d/www.conf
        * /etc/rc.d/init.d/php-fpm restart   or    /usr/sbin/php-fpm
        * /usr/local/nginx/conf/nginx.conf，修改配置以支持php-fpm
    * ~表示nginx要用正则表达式，  ^代表正则表达式的开始，$代表正则表达式的结尾
* /etc/php.ini
* /opt/remi/php56/root/etc/php.ini
    * extension=/usr/lib64/libsqlite3.so.0
    * extension=/opt/remi/php56/root/usr/lib64/php/modules/pdo_sqlite.so

## php websocket
* 源码： https://github.com/swoole/swoole-src
* 文档： http://wiki.swoole.com/wiki/page/p-server.html
* Yii
    * 下载地址： http://www.yiiframework.com/download/   目前使用的是1.1
    * 中文文档地址： http://www.yiiframework.com/doc/guide/zh_cn
    * 建立第一个 Yii 应用： http://www.yiiframework.com/doc/guide/1.1/zh_cn/quickstart.first-app
* yii 路由： 文件、方法、参数
* PHP扩展开发  http://www.open-open.com/lib/view/open1392188698114.html
    * Download http://php.net/get/php-5.6.23.tar.gz
* Apache httpd + php
    * yum install php
    * yum install php-mysql  # 支持mysql扩展
    * /etc/httpd/conf/httpd.conf
        * AddType application/x-httpd-php .php
        * DirectoryIndex index.php index.html
    * DocumentRoot "/var/www/html"
* /etc/php-fpm.d/www.conf


## 问题
1. Unable to resolve the request "site/error".   yii  <= index.php 设置错误
2. ThinkPHP: 项目目录不可写，目录无法自动生成！  或者   PHP Fatal error:  Unknown: Failed opening required   <=====   把selinux关闭  setenforce 0
3. ThinkPHP3.2.3 验证码错误， backend-admin/Application/Runtime/Logs/Admin/xxxx.log 打印日志：  NOTIC: [2] session_start(): open(/var/lib/php/session/sess_7eemmr8nqn9o2gj90et13lb0p7, O_RDWR) failed: Permission denied (13) /var/www/html/test/ThinkPHP/Common/functions.php   => 修改 /var/lib/php/session/ 的权限  <=  /etc/php-fpm.d/www.conf 中设置了 session的保存路径：   php_value[session.save_path] = /var/lib/php/session

