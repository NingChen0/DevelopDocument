#  MySQL

### ----写在前面：

​	ubuntu mysql数据库密码：**123456**,@Power1234

#### 解决navicate远程连接mysql失败问题（代码1045）

> 注意：如果是云服务器需在安全组中开放端口号3306

```sql
1045 - Access denied for user ‘root’@‘222.173.220.236’ (using password: YES)
---原因分析：没有远程用户连接授权--
--授予’root’@'%'用户所有权限--
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
-- ON : 这部分指定了权限授予的范围。*.* 是一个通配符，表示所有数据库（第一个 *）和所有表（第二个 *）
-- @'%':指定了用户可以从任何主机连接到数据库服务器,% 是一个通配符，代表任何主机地址
--使用以下语句设置密码--
ALTER USER 'root'@'%' IDENTIFIED BY 'root@123456';
--刷新权限--
FLUSH PRIVILEGES;
--连接成功--
```

连接失败问题 -2002

```shell
# /etc/mysql/mysqld.c/mysqld. 未注释bind-address
```

**mycat2 连接数据源报错问题**

```shell
# mycat2 连接数据源报错问题
INFO   | jvm 1    | 2024/07/08 15:45:57 | Caused by: com.mysql.cj.exceptions.CJException: null,  message from server: "Host '192.168.75.135' is blocked because of many connection errors; unblock with 'mysqladmin flush-hosts'
#解决： 在数据源mysql中刷新host
--重置 MySQL 的 host cache，从而解除对 IP 地址的阻止，在命令行中输入
mysqladmin -u root -p flush-hosts
```









### 00 .mysql的安装

* 更新列表

  ```shell
  sudo apt-get update
  ```

+ 安装mysql

  ```shell
  ls /etc/my.cnf    // 检查是否有 配置文件
  rm -rf /etc/my.cnf    // 删除配置文件
  ```

* 进入官网下载安装包

  ```shell
  http://repo.mysql.com  # 下载后上传至服务器
  #上传后解压安装包
  rpm -ivh mysql57-community-release-el7.rpm
  ```

* 正式安装mysql

  ```shell
  yum install -y mysql-community-server
  ```

  > 可能会出现***\*GPG\****密钥过期  # 输入命令后重新安装rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023   (在官网最top位置可查看)

* 启动mysql

  ```shell
   systemctl start mysqld  
  ```

+ 首先要先使用无密码登录数据库

  ```shell
  mysql -u root -p  #提示密码错误，去/var/log/mysql.log中查看临时密码
  ```

* 修改密码

  ```mysql
  mysql> use mysql;
  #开启远程连接
  update user set host ="%" where user ='root';
  #更新权限
  flush privileges;
  #修改数据库密码。
  alter user 'root'@'%' identified with mysql_native_password by '123456';
  # 更新权限
  flush privileges;
  quit;
  ```

* 解决navicate连接问题

  ```shell
  sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf #注释
  ```

  ![image-20240531150054439](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240531150054439.png)

* 重启mysql

### 1. 函数

### 2.数据库的外键约束

1. **外键用来让两张表的数据之间建立连接，从而保证数据的一致性和完整性。**

2. 添加外键的语法

   * 创建数据库时添加

     ```sql
     create table 表名{
     	...
     	constraint 外键名称 foreign 外键字段名 references 主表（主表列名字） 
     }
     ```

   * 表已经创建时，添加外键

     ```sql
     alter table 表名 add constraint 外键名称 foreign key (外键字段名) references 主表 （主表列名）
     ```

3. 当两张表关联外键时，删除表中的数据会出现报错

4. 删除外键

   ```sql
   alter table 表名 drop foreign key 外键名称
   ```

5. 外键约束的更新行为

   * cascade （级联删除）：当父表删除/更新对应记录时，首先检查该纪录是否有对应外键，如果有则也删除/更新外键在子表中的记录。

     ```sql
     constraint 外键名称 foreign 外键字段名 references 主表（主表列名字）on update cascade on delete cascade
     --# 删除部门表中的部门数据时，相关部门的人员表中也会删除
     ```

   * set null ：当父表删除/更新对应记录时，检查是否有对应外键，如果有则设置子表中该外键值为null(要求外键允许取null)

     ```sql
     constraint 外键名称 foreign 外键字段名 references 主表（主表列名字）on update set null on delete set null
     ```

     

### 3.多表查询

准备表1emp(员工表)和表2dept(部门表)

![image-20240428222106007](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240428222106007.png)

![image-20240428222123356](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240428222123356.png)

1. 内连接

   * 内连接是查询两张表交集的部分

   隐式内连接

   ```sql
   select 字段列表 from 表1，表2 where 条件；
   ```

   显示内连接

   ```sql
   select 字段列表 from 表1 INNER jion 表二 on 连接条件；
   SELECT e.`name` as `姓名`,d.`name` as `部门` FROM emp e INNER JOIN dept d on (e.dept_id = d.id);
   
   ```

   ![image-20240428220217760](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240428220217760.png)

2. 外连接

   * 左外连接：查询**左表所有数据**包含表1和表2的交集的部分

     ```sql
     select 字段 from 表1 left [outer] jion 表2 on 条件；  （outer可省略）
     ```

     ![image-20240428221358520](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240428221358520.png)

     ##### 姓名：苏苏所在部门在部门表中未创建，所以为空

   * 右外连接：查询**右表所有数据**包含表1和表2的交集的部分

     ```sql
     select 字段 from 表1 right [outer] jion 表2 on 条件；
     ```

     ![image-20240428221535127](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240428221535127.png)

     #####  右表：人事部在左表中未分配有人员

3. 自链接 ：顾名思义，自己链接自己

   ```sql
   -- 当查询员工表的员工和直属领导的关系时，使用自链接当成两张表使用
   select * from 表1 jion 表2 on 条件；
   SELECT e.`name` as `姓名`,e2.`name` as `直属领导` FROM emp e  JOIN emp e2 on (e.managerid = e2.id);
   ```

   ![image-20240428222716001](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240428222716001.png)

4. 联合查询 ： 使用union 将两条sql语句返回的结果合并成一张表，去除重复的记录

   * union all : 对结果部分不去重

   ```sql
   SELECT e.`name` as `姓名`,e2.`name` as `直属领导` FROM emp e  JOIN emp e2 on (e.managerid = e2.id)
    union 
   SELECT emp.name ,dept.`name` as `部门名称` from emp RIGHT JOIN dept ON(dept.id=emp.dept_id);
   ```

   ![image-20240428224244328](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240428224244328.png)

### 4 . 子查询

1. 标量子查询： 子查询返回的是单个值（数字、字符串、日期），成为标量子查询

   * 常用操作符号： =   <>(不等于)  >=  <   <=

   ```sql
   -- 查询在韩寒入职之后的员工信息
   -- SELECT entrydate FROM emp WHERE `name` = '韩寒';
   SELECT * from emp WHERE entrydate >= (SELECT entrydate FROM emp WHERE name='韩寒');
   ```

2. 列子查询： 返回的结果是一列（可以是多行）

   * 常用操作符：IN 、NOT IN 、ANY（有任意一条满足即可）  、SOME（与any等同）  、ALL

   ```sql
   -- 查寻 销售部 和 市场部 的所有员工信息
   SELECT * FROM emp WHERE dept_id IN (SELECT id FROM dept WHERE `name` = '销售部' or name ='市场部');
   ```

   ```sql
   -- 查询比财务部所有人工资都高的员工信息
   SELECT salary FROM emp WHERE dept_id = (SELECT id FROM dept WHERE `name` = '财务部');
   SELECT * FROM emp WHERE salary > ALL(SELECT salary FROM emp WHERE dept_id = (SELECT id FROM dept WHERE `name` = '财务部'));
   ```

3. 行子查询： 返回的结果是一行（可以是多列）

   * 常用操作符： = 、<>  、IN 、NOT IN、

   ```sql
   -- 查询与'张无忌'的薪资相同及与其直属领导相同的员工信息
   SELECT salary , managerid FROM emp WHERE `name` = '张无忌';
   SELECT * FROM emp WHERE (`salary`,`managerid`) = (SELECT salary , managerid FROM emp WHERE `name` = '张无忌';);
   ```

4. 表子查询： 返回的结果是多列

   * 常用操作符： IN

   ```sql
   -- 查询入职日期在'2022-10-1'之后的员工信息及其部门
   SELECT e.*,dept.`name`AS '所属部门' FROM (SELECT * FROM emp WHERE entrydate > '2022-10-1') e LEFT JOIN dept on e.dept_id=dept.id;
   ```

   

### 4. 练习

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240504140802228.png" alt="image-20240504140802228" style="zoom: 80%;" />![image-20240504142636724](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240504142636724.png)

* 一、查询练习

  查询员工的姓名、年龄、职位、部门信息。

  ```sql
  SELECT emp.`name`, age,job,dept.`name` AS `所属部门` FROM emp INNER JOIN dept WHERE emp.dept_id=dept.id;
  ```

  查询年龄小于30岁的员工姓名、年龄、职位、部门信息。

  ```sql
  SELECT emp.`name`AS `员工姓名`,emp.age AS `年龄`,emp.job AS `职位`,dept.`name`as `所属部门` FROM emp INNER JOIN dept WHERE emp.dept_id=dept.id AND emp.age<30;
  ```

  查询拥有员工的部门ID、部门名称。

  ```sql
  SELECT DISTINCT dept.id,dept.`name` from dept RIGHT JOIN emp ON dept.id=emp.dept_id ;
  -- 使用distinct 对数据进行去重
  ```

  查询所有年龄大于40岁的员工,及其归属的部门名称;如果员工没有分配部门,也需要展示出来

  ```sql
  SELECT  emp.*,dept.`name` AS `所属部门` from emp LEFT JOIN dept ON emp.dept_id=dept.id WHERE emp.age>=40;
  ```
  
  查询所有员工的工资等级。
  
  ```sql
  
  ```
  
  
  
  查询"研发部"所有员工的信息及工资等级。
  
  查询"研发部"员工的平均工资。
  
  查询工资比"灭绝"高的员工信息。
  
  查询比平均薪资高的员工信息。
  
  ```sql
  SELECT * from emp WHERE salary>=(SELECT AVG(salary) FROM emp);
  ```
  
  查询低于本部门平均工资的员工信息。
  
  
  
  查询所有的部门信息,并统计部门的员工人数。
  
  查询所有学生的选课情况,展示出学生名称,学号,课程名称

### 5. 日志

#### 错误日志

记录当mysql启动或停止时，以及服务器在运行过程中发生任何严重错误时的相关信息

，默认存放目录 /var/log/,默认日志文件名时mysqld.log,

  ![image-20240519190424142](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240519190424142.png)

```mysql
# 查看日志位置
#登入mysql，mysql -u root -p
show variables like "%log_error%"
```

```mysql
# 查看日志
  tail -n 10 error.log
  查看error级别的日志行，检查原因
```

#### 二进制日志

​	二进制日志（BINLOG）记录了所有的DDL(数据定义语言)语句和DML(数据操纵语言)语句，但不包括数据查询（select 、show）语句

​	**作用：**1） 灾难时的数据恢复。2） mysql主从复制。	

```mysql
# 查看BINLOG日志文件的位置
show variables like "%log_bin%" 
```

![image-20240519191649936](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240519191649936.png)

二进制日志文件的查看

```mysql
# 通过二进制日志查询工具mysqlbinlog来查看，具体语法
mysqlbinglog [参数选项]logfilename
-d :指定数据库名称
-o :忽略掉日志中的前n行命令
-v :将行事件（数据变更）重构为sqL语句
-vv :将行事件（数据变更）重构为SQL语句，并输出注释信息
# 一般查看的是binlog.00最后一个文件
```

#### 日志删除

每天产生的binlog数据巨大，不清除会占用大量的磁盘空间。

```mysql
reset master  # 删除全部的binlog日志，删除之后，将从binlog.000001开始编号
purge master logs to "binlog.000XX" # 删除binlog.0000xx之前的日志文件

purge master logs before 'yyyy-mm-dd hh24:mi:ss'
# 删除“yyyy-mm-dd hh24:mi：ss”之前产生的日志文件
```

可以设置二进制日志的过期时间，会自动删除

```mysql
# >mysql
show variables like "binlog_expire_logs_seconds%";
```

### 06. 主从复制和读写分离

* 主从复制是指将主数据库的DDL和DML操作通过二进制日志传到从库服务器中，然后在从库上对这些日志重新执行（ 也叫重做），从而使得从库和主库保持同步，

* 支持一台同时向多台从库进行复制

* 在配置主从复制是一般是在**建立新的数据库之前**开始配置，

  ```mysql
  #特点
  #1 主库出现问题时，可以快速切换到从库提供服务
  #2 实现读写分离，降低主库的访问压力
  #3 可以在从库中执行备份，以避免备份期间影响主库服务
   
  ```

* **原理**

  ```mysql
  # 基于主数据库的二进制日志binlog
  #分成三步
  #1 Master主库在事务提交时，会把数据变更记录在二进制日志文件Binlog中
  #2 从库读取主库的二进制日志文件Binlog，写入到从库的中继日志Relay Log中
  #3 slave 重做中继日志中的事件，将改变反映它自己的数据
  ```

  ![image-20240520203313483](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240520203313483.png)

* 搭建

  准备两台服务器

```bash
从库 192.168.75.134

主库 192.168.75.131
# 关闭两台服务器防火墙，开启3306端口号
```

#### 主库配置

1.  修改配置文件 /etc/mysql/my.cnf

```shell
  # mysql服务ID，保证整个集群环境中唯一，取值范围：1--2^32 -1,默认唯一
  server-id=1
  # 是否只读，1为只读,0为读写,
  read-only=0
  # 忽略的数据，只不需要同步的数据库
  # binlog-ignore-db=mysql
  # 指定同步的数据库
  # binlog-do-db=db01
=============================================================================  
[mysqld]
server-id=1 # 唯一标识主库的ID，通常是1
# 是否只读，1为只读,0为读写,
read-only=0
#log_bin=/var/log/mysql/mysql-bin.log # 二进制日志文件的基本路径和名称前缀
expire_logs_days=7 # 二进制日志自动清理的天数
binlog_format=ROW # 日志格式，ROW格式通常更适用于复制

```

2. 重启mysql服务器

```shell
 systemctl restart mysql
```

3. 在主机登录mysql并创建复制用户，用于从库连接和复制的用户，并赋予必要的权限

```mysql
crate user 'ning'@'%' identified by '123456';
GRANT REPLICATION SLAVE ON *.* TO 'ning'@'%';
flush privileges;

#ning :用户名  %: 表示任一主机都可链接，也可指定特地IP
```

4. 获取主库的状态信息，主库上运行以下命令获取二进制日志文件名和位置，这些信息将用于从库的配置**，记住file和positiond的值。**

```sql
show master status;
```

![image-20240521195352662](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240521195352662.png)

#### 从库配置

1. 修改从库的MySQL配置文件

   vi /etc/mysql/my.cnf

```mysql
[mysqld]
server-id=2 # 从库的唯一标识ID，应与主库不同，例如2
#relay_log=/var/log/mysql/mysql-relay-bin.log # 中继日志的路径和名称前缀
read_only=1 # 设置从库为只读模式，避免在从库上进行写操作
```

2. 重启数据库mysql服务

```mysql
sudo systemctl restart mysql
```

3. 配置从库复制

```mysql
# 登入到从库数据库，执行
CHANGE MASTER TO
    MASTER_HOST='192.168.75.131', # 主库的IP地址
    MASTER_USER='ning',          # 这里使用你在主库创建的复制用户
    MASTER_PASSWORD='123456', # 复制用户的密码
    MASTER_LOG_FILE='binlog.000029',  #从主库获得的日志文件名(主库status File)
    MASTER_LOG_POS=157 for channel 'slave1'; #从主库获得的位置值(主库 status Position)
    
  重修建立通道
  删除旧通道：
stop slave；
reset slave all for channel '要删除的通道名'；
建立通道
```

4. 停止I/O线程

```shell
--重置slave
reset slave all;
--停止通道,先停止服务
stop slave;
-- 停止 I/O 线程
STOP REPLICA IO_THREAD FOR CHANNEL '';

-- 如果需要，也可以停止 SQL 线程
-- STOP REPLICA SQL_THREAD FOR CHANNEL '';

--重置 MySQL 的 host cache，从而解除对 IP 地址的阻止。
mysqladmin -u root -p flush-hosts

-- 重新启动 I/O 线程
START REPLICA IO_THREAD FOR CHANNEL '';

-- 如果需要，也可以重新启动 SQL 线程
-- START REPLICA SQL_THREAD FOR CHANNEL '';
```

1. 启动从库复制进程

```mysql
# 配置完复制关系后，执行命令启动从库的复制进程
# sql
start SLAVE;
```

5. 验证从库的复制状态以确认配置是否出错

```mysql
show SLAVE STATUS \G;

#此命令会显示详细的复制状态信息，确保Slave_IO_Running和Slave_SQL_Running都显示为Yes，这表明从库正在正常接收并应用主库的更新。
Slave_IO_Running  Yes
Slave_SQL_Running Yes
```

![image-20240521194121030](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240521194121030.png)

* **测试**： 在主库更新执行增删改操作，从库同步执行，即为成功

####  ---读写分离

* 配置mycat读写分离

  ```shell
  # 在mycat里创建数据库mydb1
  # 创建db2 逻辑库
  create databese mydb1;
  # 修改mydb1.schema.json 指定数据源"targetName":"prototype"，配置主机数据源,prototype指向集群
  vi mycat/conf/schemas/mydb1.schema.json
  ```

* 使用注解的方式创建数据源

  ```shell
  #在mycat中创建数据库mydb1;
  create database mydb1;
  #在mycat/schema中出现mydb1.schema.json文件
  #编辑json文件
  #指定数据源信息
  "targetName":"prototype"
  {
  	"customTables":{},
  	"globalTables":{},
  	"normalProcedures":{},
  	"normalTables":{},
  	"schemaName":"mydb1",
  	"targetName":"prototype",#添加的内容
  	"shardingTables":{},
  	"views":{}
  }
  ```

* 添加数据源

  ```shell
  #创建数据源
  /*+ mycat:createDataSource{
  "name":"w0",
  "url":"jdbc:mysql://192.168.75.131:3306/mydb1?useSSL=false&characterEncoding=UTF-8&useJDBCCompliantTimezoneShift=true",
  "user":"root",
  "password":"123456"
  } */;
  
  /*+ mycat:createDataSource{
  "name":"w0",
  "url":"jdbc:mysql://192.168.75.131:3306/mydb1",
  "user":"root",
  "password":"123456"
  } */;
  #查询配置数据源的结果
  /*+ mycat:showDataSources{} */
  ```
  
  ![image-20240704112349547](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240704112349547.png)
  
* 搭建读写集群 **MyCat规定集群用c开头，数字为后缀**

  ```shell
  #创建集群MyCat规定集群用c开头，数字为后缀
  /*! mycat:createCluster{"name":"c0","masters":["w0"],"replicas":["r0"]} */;
  #执行完成后  在/conf/clusters  文件夹下出现对应名称的 配置文件
  #c0集群，name可以为其他名字，主节点为w0的数据连接，从节点为r0的数据连接 
  #在创建数据库连接配置的时候  也可以使用不一样的连接地址做读写分离，但是需要mysql物理数据库先做主从同步
  
  #查看集群配置
  /*+ mycat:showClusters{} */
  
  ```
  
  **——至此，一主一从读写分离配置完成**

* 读写分离验证测试

  ```shell
  #重启mycat服务器中的mycat
  cd mycat/bin
  ./mycat restart
  ------#解决数据库服务器阻塞拒绝访问问题
     mysqladmin -u root -p flush-hosts
  #在从MySQL上修改数据
  mysql> update mytb1 set name='wangwu' where id=2;
  Query OK, 1 row affected (0.00 sec)
  Rows matched: 1  Changed: 1  Warnings: 0
  
  #然后使用mycat登录，查询来查看数据不同
  
  
  
  
  ```

* 双主双从读写分离

  <img src="https://img-blog.csdnimg.cn/17cf5e7ea6ac43f28f0973fdcb4238ac.png" alt="架构图" style="zoom: 50%; " />

  Maste1负责所有写请求，Master2,Slave1,Slave2负责所有读请求。当Master1宕机时，Master2则负责所有读请求，Slave1,Slave2负责所有读请求。Master2为备用机。

  | 编号 | 角色名称 |     IP地址     | 机器名称 |
  | :--: | :------: | :------------: | :------: |
  |  1   | Master1  | 192.168.75.131 |    m1    |
  |  2   | Master2  | 192.168.75.134 |    m2    |
  |  3   |  slave1  | 192.168.75.135 |    s1    |
  |  4   |  slave2  | 192.168.75.136 |    s2    |

* 配置S1复制主库m1,s2复制主库m2,m1和m2互相复制，

  ```shell
  CHANGE MASTER TO
      MASTER_HOST='192.168.75.131', # 主库的IP地址
      MASTER_USER='ning',          # 这里使用你在主库创建的复制用户
      MASTER_PASSWORD='123456', # 复制用户的密码
      MASTER_LOG_FILE='binlog.000029',  #从主库获得的日志文件名(主库status File)
      MASTER_LOG_POS=157 for channel 'slave N'; #从主库获得的位置值(主库 status Position)
      
  #启动slave
  ```

  

* mycat2 注解配置

  ```shell
  #重置所有配置
  /*+ mycat:resetConfig{} */
  
  #创建一个登录到mycat的用户 
  #username： 用户名
  #password：密码
  #ip： 允许登录的ip
  #transactionType：事务类型
  /*+ mycat:createUser{
  	"username":"user",
  	"password":"",
  	"ip":"127.0.0.1",
  	"transactionType":"xa"
  } */
  #删除用户
  /*+ mycat:dropUser{
  	"username":"user"} */
  #显示用户
  /*+ mycat:showUsers */
  
  #创建schema
  /*+ mycat:createSchema{
  	"customTables":{},
  	"globalTables":{},
  	"normalTables":{},
  	"schemaName":"testSchemaDB",
  	"shardingTables":{},
  	"targetName":"prototype"
  } */;
  #schemaName：物理库的库名
  #targetName： 物理库所在的集群,默认应该将prototype集群内的MySQL连接 设置为物理库连接
  #创建成功后，mycat 会映射物理库的所有单表，可以检测是否成功
  
  #创建数据源
  /*+ mycat:createDataSource{
  "name":"r0",
  "url":"jdbc:mysql://127.0.0.1:3306/mysql",
  "user":"root",
  "password":"xxxx"
  } */;
  
  /*+ mycat:createDataSource{
  "name":"w0",
  "url":"jdbc:mysql://127.0.0.1:3306/mysql",
  "user":"root",
  "password":"xxxx"
  } */;
  #配置了一组数据库连接（读写操作）
  #执行完成后 在/conf/datasources 文件夹下会出现对应名称的 配置文件
  
  #删除数据源
  /*+ mycat:dropDataSource{
  	"name":"newDs"
  } */;
  #显示数据源
  /*+ mycat:showDataSources{} */
  
  #创建集群 MyCat规定集群用c开头，数字为后缀
  /*! mycat:createCluster{"name":"c0","masters":["w0"],"replicas":["r0"]} */;
#执行完成后  在/conf/clusters  文件夹下出现对应名称的 配置文件
  #c0集群，name可以为其他名字，主节点为w0的数据连接，从节点为r0的数据连接 
  #在创建数据库连接配置的时候  也可以使用不一样的连接地址做读写分离，但是需要mysql物理数据库先做主从同步
  
  #查看集群配置
  /*+ mycat:showClusters{} */
  
  #删除集群配置
  /*! mycat:dropCluster{
  	"name":"testAddCluster"
  } */;
  
  
  ```
  
  

### 07. 分库分表

* 中心思想：将数据分散存储，使得单一数据库/表的数据量变小来缓解单一数据库的性能问题，从而达到提升数据库性能的目的

* 垂直差分

  ![image-20240528210252481](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240528210252481.png)

  ：垂直分库： 以表为依据，根据业务将不同表拆分到不同库中

  ​	特点：1. 每个哭的表的结构不一样

  		2. 每个库的数据也不一样
     3. 所有库的并集是全量数据

  ![image-20240528210309109](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240528210309109.png)

  ： 垂直分表：以字段为依据，根据字段属性将不同字段拆分到不同表中。

    特点：1. 每个表的结构不一样

  2. 每个表的数据不一样，一般通过一列（主键/外键）关联
  3. 所有表的并集是全量数据

* 水平拆分



* **分库分表**

  --在mycat2中创建两组读写分离数据源r0,w0,r1,w1,

  --创建2组读写集群

  ![image-20240708160357463](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240708160357463.png)

  读写集群：MyCat规定集群用c开头，数字为后缀

  ```json
  {
  	"clusterType":"MASTER_SLAVE",
  	"heartbeat":{
  		"heartbeatTimeout":1000,
  		"maxRetryCount":3,
  		"minSwitchTimeInterval":300,
  		"showLog":false,
  		"slaveThreshold":0.0
  	},
  	"masters":[
  		"w0"
  	],
  	"maxCon":2000,
  	"name":"dwr0",
  	"readBalanceType":"BALANCE_ALL",
  	"replicas":[
  		"r0"
  	],
  	"switchType":"SWITCH"
  }
  ```

  **--创建全局表（广播表）**广播表是冗余在每个数据库中的公共表，具有全量的数据

  ```sql
  --创建数据库db1
  create database db1;
  --切换到db1
  use db1;
  --在建表语句中加上关键字broadcast（广播，即为全局表）
  CREATE TABLE db1.`travelrecord` (
  `id` bigint NOT NULL AUTO_INCREMENT,
  `user_id` varchar(100) DEFAULT NULL,
  `traveldate` date DEFAULT NULL,
  `fee` decimal(10,0) DEFAULT NULL,
  `days` int DEFAULT NULL,
  `blob` longblob,
  PRIMARY KEY (`id`),
  KEY `id` (`id`)
  ) ENGINE=InnoDB DEFAULT CHARSET=utf8 BROADCAST;
  ```

  ​	--数据源中出现db1数据库

  **--创建分片表，实现数据库的分片**

  ```sql
  --#在mycat终端中运行建表语句进行数据分片
  --#核心就是后面的分片规则。 dbpartition表示分库规则，tbpartition表示分表规则。而mod_hash表示按照customer_id字段取模进行分片
  --#tbpartitions 1 dbpartitions 2 表示分一片,数据库两个
  CREATE TABLE db1.orders(
  	id BIGINT NOT NULL AUTO_INCREMENT,
  	order_type INT,
  	customer_id INT,
  	amount DECIMAL(10,2),
  	PRIMARY KEY(id),
  	KEY `id` (`id`)
  )ENGINE=INNODB DEFAULT CHARSET=utf8
  dbpartition BY mod_hash(customer_id) tbpartition BY mod_hash(customer_id)
  tbpartitions 1 dbpartitions 2;
  ```

  插入测试数据

  ```sql
  INSERT INTO orders(id,order_type,customer_id,amount)
  VALUES(1,101,100,100100);
  INSERT INTO orders(id,order_type,customer_id,amount)
  VALUES(2,101,100,100300);
  INSERT INTO orders(id,order_type,customer_id,amount)
  VALUES(3,101,101,120000);
  INSERT INTO orders(id,order_type,customer_id,amount)
  VALUES(4,101,101,103000);
  INSERT INTO orders(id,order_type,customer_id,amount)
  VALUES(5,102,101,100400);
  INSERT INTO orders(id,order_type,customer_id,amount)
  VALUES(6,102,100,100020);
  ```

  查看两台主机的数据库，分别多出db1_0和db1_1两个数据库，且具有两张表

  ```sql
  mysql> select * from orders_0;
  +----+------------+-------------+-----------+
  | id | order_type | customer_id | amount    |
  +----+------------+-------------+-----------+
  |  1 |        101 |         100 | 100100.00 |
  |  2 |        101 |         100 | 100300.00 |
  |  6 |        102 |         100 | 100020.00 |
  +----+------------+-------------+-----------+
  mysql> select * from orders_1;
  +----+------------+-------------+-----------+
  | id | order_type | customer_id | amount    |
  +----+------------+-------------+-----------+
  |  3 |        101 |         101 | 120000.00 |
  |  4 |        101 |         101 | 103000.00 |
  |  5 |        102 |         101 | 100400.00 |
  +----+------------+-------------+-----------+
  3 rows in set (0.00 sec)
  ```

  可以看到两个db库分别有三条数据,总和就是mycat的六条数据,从而实现了分库分表功能

  * **创建ER表**

    -=-与分片表关联的表如何分表，也就是 ER 表如何分表

    ```sql
    CREATE TABLE orders_detail(
    	`id` BIGINT NOT NULL AUTO_INCREMENT,
    	detail VARCHAR(2000),
    	order_id INT,
    	PRIMARY KEY(id)
    )ENGINE=INNODB DEFAULT CHARSET=utf8
    dbpartition BY mod_hash(order_id) tbpartition BY mod_hash(order_id)
    tbpartitions 1 dbpartitions 2;
    ```

    插入测试数据

    ```sql
    INSERT INTO orders_detail(id,detail,order_id) VALUES(1,'detail1',1); INSERT INTO orders_detail(id,detail,order_id) VALUES(2,'detail1',2); INSERT INTO orders_detail(id,detail,order_id) VALUES(3,'detail1',3); INSERT INTO orders_detail(id,detail,order_id) VALUES(4,'detail1',4); INSERT INTO orders_detail(id,detail,order_id) VALUES(5,'detail1',5); INSERT INTO orders_detail(id,detail,order_id) VALUES(6,'detail1',6);
    ```

    运行关联语句查询数据

    ```sql
    SELECT * FROM orders o INNER JOIN orders_detail od ON od.order_id=o.id; 
    ```

    

  

### 07.mycat安装

![image-20240701152439573](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240701152439573.png)

* 准备一台服务器安装MyCat使用（ubuntu 64位）,mycat本身不存储数据

* 安装jdk

  下载jdk包

  ```shell
  https://repo.huaweicloud.com/java/jdk/    #华为云下载jdk包
  sudo tar -zxvf  jdk-8u371-linux-x64.tar.gz -C /usr/local #解压到 /usr/local 目录下
  # 修改配置文件
  cd ~ 
  vim ./.bashrc
  ```

  编辑环境变量

  ```shell
  export JAVA_HOME=/usr/local/jdk1.8.0_371
  export JRE_HOME=${JAVA_HOME}/jre
  export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
  export PATH=${JAVA_HOME}/bin:$PATH
  
  ```

  查看java

  ```shell
  java -version
  #结果
  ning@ning-machine:~/Desktop$ java -version
  java version "1.8.0_191"
  
  #出现版本号即为安装成功
  ```

安装mycat

* ```shell
  # 将mycat 的包解压到指定目录即可
  
  # 进入mycat 文件夹
  bin :存放可执行文件
  conf: 存放mycat的配置文件
  lib：存放mycat的项目依赖包（jar）
  logs:存放mycat得日志文件
  ```

* 安装依赖包

  ```shell
  wget https://download.topunix.com/MySQL/Software-Cluster/Software-Mycat/Mycat2/mycat2-1.21-release-jar-with-dependencies.jar
  cp /mysql/app/mycat2-1.21-release-jar-with-dependencies.jar mycat/lib/
  ```

  

* 启动mycat服务

  ```shell
  #配置权限
  cd /mycat/bin
  chmod 777 ./mycat
  chmod 777 ./wrapper-linux-ppc-64
  chmod 777 ./wrapper-linux-x86-32
  chmod 777 ./wrapper-linux-x86-64
  #启动
  bin/mycat start
  #停止
  bin/mycat stop
  #mycat启动后，占用端口号8066
  ```

* 查看启动日志

  ```shell
  tail -10f mycat/logs/wrapper.log
  ```

* 数据库远程访问

  mycat作为数据库中间件要和数据库部署在不同的机器上，所以要验证远程访问情况

  ```shell
  mysql -u root -p 123456 -h 192.168.75.131 -P 3306
  mysql -u root -p 123456 -h 192.168.75.133 -P 3306
  # 如远程访问出错，请建立对应用户
  grant all privileges on *.* to root@'主机号' identified by '123456'
  #登录mycat
  
  ```

  

* 分片配置（schema.xml）

  

  ```shell
  # 在mycat/conf/目录下
  vim schema.xml
  
  ```

* 配置原型库数据源

  ```shell
  vi mycat/conf/datasources/prototypeDs.datasource.json 
  {
  	"dbType":"mysql",
  	"idleTimeout":60000,
  	"initSqls":[],
  	"initSqlsGetConnection":true,
  	"instanceType":"READ_WRITE",
  	"maxCon":1000,
  	"maxConnectTimeout":3000,
  	"maxRetryCount":5,
  	"minCon":1,
  	"name":"prototypeDs",
  	"password":"123456",  #数据库密码
  	"type":"JDBC",
  	"url":"jdbc:mysql://localhost:3306/mysql?useUnicode=true&serverTimezone=Asia/Shanghai&characterEncoding=UTF-8",
  	"user":"root",        #数据库用户名
  	"weight":0
  }
  ```

* 配置mycat登录用户

  ```shell
  vi /mycat/conf/users/root.user.json
  {
  	"dialect":"mysql",
  	"ip":null,
  	"password":"123456",
  	"transactionType":"xa",
  	"username":"root"
  }
  ```

  设置后，可以通过此用户和密码登录mycat(mysql -uroot -p123456 -P 8066 -h192.168.75.135)

### 08.使用mycat2进行分库分表

* 

### 09. mysql 权限管理

1. 创建用户

   ```mysql
   create user "ning"@"localhost" identified by "password";
   ```

2. 授予用户权限

   ```mysql
   GRANT SELECT , UPDATE ON `DATEBASE`.`TABLES` TO 'NING'@localhost;
   ```

3. 查看用户权限

   ```mysql
   SHOW GRANTS FOR 'ning'@'localhost';
   ```

4. 撤销用户权限

   ```mysql
   REVOKE UPDATE ON `DATEBASE`.`TABLES` FROM 'NING'@`localhost`;
   ```

5. 删除用户

   ```mysql
   DROP USER 'ning'@'localhost';
   ```

### 10 .mysql 事务管理

1. 事务的基本特性
   * 原子性（atomicity）
   * 一致性（consistency）
   * 持久性（durability）
   * 隔离性（isolation）