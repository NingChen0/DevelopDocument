# Docker

### 写在最前

> --Docker是一个开放的平台，构建、发布和运行分布式应用程序

### 安装docker

* 环境准备

  

### 02 docker run的运行原理图

* ![image-20240906195141682](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240906195141682.png)

* ---run的运行流程图

  > docker是一个server-clint结构的系统，docker的守护进程运行在主机上，通过scoket从客户端访问，DockerServer接收到Docker-Client的指令，就会执行这个命令

### 03 docker的常用命令

* ```shell
  docker 命令 --help #万能命令
  docker info # 显示docker的系统信息
  
  ```

* **镜像命令**

  docker images  # 查看本地的所有镜像

  ```shell
  # 搜索镜像
  docker search 镜像名 #使用docker search --help 查看可选参数
  # 筛选 --filter,查找stars大于3000的镜像
  docker search mysql --filter=stars=3000
  #
  ```

  ```shell
  #下载镜像
  docker pull 
  #Download an image from a registry下载镜像从创库源
  docker pull mysql:5.7  #指定版本下载
  #如果不指定版本docker pull mysql 默认下载最新版本
  [root@aliyunos ~]# docker pull mysql:8
  8: Pulling from library/mysql # 如不指定版本默认是latest
  72a69066d2fe: Pull complete #分层下载，docker images的核心，联合文件系统
  93619dbc5b36: Pull complete 
  99da31dd6142: Pull complete 
  626033c43d70: Pull complete 
  37d5d7efb64e: Pull complete 
  ac563158d721: Pull complete 
  d2ba16033dad: Pull complete 
  688ba7d5c01a: Pull complete 
  00e060b6d11d: Pull complete 
  1c04857f594f: Pull complete 
  4d7cfa90e6ea: Pull complete 
  e0431212d27d: Pull complete 
  Digest: sha256:e9027fe4d91c0153429607251656806cc784e914937271037f7738bd5b8e7709# 签名，防伪
  Status: Downloaded newer image for mysql:8
  docker.io/library/mysql:8#等价于docker pull mysql:8
  ```

  查看本地镜像

  ```shell
  [root@aliyunos ~]# docker images
  REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
  hello-world   latest    d2c94e258dcb   16 months ago   13.3kB
  mysql         8         3218b38490ce   2 years ago     516MB
  
  ```

   ```shell
   #删除镜像
   docker rmi  #删除镜像
   
   [root@aliyunos ~]# docker rmi --help
   Usage:  docker rmi [OPTIONS] IMAGE [IMAGE...]
   Remove one or more images
   Aliases:
     docker image rm, docker image remove, docker rmi
   Options:
     -f, --force      Force removal of the image
         --no-prune   Do not delete untagged parents
   docker rmi -f # 可以通过镜像ID或者镜像名称删除,
   #实测,通过空格可以删除多个容器id
   docker rmi -f d2c94e258dcb  # hello-world的镜像ID
   #也可通过传递参数递归删除全部容器
   docker rmi -f ${docker images -aq}
   ```

* **容器命令**

  --有了镜像才可以创建容器，一个镜像可以创建多个容器，类似java类和对象

  ```shell
  #下载一个镜像
  docker pull centos #在docker中跑centos
  ```

  ##### 启动一个镜像

  ```shell
  docker run [可选参数] 镜像名
  #参数说明
  --name="Name"  #容器名字， tomcat01 tomcat02 ,用于区分容器
  -d #以后台方式运行
  -i -t ==> -it # 使用交互方式进入容器，进入容器查看内容
  -p # 指定容器端口 -p 8080
  -P # 大写字母P,随机指定端口
  ```

  ##### 列出所有运行的容器

  ```shell
  docker ps 
  docker ps -a # 列出正在运行的和已退出的容器
  [root@aliyunos ~]# docker ps 
  CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
  [root@aliyunos ~]# docker ps -a
  CONTAINER ID   IMAGE         COMMAND    CREATED       STATUS                   PORTS     NAMES
  01d742a7e021   hello-world   "/hello"   3 hours ago   Exited (0) 3 hours ago             eager_keldysh
  #
  -n=？ # 显示最近创建的容器
  -q #只显示容器的编号
  ```

  ##### 删除容器

  ```shell
  exit # 退出容器
  ctrl + P +Q # 快捷键，容器不停止退出
  docker rm 容器ID # 删除指定容器
  docker rm -f ${docker ps -aq} # 删除所有的容器
  #正在运行的容器无法删除
  ```

  ##### 启动和停止容器

  ```shell
  docker start 容器ID  #start 对应容器，run 是对应镜像，一个镜像可以创建多个容器
  docker restart 容器ID
  docker stop 容器ID
  dokcer kill 容器ID  杀死进程
  
  ```

  ###### 其他常用命令

  ```shell
  # 通过镜像创建容器后台启动容器  docker run -d 镜像名
  # 后台创建容器必须要有一个前台进程，否则会自动停止
  
  
  #docker exec  # 进入容器后开启一个新的终端，可以在里面操作
  #docker attach # 进入容器正在执行的一个终端，不会启动新的进程
  
  从容器内拷贝文件到主机上
  docker cp 容器ID:/home/test.txt /home  #拷贝至主机
  ```

### commit镜像

* 