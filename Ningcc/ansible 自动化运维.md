# ansible 自动化运维

#### ——写在最前

​	ansible 功能

* 可以实现批量系统操作配置

- ​	可以实现批量软件服务部署
  - 可以实现批量文件数据分发
  - 可以实现批量系统信息收集

###### state

* state参数决定了Ansible模块对目标系统上的资源所采取的操作，

  ```shell
  #state参数在各模块的使用
  file:
  #state的值
  present:或exists:表示资源应该存在
  absent:表示资源不应该存在
  latest:表示软件包应该是最新版本
  started:表示服务应该正在运行
  stoped:表示服务应该被停止
  restarted:表示服务应该被重启
  reload:表示服务应该被重新加载
  
  ```

  

#### 01.环境安装

* 机器准备

  |   名称   |       ip       |  角色   |
  | :------: | :------------: | :-----: |
  |  clone1  | 192.168.75.131 |  host1  |
  |  clone2  | 192.168.75.134 |  host2  |
  | ubuntu64 | 192.168.75.135 |  host3  |
  |  clone3  | 192.168.75.136 | ansible |

* 在hosts中添加主机名称

  ```shell
  sudo vi /etc/hosts
   # 添加
   192.168.75.136 ansible
   192.168.75.131 hsot1
   192.168.75.134 host2
   192.168.75.135 hsot3
   #保存退出
   #测试
   ping host1\host2\host3
   #成功 
  ```

* 安装

  ```shell
  #更新软件包列表
  sudo apt update
  sudo apt ugrade -y
  #安装依赖
  sudo apt install software-properties-common
  #添加 Ansible 存储库（正在添加 Ansible 的 PPA（Personal Package Archive）到你的系统中。这允许你从 Ansible 团队维护的仓库安装最新的 Ansible 软件包）
  sudo add-apt-repository ppa:ansible/ansible
  #更新软件包列表以包括新的存储库信息
  sudo apt update
#安装ansible
  sudo apt-get install ansible
  #验证
  ansible --version
  ```

* 测试

  ```shell
  # 在 /etc/ansible/host文件中添加主机清单
  host1
  host2
  ansible host1 -m ping -u abc -k
  密码：1234
  
  
  ```

#### 02.Inventory -主机清单

* ###### 增加主机组

```shell
#在/etc/ansible/host中添加主机
[webservers]
host1
host2
example
## db-[99:101]-node.example.com
[webservers] #webservers一组
host1
host2
[host3servers] #host3webservers一组
host3
#测试 ： ansible webservers -m ping -u abc -k
```

* ###### 增加用户名密码

```shell
## db-[99:101]-node.example.com
[webservers]
host1 ansible_ssh_user='abc' ansible_ssh_pass='1234'
host2 ansible_ssh_user='abc' ansible_ssh_pass='1234'
#测试 ： ansible webservers -m ping
```

* ###### 增加端口

```shell
host2 ansible_ssh_user='abc' ansible_ssh_pass='1234' ansible_ssh_port='2222'
#测试 ： ansible webservers -m ping
```

* ###### 组：变量

```shell
#通过[组名:变量s]
[webservers:vars]
ansible_ssh_user='abc'
ansible_ssh_pass='1234'
#组变量中对所有成员（host1,host2,host3）都生效
#实例：
## db-[99:101]-node.example.com
[webservers]
host1
host2
host3 ansible_ssh_user='ning' ansible_ssh_pass='1234'
[webservers:vars]
ansible_ssh_user='abc'
ansible_ssh_pass='1234'
#测试
ansible webservers -m ping
```

* ###### 子分组

```shell
#在/etc/ansible/host中可将多个主机分组，将所分的组合成一个大组
## db-[99:101]-node.example.com
[webservers] #分组1
host1
host2
[sqlservers] #分组2
host3 ansible_ssh_user='ning' ansible_ssh_pass='1234'
[webservers:vars] #分组1的组变量
ansible_ssh_user='abc'
ansible_ssh_pass='1234'

[allservers:children]#合成的大组，下面有分组1和分组2两个孩子
webservers
sqlservers
#测试
ansible allservers -m ping 
```

* ###### 自定义主机列表

```shell
#一台新的主机，包含一个host文件，可以使用-i 来链接次host
ansible -i hosts allservers -m ping
#在存在此hosts文件目录下执行
```

#### 03.拷贝模块

* ```shell
  #ansible 拷贝模块 每一个模块都有对应的帮助文档
  ansible-doc copy
  #拷贝模块，向目标集群发送文件
  ```

* ###### 拷贝模块的使用

* ```shell
  ansible allservers -m copy -a 'src=/etc/hosts dest=/home/abc/Desktop/hosts.txt owner=abc group=bin mode=777'
  ```

* ###### 写入成功时

* ```shell
  #copy写入成功时显示如下
  host3 | CHANGED => {
      "ansible_facts": {
          "discovered_interpreter_python": "/usr/bin/python3"
      },
      "changed": true,
      "checksum": "8dade59b84f5f1a80b42ff6f7ccc85b76b49be7a",
      "dest": "/home/abc/Desktop/hosts.txt",#目标地址
      "gid": 135,
      "group": "mysql",
      "mode": "0777",#权限
      "owner": "abc",
      "path": "/home/abc/Desktop/hosts.txt",
      "size": 321,
      "state": "file",
      "uid": 1001
  }
  ```

#### 04.用户模块

* ###### 用户模块介绍

  ```shell
  #创建用户，修改密码，修改shell,删除用户
  #user模块用于管理系统用户，可以创建、删除修改用户属性
  ```

* ###### 用户模块的使用

* ```shell
  #参数说明
  #state :用户的目标状态，（可选值：present:确保用户存在\absent：确保用户不存在）默认值present
  ```

* ###### 实例

  ```shell
  #创建一个名为john的用户
  ansible allservers -m user -a "name=john state=present"#出席
  #删除名为john的用户
  ansible allservers -m user -a "name=john state=absent"#缺席
  #修改用户属性
  
  #建用户jane并为其设定特定属性
  #通过openssl生成密码
  echo '123456' | openssl passwd -1 -stdin
  #输出一段密文
  
  ansible allservers -m user -a "name=jane commentne Doe' uid=1042 group=admin home=/home/jane shell=/bin/bash passwor=‘生成的密码
  
  ```

#### 05.yum模块

###### yum模块

```shell
#yum模块主要用于软件的安装、升级和卸载
```

* 批量安装软件

* ```shell
  ansible allservers -k -m yum -a "name=sysstat,screen state=installed"
  #name：安装软件的名称，state: installed 表示安装软件
  #-k :提示输入密码
  #-a :传递参数给指定模块
  ```

* 批量删除软件

* ```shell
  ansible allservers -m yum -a "name=sysstat,screen state=absent"
  state=absent :卸载软件
  ```


#### 06. 文件file模块

###### file模块

* ```shell
  # 使用文件模块批量创建文件
  ansible allservers -m file -a "path=/home/abc/Desktop/88.txt mode=777 state=touch"
  #新建文件夹
  ansible allervers -m file -a "path-/home/abc/Desktop/99 mode=777 state=directory"
  #删除文件 
  ansible allservers -m file -a "path=/home/Desktop/88.txt mode=777 state=absent"
  
  ```

* 

* 







