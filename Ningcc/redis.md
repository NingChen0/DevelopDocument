# redis 

> remote dictionary server   (远程字典服务)
>
> 内存存储、持久化、效率高，高速缓存

#### 什么是NoSQL

> Not Only SQL (不仅仅是SQL)

*  泛指只非关系型数据库 
* 方便拓展（数据之间没有关系
* 大数据量高性能（redis一秒读取11万，写8万次）
* 数据类型是多样性的（不需要事先设计数据库，随取随用）
* 没有固定查询语言
* 键值对存储，列存储，文档存储，图形数据库

#### 01. redis的安装

* 下载安装包，将安装包解压至/opt/文件夹下

  ```shell
  tar -zxvf redis-7.4.0.tar.gz -C /opt/
  ```

* 进入redis

  ```shell
  cd redis-7.4.0
  ```

* 安装环境依赖

  ```shell
  yum insatll gcc-c++
  # 查看gcc版本
  gcc -v
  # 输入make命令配置Gcc需要的文件,用于自动化构建过程，根据makefile的文本文件中指令来构建目标文件
  make
  #输入make install 确认所有安装完成
  make install
  ```

  ![QQ_1722600494035](C:\Users\Administrator\AppData\Local\Temp\QQ_1722600494035.png)

* reids默认安装路径

  ```shell
  /usr/local/bin
  ```

* 修改配置文件

  ```shell
  #将/opt/redis/下的redis.conf配置文件复制到/usr/local/bin/新建文件夹/下
  cp xxx  xxx
  # 修改配置文件
  vi redis.config
  #
  daemonize yes  #守护进程，修改为yes后可在后台运行
  requirepass 123456 # 访问redis的密码
  bind 127.0.0.0 #只能在本地访问，修改后可在任意主机访问
  ```

* 启动redis

  ```shell
  #在/usr/local/bin目录下，通过指定配置文件启动
  redis-server ./Nconfig/redis.conf 
  ```

  查看进程

  ![QQ_1722602609181](C:\Users\Administrator\AppData\Local\Temp\QQ_1722602609181.png)

* 测试

  ```shell
  # 在/usr/local/bin/ 
  redis-cli -p 6379
  ping
  #pong
  #设置了登录密码需要验证
  auth 123456
  [root@centos bin]# redis-cli -p 6379
  127.0.0.1:6379> ping
  (error) NOAUTH Authentication required.
  127.0.0.1:6379>auth 123456
  OK
  127.0.0.1:6379> ping
  PONG
  ```

* 关闭redis服务

  ```shell
  127.0.0.1:6379>shutdown
  exit
  ```


#### redis 基础知识

> redis 默认有16个数据库，通过select [index] 进行切换

> redis数据类型（常用）
>
> string(字符串)、hash)(哈希)、list(列表)、set(集合)、zser(sorted set:有序集合)

* 命令格式

  ```shell
  # 类型命令 key 参数数据
    set     name ning
  ```


* 键（key）

  ```shell
  127.0.0.1:6379> set ningkey hahha
  OK
  127.0.0.1:6379> get ningkey
  "hahha"
  127.0.0.1:6379> del ningkey
  (integer) 1     #del 删除命令，如果删除成功，输出（integer）1，反之0
  ```

  ```shell
  expire key seconds  #为key设置过期时间，以秒计 
  #查看当前key所剩余的生命时间
  ttl key    #若返回-2,则证明已过期
  #查看key的类型
  type key
  #检查key是否存在
  exist key
  #删除key
  del key
  #查看当前库里所有的key
  keys *
  #清空所有库的key
  flushall
  #将默认库0的key移动到1号库
  move key 1
  #select 切换数据库
  select 2  #切换到2号数据库
  ```

#### 五种数据结构类型

##### -- String(字符串)类型

* 字符串

  ```shell
  #追加字符串
  append key "hello
  #查看键值的字符串长度
  strlen key   # 返回字符串长度
  #追加的字符串含有空隔应用双引号引起来
  #设置键值自增
  incr key  #指定key的值自增1，返回结果
  #自减
  decr key #自减1
  #设置自增步长
  incrby key 10 #指定key的值增加10
  decrby key 7 #减少7
  #截取字符串
  getrange key start end #
  getrange key 3 5 #截取key值第3-5个字符
  getrange key 3 -1 #截取第3至最后一个字符
  #替换字符串
  setrange key 3 xx # 替换第4位开始xx位字符
  127.0.0.1:6379[1]> get name1
  "nihaohello"
  127.0.0.1:6379[1]> setrange name1 3 haha
  (integer) 10
  127.0.0.1:6379[1]> get name1
  "nihhahallo"
  # setex (set with expire) #设置过期时间
  # setnx (set if not exist) #不存在再设置（在分布式硕中常常使用）
  
  27.0.0.1:6379[1]> setex myke1 30 "hello"  # 设置myke1,值为hello 30s后过期
  OK
  127.0.0.1:6379[1]> ttl myke1
  (integer) 16
  127.0.0.1:6379[1]> ttl myke1
  (integer) 11
  127.0.0.1:6379[1]> get myke1
  (nil)
  127.0.0.1:6379[1]> ttl myke1
  (integer) -2
  127.0.0.1:6379[1]> setnx mykey2 "no exist" #如果mykey2不存在，则创建，存在则也不覆盖
  (integer) 1
  127.0.0.1:6379[1]> keys *
  1) "mykey2"
  2) "name1"
  127.0.0.1:6379[1]> get mykey2
  "no exist"
  
  #批量设置键值对
  127.0.0.1:6379> mset key1 ha key2 ye key3 ning #key 值 key 值 key 值
  OK
  127.0.0.1:6379> keys *
  1) "key3"
  2) "key2"
  3) "key1"
  127.0.0.1:6379> mget key1 key2 key3 # 批量获取值，
  1) "ha"
  2) "ye"
  3) "ning"
  msetnx key1 v1 k4 k4 #如果不存在则创建，是一个原子性操作，要么一起成功，要么一起失败
  #组合命令 getset
  127.0.0.1:6379> getset db mysql #先获取db的值再设置新值
  (nil)
  127.0.0.1:6379> get db
  "mysql"
  127.0.0.1:6379> getset db redis
  "mysql"
  127.0.0.1:6379> get db
  "redis"
  ```

* 使用场景

  > 计数器
  >
  > 统计多单位的数量
  >
  > 粉丝数
  >
  > 对象缓存存储

##### -- list（集合） 类型

* > 所有的list命令都是以l开头

* 将一个值或多个值插入到列表头部

  ```shell
  lpush list one  # lpush --==left push,也有rpush，从右侧添加
  ```

* 将一个值或多个值插入到list尾部

  ```shell
  rpush list four #right push 
  # 查看list 数据
  127.0.0.1:6379> lrange list 0 -1
  1) "three"
  2) "two"
  3) "one"
  4) "four"
  ```

* 通过区间获取具体的值

  ```shell
  lrange list 0 -1
  ```

* 移除list的第一个元素

  ```shell
  lpop list 
  ```

* 移除最后一个元素

  ```shell
  rpop list
  ```

* 通过下标获取list的某一个值

  ```shell
  lindex list 0
  # 实测
  127.0.0.1:6379> lindex list 0
  "three"
  127.0.0.1:6379> lindex list 2
  "one"
  ```

* 获取list的长度

  ```shell
  llen list 
  ```

* 移除list中指定个数的值

  ```shell
  lrem list 1 one 
  lrem list 2 three # lrem --==list remove 2:移除两个three
  # 实测
  127.0.0.1:6379> lrange list 0 -1
  1) "three"
  2) "two"
  3) "one"
  4) "four"
  5) "three"
  127.0.0.1:6379> lrem list 2 three
  (integer) 2
  127.0.0.1:6379> lrange list 0 -1
  1) "two"
  2) "one"
  3) "four"
  127.0.0.1:6379> 
  ```

* 截取list 中下标为1到3之间元素的集合

  ```shell
  127.0.0.1:6379> lrange mylist 0 -1
  1) "four"
  2) "one"
  3) "two"
  4) "three"
  127.0.0.1:6379> ltrim mylist 0 2 # ltrim :让list只保留指定区间内的元素，删除three
  OK
  127.0.0.1:6379> lrange mylist 0 -1
  1) "four"
  2) "one"
  3) "two"
  ```

* 更新list 中下标为0 的值为zero，，如果下标0的值不存在，则报错

  ```shell
  lset mylist 0 zero
  # 实测
  127.0.0.1:6379> lrange mylist 0 -1
  1) "four"
  2) "one"
  3) "two"
  127.0.0.1:6379> lset mylist 0 zero
  OK
  127.0.0.1:6379> lrange mylist 0 -1
  1) "zero"
  2) "one"
  3) "two"
  ```

* 将某一个值添加到某一元素之前或之后

  ```shell
  # 插入到one元素之前
  linsert mylist before one zero-after
  #插入到one元素之后
  linsert mylist after one two-befor
  #实测·
  127.0.0.1:6379> linsert mylist before one zero-after
  (integer) 4
  127.0.0.1:6379> lrange mylist 0 -1
  1) "zero"
  2) "zero-after"
  3) "one"
  4) "two"
  #插入after
  127.0.0.1:6379> linsert mylist after one two-before
  (integer) 5
  127.0.0.1:6379> lrange mylist 0 -1
  1) "zero"
  2) "zero-after"
  3) "one"
  4) "two-before"
  5) "two"
  ```

##### set (集合)类型

* >  set 中的值不能重复，不能重读
  >
  > set 开头都是s
  >
  > set是无序不重复的，list是有序

* 添加元素

  ```shell
  sadd myset hello
  ```

* 查看set集合中所有元素

  ```bash
  smembers myset
  # 实测
  127.0.0.1:6379> smembers myset
  1) "hello"
  2) "ning"
  3) "mike"
  127.0.0.1:6379> 
  ```

* 查看set中是否存在某元素

  ```shell
  sismember myset world 
  #实测
  127.0.0.1:6379> sismember myset hello
  (integer) 1
  127.0.0.1:6379> sismember myset world
  (integer) 0
  # 存在是返回1 ，不存在返回0
  ```

* 获取set中元素的个数值

  ```shell
  scard myset # s card:cardinality : 集合的基数/势
  #实测
  127.0.0.1:6379> scard myset
  (integer) 3
  ```

* 移除set中指定元素

  ```shell
  srem myset hello
  #实测
  127.0.0.1:6379> srem myset hello
  (integer) 1
  127.0.0.1:6379> smembers myset
  1) "ning"
  2) "mike"
  ```

  
