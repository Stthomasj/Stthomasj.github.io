---
layout: post
title:  "NOSQL-redis"
date:   2017-11-17 23:33:00 +0800
categories: jekyll update
---


**Redis**是一个开源的使用ANSI C语言编写、支持网络、*可基于内存亦可持久化的日志型、Key-Value数据库*，并提供多种语言的API。特点概述：
 
- **储存类型多** ：它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，redis支持各种不同方式的排序。与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。
- **提供的API丰富** ：Redis 是一个高性能的key-value数据库。 redis的出现，很大程度补偿了memcached这类key/value存储的不足，在部 分场合可以对关系数据库起到很好的补充作用。它提供了Java，C/C++，C#，PHP，JavaScript，Perl，Object-C，Python，Ruby，Erlang等客户端，使用很方便；
- **主从复制** ：Redis支持主从同步。数据可以从主服务器向任意数量的从服务器上同步，从服务器可以是关联其他从服务器的主服务器。这使得Redis可执行单层树复制。存盘可以有意无意的对数据进行写操作。由于完全实现了发布/订阅机制，使得从数据库在任何地方同步树时，可订阅一个频道并接收主服务器完整的消息发布记录。同步对读取操作的可扩展性和数据冗余很有帮助。

-------------------

## Redis安装【linux】
	shell：
		$ wget http://download.redis.io/releases/redis-4.0.2.tar.gz
		$ tar xzf redis-4.0.2.tar.gz
		$ cd redis-4.0.2
		$ make
####安装完毕后

将运行文件存放至统一路径 例如/usr/local/redis;
			
	makedir	/usr/local/redis
	src]$ cp redis-server /usr/local/redis/
	src]$ cp redis-cli /usr/local/redis/
	src]$ cp ../redis.conf /usr/local/redis/	

### 运行
	
	cd /usr/local/redis
	./redis-server redis.conf//指明配置文件

会c出现几个Warning
1)  WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.	
解决方法 echo 1024 >/proc/sys/net/core/somaxconn

somaxconn 是什么？ 为什么要修改它
对于一个TCP连接，Server与Client需要通过三次握手来建立网络连接.当三次握手成功后,我们可以看到端口的状态由LISTEN转变为ESTABLISHED,接着这条链路上就可以开始传送数据了.
　　每一个处于监听(Listen)状态的端口,都有自己的监听队列.监听队列的长度,与如下两方面有关:
　　- somaxconn参数.
　　- 使用该端口的程序中listen()函数.
　somaxconn : 定义了系统中每一个端口最大的监听队列的长度,这是个全局的参数,默认值为128
　限制了接收新 TCP 连接侦听队列的大小。对于一个经常处理新连接的高负载 web服务环境来说，默认的 128 太小了。大多数环境这个值建议增加到 1024 或者更多。 服务进程会自己限制侦听队列的大小(例如 sendmail(8) 或者 Apache)，常常在它们的配置文件中有设置队列大小的选项。大的侦听队列对防止拒绝服务 DoS 攻击也会有所帮助。
>（[拒绝服务 DoS 攻击](https://baike.baidu.com/item/%E6%8B%92%E7%BB%9D%E6%9C%8D%E5%8A%A1%E6%94%BB%E5%87%BB/421896?fr=aladdin)：拒绝服务攻击即是攻击者想办法让目标机器停止提供服务，是黑客常用的攻击手段之一。其实对网络带宽进行的消耗性攻击只是拒绝服务攻击的一小部分，只要能够对目标造成麻烦，使某些服务被暂停甚至主机死机，都属于拒绝服务攻击。）

2)Warning：overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to/etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
意思是：overcommit_memory参数设置为0！在内存不足的情况下，后台程序save可能失败。建议在文件 /etc/sysctl.conf 中将overcommit_memory修改为1。

>1.   overcommit_memory是什么？  
      overcommit_memory是一个内核对内存分配的一种策略。 具体可见/proc/sys/vm/overcommit_memory下的值
      
>2.  overcommit_memory有什么作用？  
     overcommit_memory取值又三种分别为0， 1， 2
	    overcommit_memory=0， 表示内核将检查是否有足够的可用内存供应用进程使用；如果有足够的可用内存，内存申请允许；否则，内存申请失败，并把错误返回给应用进程。
	    overcommit_memory=1， 表示内核允许分配所有的物理内存，而不管当前的内存状态如何。
	    overcommit_memory=2， 表示内核允许分配超过所有物理内存和交换空间总和的内存
>3.  overcommit_ratio是什么？ 
	    当overcommit_memory=2的时候，它一般是代表的是系统中总的内存的百分比
	
>4. 虚拟内存
	  CommitLimit = SwapTotal + MemTotal * overcommit_ratio
	  总的虚拟内存 = 总的交换分区 + 总的物理内存 * overcommit_ratio
	  这些信息可以到cat  /proc/meminfo中看到，  可以通过上述的计算公式可以计算就可以获得系统的CommitLimit的值

>5. Committed_AS:是什么？
  Committed_AS代表了系统已经用了的内存情况

>6.  overcommit_memory的系统默认值是0， overcommit_ratio的默认值是50。可以实际中会遇到相同配置的电脑，相同的程序一个可以申请到内存，一个不可一申请到。这时候可以看看overcommit_memory的值是否被修改了。


3)Warning:you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix thisissue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain thesetting after a reboot. Redis must be restarted after THP is disabled.
意思是：你使用的是透明大页，可能导致redis延迟和内存使用问题。执行 echo never > /sys/kernel/mm/transparent_hugepage/enabled 修复该问题。
临时解决方法：
echo never > /sys/kernel/mm/transparent_hugepage/enabled。

#### 以上问题永久解决方法将其写入/etc/rc.local文件中。

#### 修改相应的配置信息

1.修改redis.conf中的daemonize选项 将no改为yes；（当你还在狐疑，执行了server无法执行其他操作只能ctrl+c的时候）
	
2.为了使redis-server可在任意目录下执行,修改dir ./为绝对路径->/usr/local/redis.
	   
3.修改appendonly为yes,指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘,如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的,所以有的数据会在一段时间内只存在于内存中。默认为no 
	  
4.将redis添加到自启动中  echo "/usr/local/bin/redis-server /etc/redis.conf" >> /etc/rc.d/rc.local 
	 
5.启动redis redis-server /etc/redis.conf。
	
6.查看redis是否己启动 ps -ef | grep redis	  
	  
7.开放redis端口
		service iptables stop   
		vi /etc/sysconfig/iptables   
		iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 6379 -j ACCEPT
		service iptables restart  
#### PHP-Redis 安装
	
		1. wget https://github.com/phpredis/phpredis/archive/3.1.4.tar.gz
		2. tar -zxvf 3.1.4.tar.gz
		3. mv phpredis-3.1.4 /usr/local/;//统一管理
		4. cd phpredis-3.1.4 
		5. /usr/local/php/bin/phpize #php安装后的路径 使用PHP生成configure
		6. ./configure --with-php-config=/usr/local/php/bin/php-config
		7.  make && make install

完成后会出现下面路径

	/usr/local/php/lib/php/extensions/no-debug-non-zts-20090626/
	
修改PHP配置文件

	vi /usr/local/php/etc/php.ini  #编辑配置文件，在最后一行添加以下内容
	extension="redis.so"

重启服务 PHP PHP-FPM

	sudo service nginx restart
	sudo /etc/init.d/php-fpm restart
	
检查是否成功
	
	 php -r "phpinfo();"


### REDIS 集群待续......
