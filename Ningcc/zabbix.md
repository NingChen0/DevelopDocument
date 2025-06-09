# zabbix 

#### 写在最前

>  --mysql zabbix 用户密码为ning6666

* ``dpkg``: 用于管理系统里的deb包。可以对其安装卸载、安装、deb打包、deb解压等操作，与之相关的工具apt-get 工具可以在线下载deb包

* ```shell
  -i : 安装软件包
  -r : 删除软件包
  -P : 删除软件包的同时删除其配置文件
  -L : 显示与软件包关联的文件
  -l : 显示已安装的软件包列表
  --unpack ： 揭开软件包
  -c ： 显示软件包内文件列表
  --configre : 配置软件包
  ```

#### 01. zabbix 安装和部署

* 

#### 02. 安装zabbix-agent2监控端

* ```shell
  # 下载包
  wget https://repo.zabbix.com/zabbix/5.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_5.0-1+focal_all.deb
  # 安装软件包
  sudo dpkg -i zabbix-release_5.0-1+focal_all.deb
  # 更新列表
  sudo apt update
  # 安装agnet2
  sudo apt install zabbix-agent2
  # 编辑配制文件
  vim /etc/zabbix/zabbix_agent2.conf   #编辑你的主机ip 
  # 更新以下设置：
  Server=xxxxxxx		        //Server=[zabbix server ip]# 服务端ip
  Hostname=xxxxxx			//Hostname=[Hostname of client system ] # 服务端ip
  ```

* 重新启动和自启动

* ```shell
  sudo systenmctl restart zabbix-agent2
  sudo systemctl enable zabbix-agent2
  ```

* 验证zabbix- agent2的连通性

  ```shell
  # 在服务端通过命令主动获取数据
  sudo apt install zabbix-get
  #测试
  [ning@ning-machine zabbix]$ zabbix_get -s '192.168.75.131' -p 10050 -k 'agent.ping'
  1  #结果
  ```

#### 03. zabbix创建自定义key监控项目

* ``监控键值``可通过zabbixWeb端页面配置->主机->点击主机名称->模板中看到监控项
* ``自定义监控内容:``

​	监控登录服务器的人数，需求：限制登录人数不超过三个，超过三个就发出报警信息

* 使用命令行用zabbix自带的key获取内存信息

  ```shell
  # 查询/目录下已使用的磁盘大小
  zabbix_get -s '192.168.75.131' -p 10050 -k 'vfs.fs.size[/,used]'
  # 查询/目录下磁盘总大小
  zabbix_get -s '192.168.75.131' -p 10050 -k 'vfs.fs.size[/,total]'
  ```

  ![QQ_1721908804876](C:\Users\Administrator\AppData\Local\Temp\QQ_1721908804876.png)

* 如何添加自定义key

* 需求：限制登录人数不超过三个，超过三个就发出报警信息

  ```shell
  who #查看当前登入用户，当有两个用户登录时会列出两行
  ```

  ![QQ_1721909254091](C:\Users\Administrator\AppData\Local\Temp\QQ_1721909254091.png)

  ```shell
  who | wc -l
  #wc 统计行数，单词数和字节信息等
  #-l:统计有多少行  -w: 统计单词的数目  -c: 统计字节的数目  -L:统计最长行的长度
  ```

* 手动创建zabbix的自定义文件

  ```shell
  #在被监控机器上编辑配置文件，用于自定义key
  vim /etc/zabbix/zabbix_agent2.conf
  ```

  ![QQ_1721909755188](C:\Users\Administrator\AppData\Local\Temp\QQ_1721909755188.png)

​	**文件中已经有Include包含了zabbix_agentd.d/*.conf**

* 所以可以在文件夹中写自定义的key.conf配置文件

  ```shell
  sudo vi userparamter_login.conf
  #内容
  UserParameter=login.user,who | wc -l
  # login.user :为key
  #who | wc -l : 为值
  ```

* 重启zabbix-agent2

  ```shell
  systemctl restart zabbix-agent2
  ```

* 在主服务器上测试

  ```shell
  zabbix_get -s '192.168.75.131' -p 10050 -k 'login.user'
  #的到结果为1，服务器登录用户为1个
  ```

  ![QQ_1721910597388](C:\Users\Administrator\AppData\Local\Temp\QQ_1721910597388.png)

###### 在zabbixWeb服务端中添加

* 创建模板

  ![QQ_1721913131559](C:\Users\Administrator\AppData\Local\Temp\QQ_1721913131559.png)

* 创建应用集（好比一个文件夹，放入一堆监控项）

  ![QQ_1721913025214](C:\Users\Administrator\AppData\Local\Temp\QQ_1721913025214.png)

* 创建监控项，自定义item,

* <img src="C:\Users\Administrator\AppData\Local\Temp\QQ_1721913055238.png" alt="QQ_1721913055238" style="zoom:50%;" />

* 创建触发器，当监控项获取到值的时候，进行和触发器比较，判断，决定是否报警

  <img src="C:\Users\Administrator\AppData\Local\Temp\QQ_1721911976456.png" alt="QQ_1721911976456" style="zoom:50%;" />

* 创建图形

  <img src="C:\Users\Administrator\AppData\Local\Temp\QQ_1721912148636.png" alt="QQ_1721912148636" style="zoom:50%;" />

* 将具体的主机和改模板链接，关联

  <img src="C:\Users\Administrator\AppData\Local\Temp\QQ_1721912329762.png" alt="QQ_1721912329762" style="zoom:50%;" />

* 测试结果

  <img src="C:\Users\Administrator\AppData\Local\Temp\QQ_1721912742808.png" alt="QQ_1721912742808" style="zoom: 50%;" />

#### 04. 邮件报警配置

* > 

* ![QQ_1721959100812](C:\Users\Administrator\AppData\Local\Temp\QQ_1721959100812.png)

* ![QQ_1721959263243](C:\Users\Administrator\AppData\Local\Temp\QQ_1721959263243.png)

* 给用户添加上邮箱报警
* ![QQ_1721959755933](C:\Users\Administrator\AppData\Local\Temp\QQ_1721959755933.png)

* ![QQ_1721959783204](C:\Users\Administrator\AppData\Local\Temp\QQ_1721959783204.png)

* 报警邮件信息配置

  启用动作报警信息

  ![QQ_1721960363730](C:\Users\Administrator\AppData\Local\Temp\QQ_1721960363730.png)

* 测试：当agnet1机器登入用户超过3个，发送邮件报警
* <img src="E:\Documents\Tencent Files\2105341842\nt_qq\nt_data\Pic\2024-07\Ori\8c2fe3967337545464ccc4351bfef8e9.png" alt="8c2fe3967337545464ccc4351bfef8e9" style="zoom: 25%;" />
* 
