# linux

### ---写在最前

* xhell7命令行高亮颜色设置

  ```shell
  #在用户家目录中 编辑 .bashrc文件
  vim .bashrc
  #在文件末尾添加
  PS1='[\[\e[32;1m\]\u@\[\e[32;1m\]\h\[\e[33;1m\] \W\[\e[0m\]]\$ '
  #保存退出重新加载配置文件
  source .bashrc
  ```

* 开放指定端口号

  ```shell
  firewall-cmd --zone=public --add-port=3306/tcp -permanent
  firewall-cmd -reload
  ```

* 关闭服务器防火墙

  ```shell
  systemctl stop firewalld
  systemctl disable firewalld
  systemctl stop ufw
  systemctl disable ufw
  ```

* 安装`ssh`软件

  ```shell
  sudo apt-install ssh
  ```

* `rpm`命令

  -- `rpm`是`redha`t软件包管理工具

  常用组合命令

  ```shell
  - ivh : # 安装显示安装进度 install verbose hash
  - Uvh : # 升级软件包 Upadate
  - qpl : # 列出rpm软件包内的文件信息{ query package insatll packages}
  - e : # 删除包
  
  ```

  


### --linux知识点

#### 1.`adduser` 和 `useradd` 的区别

  	答：`useradd` 只创建用户，不会创建用户密码和工作目录，创建完了需要使用 `passwd <username>`  去设置新用户的密码。`adduser` 在创建用户的同时，会创建工作目录和密码（提示你设置），做这一系列的操作。其实 `useradd`、`userdel` 这类操作更像是一种命令，执行完了就返回。而 `adduser` 更像是一种程序，需要你输入、确定等一系列操作

#### 2. `head`、`tail`、`wc`、`tee`

* `head`默认获取文件的前十行

  ```shell
  head fileName # 默认获取文件的前10行
  eg: head -3 fileName # 获取文件前三行
  ```

* `tail` 获取文件的后十行

  ```shell
  tail -4 fileName : # 获取文件的后4行
  tail -f fileName : # 持续跟踪日志，-f: follow ,跟踪文件的增长
  ```

* `wc` 计算文本数量

  ```shell
  wc -l # 打印行数
  wc -w # 打印单词数量
  wc -c # 打印字节数
  wc -L # 打印最长行的字节数
  # eg:
  [ning@aliyunos ~]$ wc 1.txt 
   2  4 10 1.txt  # 2行 4个单词 10个字节
  [ning@aliyunos ~]$ cat 1.txt 
  AA B
  b CC
  ```

#### 3. `cut`、`paste`、`sort`、`tr`

* `cut` 截切文件中部分字符，一般是有固定宽度或分隔符组成的文件

  ```shell
  #复制文件passwd至家目录
  cp /etc/passwd .
  # passwd 每一行都以`:`分割
  cut -d ':' -f 1,3 fileName # 提取文件中以`:`分割的第1列和第3列进行组合
  -d :# delimiter，界定符，以什么为分隔符，默认为tab键
  -f : #fields ,指定要提取的（字段）列，一个或多个
  # 输出文件中第一列到第三列的内容
  cut -d : -f 1-3 passwd
  ```

* `paste`:合并两个或多个文件的行

  ```shell
  # 创建两个文件
  touch 1.txt 2.txt
  paste 1.txt 2.txt > combine.txt # 合并两个文件至combine.txt(每一行合并)
  paste 1.txt 2.txt # 合并文件并将结果输出到屏幕
  #eg:
  [ning@aliyunos ~]$ paste 1.txt 2.txt > combine.txt
  [ning@aliyunos ~]$ cat combine.txt 
  AA B	hello
  b CC	expire
  	desire
      escape
  # 默认情况下，paste使用tab键空格进行分隔链接，可使用-d 来指定分隔链接符
  -d #来指定分隔链接符
  [ning@aliyunos ~]$ paste -d ':' 1.txt 2.txt > combine.txt
  [ning@aliyunos ~]$ cat combine.txt 
  AA B:hello
  b CC:expire
  :desire
  :escape
  ```

* `sort` : 对文件文本中的行进行排序，可以是数字大小、字母顺序，或指定特定规则

  ```shell
  sort filename # 按行进行排序，并标准输出至屏幕上
  -o :# 直接修改源文件或写入指定文件
  eg: sort -o sorted_file file
  -u : # 去除重复的行
  ```

* `tr` :

#### 4. `jion`、`split`、 `nl` 、`uniq`

* `jion`: 用于合并具有共同字段的两个文件，类似于数据库的链接操作

  ```shell
  join [选项]	file1 file2
  # eg:
  [ning@aliyunos lix_te]$ cat 1.txt 
  apple	1.00
  banana	0.50
  cheery	2.00
  date	1.50
  [ning@aliyunos lix_te]$ cat 2.txt 
  apple	5
  banana	10
  cheery	8
  fig	15
  [ning@aliyunos lix_te]$ join 1.txt 2.txt 
  apple 1.00 5
  banana 0.50 10
  cheery 2.00 8
  # 如果须显示第一个文件的所有内容
  join -a 1 file1 file2
  # 显示第二个则-a 2, 显示两文件都没匹配上的内容 join -a 1 -a 2 file1 file2
  ```

* `split`： 将一个大的日志文件切割成若干个小文件

  ```shell
  split -l 5 1.txt
  -l :按行切割
  split [选项] [输入文件] [前缀]
  #eg:
  [ning@aliyunos lix_te]$ split -l 5 1.txt zou_
  [ning@aliyunos lix_te]$ ls
  1.txt  2.txt  zou_aa  zou_ab  zou_ac  zou_ad
  
  ```

* `unip`：过滤相邻的重复行，间隔的重复行不过滤

  ```shell
  #可以使用sort进行排序后过滤重复行，即可过滤所有重复的行
  ```

#### 5.`sed`、`awk`

* `sed`:用于自动化文本替换、删除、插入等操作

  ```shell
  `s/查找的文本/替换的文本/标志`:#替换操作。如果指定标志为`g`,则全局替换，否则只替换每行的第一个匹配项
  -i ：# 强制性更改源文件
  sed -i 's/nihao/hello/g' 1.txt # 在源文件中替换nihao为hello
  hello banana
  hello apple
  hello world
  orange hello
  xiaomi hello
  `d`:# 删除模式空间中的行
  [ning@aliyunos lix_te]$ sed  '/nihao/d' 1.txt #添加参数-i 在源文件中删除带nihao的行
  orange hello
  xiaomi hello
  `p`:# 打印模式空间中的行
  ```

* `awk`：

  ```shell
  awk -F':' '{print $1,$NF}' passwd | head -n1# -F: 设置字段分隔符为冒号（:）,打印第一列和最后一列
  root /bin/bash
  
  ```

  

### 01. liunx 目录结构

1. bin:可执行脚本文件目录，如常用的命令ls、tar、mv、cat等
2. /dev:linux的设备文件，访问该目录的某个文件相当于访问该设备
3. /etc:环境变量，系统配置文件存放的目录
4. /lib或lib64:依赖，系统使用的函数库的目录，程序在执行过程中，需要调用一些额外的参数时需要函数库的协助
5. /mnt：光盘挂载点，/media：设备
6. /opt:装软件的，
7. /proc:内存的编号，此目录的数据都在内存中，如系统核心，外部设备，网络状态，由于数据都存放于内存中，所以不占用磁盘空间
8. /srv：运行时产生的文件，服务启动之后需要访问的数据目录，如 www 服务需要访问的网页数据存放在 /srv/www 内

### 02. 使用Xshell连接OS

1. xshell新建会话连接

### 03. 卸载软件包

 1. ```bash
    #输入以下命令来卸载软件包，将package_name替换为您要卸载的软件的名称：
    sudo apt-get remove package_name
    #这个命令会卸载软件但保留配置文件。如果您也想删除配置文件，可以使用：
    sudo apt-get purge package_name
    ```

### 03. 开关机命令

 1. ```js
    sync 将内存中的信息保存进磁盘
    shutdown -h 关机
    shutdown -r 重启
    reboot  重启
    poweroff 关机
    ```

### 04. 防火墙设置

1. ```js
   systemctl stop firewalld		# 关闭防火墙
   systemctl disable firewalld		# 停止防火墙开机自启
   ```

### 04. 查看网络状态

1. ```js
   systemctl status NetworkManger	//查看状态
   systemctl stop NetworkManger     //关闭
   systemctl start NetworkManger   //开启
   
   //配置网络
   ```

### 05. ip地址和主机名

 1. ```js
    //查看IP地址 
     ifconfig
    //修改主机名
      hostnamectl set hostname 新名字 
    // DCHP：动态获取IP地址，及每次重启设备后都会获取一次，可能导致ip地址频繁变更
    //配置固定的IP地址
    //在VMware 中配置固定IP，
      Linux（centOS）中vim编辑/etc/sysconfig/network-scripts/ifcfg-ens33文件
    //ubuntu vim /etc/netplan/01-network-manager-all.yaml
    
    ```

 2. ```js
    //01-network-manager-all.yaml内配置项
    //本机网卡名为：ens33，IP地址为：192.168.75.130，子网掩码为：255.255.255.0
    # Let NetworkManager manage all devices on this system
    network:
      ethernets:
        ens33:
          addresses: [192.168.75.130/24]          # 设置静态IP地址和掩码
          routes:                                 # 设置网关地址
           - to: default
             via: 192.168.15.2
          dhcp4: false                            # 禁用dhcp
          nameservers:
            addresses: [114.114.114.114, 8.8.8.8] # 设置主、备DNS
      version: 2
      renderer: NetworkManager
    
    ```
    
 3. 

 4. #### 端口

    * 可以通过端口实现锁定具体程序，确保程序进行沟通
    * netstat命令，查看指定端口的占用情况

    ```js
    //安装netstat：apt-get install net-tools
    netstat -anp | grep 6000 //查看当前系统端口号为6000 的占用情况
    ```

### 05. 进程管理-ps -top

1. ```js
   //查看进程信息
   ps [-e -f]
   ps -ef //列出全部进程的全部信息
   // -e: 显示出全部进程
   // -f: 以完全格式化的形式展示信息（展示全部信息）
   //配合管道符使用
   ps -ef | grep tail //查找tail进程相关信息
   ```

2. ![image-20240409171718078](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240409171718078.png)

3. ```js
   //从左到右依次是
   UID：所属的用户Id,
   PID：进程号ID
   PPID：进程的父ID(启动此进程的其他进程)
   C: 此进程的CPU占用率（百分比）
   STIME：进程的启动时间
   TTY：启动此进程的终端序号，如显示？，表示非终端启动
   TIME：进程占用CPU的时间
   CMD：进程对应的名称或启动命令或启动路径
   ```

4. 进程的关闭

   ```js
   kill -9 进程ID
   // -9:表示强制关闭进程
   ```


#### top-查看系统资源占用

* 通过top命令查看cpu、内存使用情况，类似windows的任务管理器
* 默认5秒刷新一次，直接输入top即可，按**q或者ctrl+c退出**

![image-20240409224321074](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240409224321074.png)

```js
PID :进程ID
user :进程所属用户
FR: 进程优先级，越小优先级越高
NI: 负值表示高优先级，正值表示低优先级
VIRT: 进程使用的虚拟内存，KB
RES: 进程使用的物理内存
SHR : 进程使用的共享内存
S :进程状态(S 休眠，R运行，Z僵尸状态，N负数优先级，I空闲状态)
%cpu: 进程占用cpu率
%MEM: 进程占用内存率
TIME+: 进程使用CPU时间总计，单位10毫秒
COMMAND:进程的命令或名称或程序文件路径
```

```shell
#导航和排序
按键P ： 按cpu使用率排序（默认）
M: 按内存使用率排序
T: 按运行时间排序
N: 按PID排序
R: 反转当前排序顺序
```



```js
top -i //只显示运行的进程， 不显示任何的闲置进程
```

* ##### 磁盘信息监控

```js
df [-h] //-h : 以更加人性化的单位显示
iostat [-x] [num1] [num2] //-x : 显示更多信息 num1:刷新时间，num2,刷新次数
```

### 05.磁盘管理

1. `df`命令

   * disk free 显示已挂载的文件系统的磁盘使用情况

   ```shell
   
   ```

   



### 05 .`du`命令

* disk usage(磁盘使用量)  --直接指定一个目录或文件作为参数，`du`显示所占空间大小

  ```shell
  du /path/to/directroy
  du -h /
  ```

  ```shell
  -h ：# 以人类可读形式展示结果
  -s : # 只显示总计
  # 它默认报告的是文件和目录占用的块空间，而不是实际存储的数据大小
  
  
  ```

  

### 05. 文件目录类命令

查看当前工作路径

* ```js
  pwd
  ```

打印当前所有文件及文件夹,不同颜色不同类型，（黑色是文件，蓝色文件夹，绿色可运行的脚本文件）

* ```js
  ls
  ls -a //列出所有文件，包括隐藏文件,隐藏问价用.或..表示
  ls -l //列出目录下文件的完整信息
  
  ```

1. 创建文件夹

   * ```shell
     mkdri 文件名  //#或者路径
     makdir -p wenjian/wenjian2/wenjian3    #//-p 创建多重目录
     ```

     删除空文件夹

   * ```js
     rmdir wenjian   //只能删除空文件夹
     ```

     创建空文件

   * ```js
     touch 1.txt
     touch 2.mp3
     touch 3.mp4  //不管后缀怎么样，创建的文件都是异样的
     ```

     复制文件  (**写路径时忘了某路径下是否有该文件，按两下tab键)**  --rs

   * ```bash
     cp 源文件 目标文件路径
     cp sun.txt ../yaoGUai/
     cp shen/sun.txt ../yaoguai/ -r #//递归复制整个文件夹
     cp shen/* ../yaoguai/   #//*代表复制该文件夹下所有文件
      
     #// 复制目录
     cp -r dir1 dir2 # 将dir1整个目录复制到dir2文件夹下 
     cp -r 
     
     #//强制覆盖文件不提示
     \cp shen/* yaoguai/       #//反斜杠cp就不会提示是否覆盖相同文件名
     ```
     
     删除文件
     
   * ```js
     rm 文件名    //删除时会提示是否删除
     rm -f 文件名 //删除时不提示是否删除
     rm -r 文件目录 //递归删除目录和目录内文件
     rm -rf 文件目录  //直接递归删除整个文件夹
     ```
   
     移动文件，又具有修改名称功能
   
   * ```js
     mv wenjian.txt ~/wenjian/wenjian.txt   //移动到wenjian/下，一般加个文件名，
     mv oldName newName //重命名
     ```
   
     查看文件内容
   
   * ```js
     cat wenjian.txt 
     cat -n wenjian.txt //显示行号
     ```
   
     文件内容很多时，使用more,可以翻页，或less，/或？可以查询

   * ```js
     more wenjian.txt //按空格或回车向下翻页，
     ```

     **现在常用查看文件的命令**
   
     head 和**tail**
   
   * ```js
     head -4 wenjain.txt 显示文件的前4行 //默认情况不指定n时显示文件前10行
     tail -5 wenjain.txt 查看文件末尾5行 //
     ```

### 文件查找

* find 查找

  ```shell
  find :# 从指定的目录递归的向下子目录查找文件
  find [目录][选项][动作]
  # -name: 按照指的文件名查找文件
  # -user: 查找属于指定用户名下的文件
  # -size: 按照制定文件大小查找文件，单位：b，c,w，k，M,G
  find -name info # 查找名字为info的文件
  -a:and 必须满足两个条件才显示
  -o:or 只要满足一个条件就显示
  -name：按照文件名查找文件
  -iname：按照文件名查找文件(忽略大小写)
  -type：根据文件类型进行搜索
  -perm：按照文件权限来查找文件
  -user 按照文件属主来查找文件。
  -group 按照文件所属的组来查找文件。
  -fprint 文件名：将匹配的文件输出到文件。
  -newer file1 ! newer file2 查找更改时间比文件file1新但比文件file2旧的文件
  #常用动作
  
  -print 默认动作，将匹配的文件输出到标准输出
  -exec 对匹配的文件执行该参数所给出的命令。相应命令的形式为 'command' { } \;，注意{ }和\；之间的空格。
  -ok 和-exec的作用相同，只不过以一种更为安全的模式来执行该参数所给出的命令，在执行每一个命令之前，都会给出提示，让用户来确定是否执行。
  -delete 将匹配到的文件删除
  |xargs  将匹配到的文件删除   |xargs rm -rf 
  
  ```
  
  ```shell
  # find使用
  #在home目录下查找以“.txt”结尾的文件
  find /home/ -name "*.txt"
  #在/home目录下查找以。txt结尾的文件，忽略大小写
  find /home -iname "*.txt"
  ```
  
  

### 06.输出命令 echo

1. ```js
   echo hello world ! //输出hello world到控制台
   # echo -e 开启转义字符
   echo -e "OK! \n" # -e 开启转义
   echo "It is a test"
   # 输出字符串可省略双引号
   ```
   
2. 覆盖与追加   ： >和>>

   ```js
   ll >a.txt   //将列出来的信息写入文件a.txt中，将原有的内容覆盖(没有这个文件时会自动创建)
   ls -l >>a.txt //将列出来的信息追加到a.txt中，在末尾写入
   cat a.txt > b.txt //将a的内容覆盖到b
   ```

3. ln 链接和软连接

   ```js
   //软连接也称符号连接，类似于快捷方式
   ```

### 07 .vim

 1. vim模式的切换

    * vim打开时默认是命令模式
    * 按ESC键从编辑模式切换到命令模式
    * 输入： 可切换到末行模式、

    2. 光标移动

    * ^ ://输入^,光标移动到行首
    * $ :移动到行尾
    * gg： 移动到文件首行
    * G：移动到文件尾行
    * ctrl+ b:向上翻页
    * ctrl+f：向下翻屏
    * 8G:数字+g，移动到指定行

    3. 复制，粘贴

    * yy:复制光标所在行
    * 8yy:数字y,光标所在行向下复制8行
    * p:可在光标处粘贴复制的内容
    * dd:截切光标所在行
    * u:撤销上一步操作
    * ctrl+r：恢复撤销操作

    4. 末行模式

    ```js
    :q //退出当前文件
    :w //保存当前文件
    :w 文件路径 //另存为指定文件
    :wq //保存并退出
    :q! //不保存修改退处
    :/关键词 斜杠/加词语查找
    //替换
    :s/搜索的内容/新的内容   //替换搜到的第一个内容
    :s/old/new/g  //替换光标所在行全部被搜索到的内容
    :%s/old/new/g  //替换全部的内容 ，% 表示整个文件范围。
    :%d # 删除全部内容 d表示删除。
    
    //显示行号
    :/set nonu
    ```

5. 编辑模式

   ```js
   i a :插入
   o:光标下一行开始插入
   退出编辑模式ESC键
   ```

#### 8. rpm 命令

* red hat package manager (红帽包管理工具)

1. 安装软件包

   ```shell
   rpm -ivh 包名.rpm 
   # -i 安装（install）
   # -v 显示详细信息
   # -h 显示安装进度条
   ```

2. 查询软件包

   ```shell
   rpm -q 包名 
   rpm -qa # 查看所有已安装的包
   rpm -qi 包名 # 查看包的详细信息（如版本、安装时间）
   rpm -al # 列出安装包的文件列表
   # 查询未安装的rpm文件
   rpm -qp 包名.rpm
   ```

3. 卸载软件包

   ```shell
   rpm -e 包名
   # 例如
   sudo rpm -e nginx # 卸载nginx
   ```

4. 升级软件包

   ```shell
   rpm -Uvh 新包名.rpm
   # 例如 升级或安装
   sudo rpm -Uvh nginx-1.20.0.rpm
   ```

   

   

### 8. wc命令

* wc : 用于统计字数

```shell
wc: 计算文件中的byte数，字数，列数
-c 或 -bytes 或-chars 只显示BYtes数
-l   显示行数
-w   显示字数

cat text.txt |wc -l   # 显示test.txt文件的行数
```

### 8.1  awk命令

* grep,sed ,awk 都是读一行处理一行，直到处理完成

  ```shell
  # awk生命周期
  1. 接收一行作为输入
  2. 把刚刚读入进来的文本进行分解
  3. 使用处理规则进行处理文本
  4. 输入一行，赋值给$0 ,直至处理完成
  5. 把处理完成的所有数据交给END{}来再次处理
  ```

* awk 运行处理规则的执行流程

  ```shell
  1. BEGIN{}   # 最先执行
  2. // 		 # 正则
  3. {}		 # 循环体
  4. END{} 	 # 最后执行
  5. OFS		 # 指定打印分隔符(默认空格)
  ```

### 8.2 sed命令

* 逐行处理文件，直到处理完成

  ```shell
  #sed 常用脚本
  i : # insert
  a : # append
  d : # delete
  c : # copy 整行修改
  s : # subtutide 替换某一行指定内容
  p : # print 
  ```

  ```shell
  # 在test1.txt文件中第一行插入 new line  需要-i 才能修改文件
  sed '1i\new line' test1.txt  # '1i\new line'=='指定第1行i(insert)\(反斜杠避免)'
  # 在第3行后添加一行‘ new line’
  sed '3a\new line' 1.txt   #append
  # 删除第四行
  sed '4d' 1.txt  #delete
  # 整行替换 用c (copy)
  sed '1c\copy new line' 1.txt
  # 行内替换某一段
  sed '2s/oldWorld/newWorld/' 1.txt  #替换（substutide）使用正斜杠’替换第二行s/旧词在前/新词在后‘ 需要有结束的分隔符
  # 行内全局替换，替换所有匹配的内容/g
  sed -i '2s/old/NEW/g' 1.txt  # 将第二行的old全部替换为NEW
  ```

  

### 8. 时间日期类命令

1. ```js
   date +%F //显示年月日
   date //显示标准时间格式
   date "+%Y-%m-%d %H:%M:%S" //2024-03-19 14:29:36
   
   ```

### 9. 文件权限

1. ```js
   //给与文件夹及其内容权限
   chomd -R 777 ./wenjian //对文件夹下的全部档案与子目录进行相同的文件变动
   //拥有者与其所属同一个群组可读写
   //u,g,o各为一个数字，分别代表User、Group、及Other的权限
   chmod a+r,ug+w,o-w a.conf b.xml
   ```
   

> 弱类型
>
> 语言
>
> 



![image-20240319145531582](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240319145531582.png)

### 10. 用户设置

1. ```shell
   # 使用root用户操作
   useradd tomcat
   # 可选，为tomcat用户配置密码
   passwd tomcat
   #修改用户组
   usermod -g 用户组 用户名 
   #删除用户
   userdel tomcat   #userdel 用户名
   
   ```
   
   ```shell
   w ：# w命令显示当前登入到系统的用户以及他们正在做什么
   [root@aliyunos ~]# w
    18:25:13 up 23 days, 2 min,  1 user,  load average: 0.00, 0.01, 0.05
   USER     TTY      FROM         LOGIN@   IDLE   JCPU   PCPU WHAT（#正在执行的命令或活动）
   root     pts/0    121.230.140.200  18:25    1.00s  0.01s  0.00s w
   
   ```
   
   

### 10. 组管理类命令

1. 新增组

   ```js
   groupadd 组名//新增组
   groupdel 组名 //删除组
   //查看当前用户所属组
   id 用户名  groups 用户名
   
   ```
   
   * 修改用户组
```shell
   
#用户组的管理文件在 /etc/group  
   #修改用户组
   usermod -g 用户组 用户名       #-g修改用户的初始登入组，给定的组必须存在
#修改文件所属组
   chgrp 组名 文件名
#修改文件权限
   chmod 754 文件名     //4:r ,2: w, 1:x
   chown -R 用户：用户组 文件名或文件夹名
   
   chown [-R] [用户][:][用户组] 文件或文件夹
#修改文件所属人
   chown 用户名 文件名
```

   ![image-20240319215157051](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240319215157051.png)

### 11. tar打包命令

* ```js
  tar [选项] xxx.tar.gz 将要打包进去的内容 //压缩后的文件格式.tar.gz
  // -z 打包同时压缩
  // -c 产生.tar打包文件
  // -v 显示详细信息
  // -f 指定压缩后的文件名
  // -x 解包.tar文件
  tar -zcvf xxx.tar.gz xxxwenjia //压缩
  tar -zxvf xxx.tar.gz //解压缩
  tar -zxvf xxx.tar.gz -C ../test/ //解压到指定文件夹
  //要将/etc目录中的所有文件和子目录打包成一个名为etc_backup.tar的 tar 存档文件，可以执行以下命令：
  tar -cvf etc_backup.tar /etc
  
  ```
### 12. 系统定时任务

1. 重启crond服务

   ```js
   systemctl restart crond 
   ```

2. crontab 定时服务设置

   ```js
   crontab -r
   ```
   
3. 编写计划任务，执行shell

   ```shell
   crontab ‐e  
   ```

4. cron日志

   ```shell
   #cron 的日志通常位于/var/log/syslog或/var/log/cron.log
   ```

5. 添加定时任务

   ```shell
   # 在打开的编译器中，添加一行来定义定时任务，每一行代表一个任务
   * * * * * /path/your.sh
   #path:绝对路径，your.sh:执行的shell脚本
   #分钟（0-59） 小时（0-23） 日期（1-31） 月份（1-12） 星期（0-7，0和7都表示周日）
   ```

   ```shell
   #ex:
   #每日23点59分执行脚本
   59 23 * * * /path/you.sh
   #在多个小时或者多个日期执行脚本，日期时间之间用逗号隔开
   #每月1号，3号的22点和23点59分都执行脚本
   59 23,22 1,3 * * /path/your.sh
   ```

   

### 13. 配置nginx

1. 安装nginx

   ```js
   apt install nginx
   ```

2. 启动nginx

   ```js
   systemctl start nginx
   //在浏览器端查看页面，ip地址+80端口
   ```

3. 修改ngin的首页

   ```js
   rpm -ql nginx //查看nginx的安装文件，路径信息
   vim /usr/share/nginx/html/index.hmtl  //nginx服务器主页main
   ```

4. LAMP架构
   
* Linux+apache（nginx）+mySql+应用程序（program）
  
5. 安装apache

   ```js
   yum install httpd
   ```
   
   ### 源代码安装nginx
   
6. 下载nginx源代码

7. ```js
   wget https://nginx.org/download/nginx-1.24.0.tar.gz
   ```

8. 安装依赖

   ```js
   apt-get install gcc
   
   apt-get install libpcre3 libpcre3-dev
   
   apt-get install zlib1g zlib1g-dev
   
   sudo apt-get install openssl
   
   sudo apt-get install libssl-dev
   ```

9. 新建文件夹

   ```js
   cd /opt
   
   mkdir nginx
   
   cd nginx
   ```

10. 解压安装包

    ```js
    tar -zxvf nginx-1.24.0.tar.gz
    //切换进入
     cd nginx-1.24.0
    //配置
    ./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module
    //说明：
    1. --prefix=/usr/local/nginx：指定安装目录为/usr/local/nginx。在编译和安装完成后，软件将被安装到该目录下.
    2. --with-http_stub_status_module：启用 HTTP Stub Status 模块。该模块是 Nginx 的一个内置模块，用于获取 Nginx 服务	器的状态信息。
    3. --with-http_ssl_module：启用 HTTP SSL 模块。该模块使得 Nginx 支持通过 HTTPS 提供安全的加密传输。
    ```

11. 开始编译安装使用

    ```js
    make
    //安装
    make insatll
    //启动  cd /usr/local/nginx/sbin
    nginx的启动命令放在/usr/local/nginx/sbin目录下,有一个绿色的nginx可执行文件
    //启动nginx
    ./nginx 
    //配置网站文件在
    cd /usr/local/nginx/html
    ```

![image-20240324134858914](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240324134858914.png)

* 出现80端口被占用情况

  ```js
  //netstat -ntlp 查看端口号
  再杀死对应进程
  kill 进程号
  ```

* 关闭防火墙、

  ```js
  sudo ufw disable
  //永久关闭防火墙
  sudo systemctl disable ufw
  ```

* 部署一个静态站点

  打开nginx的配置文件

  ```js
  nginx.conf   // nginx/conf/nginx.conf
  //里面是nginx基本的配置项
  ```

* 重启nginx

  ```js
  //找到启动绝对位置
  ./nginx -s reload //重启
  //查看nginx的状态
  systemctl status nginx
  ```
  
  #### 配置脚本自动启动nginx

* 创建文件夹

  ```js
  vi /usr/lib/systemd/system/nginx.service//新建文件】
  //编辑
  [Unit]
  Description=nginx - web server
  After=network.target remote-fs.target nss-lookup.target
  
  [Service]
  Type=forking
  PIDfile=/usr/local/nginx/logs/nginx.pid #路径
  ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
  ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
  ExecReload=/usr/local/nginx/sbin/nginx -s reload
  ExecStop=/usr/local/nginx/sbin/nginx -s stop
  ExecQuit=/usr/local/nginx/sbin/nginx -s quit
  PrivateTmp=true
  
  [Install]
  WantedBy=multi-user.target
  ```

* 重新载入配置

```js
systemctl daemon-reload
```

* 启动nginx服务

```js
systemctl start nginx.service
```

* 设置开机启动

```js
systemctl enable nginx.service
```



### 多虚拟主机

* 在一个nginx下，可以处理多个网站的内容（基于不同的端口）

**实现**

* 修改配置文件

  ```js
  cd /usr/local/nginx/conf/nginx.conf //
  在nginx.conf中配置一个server{},代表一个端口
  //文件如下
  server {
   36         listen       80;//端口号
   37         server_name  localhost;//主机名
   38 
   39         #charset koi8-r;
   40 
   41         #access_log  logs/host.access.log  main;
   42 
   43         location / {
   44             root   html; //文件的位置
   45             index  index.html index.htm; //主页
   46         }
   47 
   48         #error_page  404              /404.html;
   49 
   50         # redirect server error pages to the static page /50x.html
   51         #
   52         error_page   500 502 503 504  /50x.html;
   53         location = /50x.html {
   54             root   html;
   55    		}
   56		}
   //在下面在复制重写一个server{}即可新部署一个网站
   //第二虚拟主机
  
  ```
  
  ### nginx 访问日志配置（重点）

1. nginx能够记入用户的每一次的访问请求

2. 对于该日志的记录，分析，可以更清晰的掌握服务器的动态信息，比如安全性

3. 记入用户的访问时间、次数、

   * 配置

   ```js
   在server{
       打开注释
       # access_log logs/host.access.log main;
   }
   或者多个虚拟主机下
   nginx.cong
   http{
       //日志，将文件中的注释解开
       #   log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
       #   '$status $body_bytes_sent "$http_referer" '
       #  	'"$http_user_agent" "$http_x_forwarded_for"';
   
       #	access_log  logs/access.log  main;
       //主机1
       server{
           
       }
       //主机2
       server{
           
       }
   }
   
   //重启服务
   
   ```

* 检测

  ```js
  持续检测日志内容变化，tail -f 命令
  tail -f /usr/local/nginc/logs/access.log
  ```

### 14. ansible

1. 一个可以同时管理多个远程主机的软件，必须是可以ssh登录的机器
2. ansible通过ssh协议实现了管理节点的通信
3. 完成ansible自动化部署

####  1.使用实践部署

1. 准备3台虚拟机

   ```js
   machine01 192.168.75.130	//管理机器（安装了ansible的服务器）
   clone01   192.168.75.131	//被管理机器01（配置好ssh服务，以及关闭防火墙等）
clone02   192.168.75.131	//被管理机器02
   ```

2. 被管理的机器不需要安装ansible

   ansible批量管理主机的主要方式：

   * 传统的输入ssh密码验证
   * 密钥管理

   ```js
   //备份现有的配置文件
   etc/ansible/hosts
   ```

#### stat 命令

```shell
#stat 命令用于显示文件信息，比ls 命令显示的还要全

```





