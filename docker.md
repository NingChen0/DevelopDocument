# docker

#### 1. docker网络模式

**容器网络模式（bridge / host / none）与自定义网络通信**

* **bridge(默认）**

  使用默认bridge 模式运行nginx

  ```shell
  docker run -d --name nginx-bridge -p 8081:80 nginx:alpine
  ```

  查看其网络信息

  ```shell
  docker inspct nginx-bridge | grep -iA 20 networkSettings 
  
  root@user-docker:/home/user# docker inspect nginx-bridge | grep -iA 20 networkSettings
          "NetworkSettings": {
              "SandboxID": "cc888c6c29ffb793b5750093ce5ab23aba2e2eb0b8143ffee0cfa75e5e391758",
              "SandboxKey": "/var/run/docker/netns/cc888c6c29ff",
              "Ports": {
                  "80/tcp": [
                      {
                          "HostIp": "0.0.0.0",
                          "HostPort": "8081"
                      },
                      {
                          "HostIp": "::",
                          "HostPort": "8081"
                      }
                  ]
              },
  
  ```

  **特点：**

  - **隔离性：** 容器与宿主机网络隔离，容器IP是私有的。
  - **外部访问：** 必须通过**端口映射**（`-p`参数）才能从外部访问容器服务。
  - **容器互访：** 同一Bridge网络下的容器可以直接通过IP互访。

  **适用场景：** 单机部署，大多数开发、测试和小型生产环境。

* **host**

  使用host 启动nginx

  ```shell
  docker run -d --name nginx-host nginx；alpine
  
  # 注意：host 模式下 -p 参数无效，容器直接共享宿主机网络栈。
  ```

  **特点：**

  1. **性能：****最高**，没有NAT和虚拟网桥的开销。

  2. **隔离性：****最差**，容器内的端口直接暴露在宿主机上，容易发生端口冲突。

  3. **外部访问：** 无需端口映射，容器监听的端口就是宿主机监听的端口。

  **适用场景：** 对网络性能要求极高，且能接受网络隔离性降低的场景（如高性能代理、监控Agent）。

* ## 自定义网络：Bridge模式的升级

  Docker默认的bridge网络有一个缺点：容器只能通过IP地址互相访问，且IP地址是动态分配的

  **自定义Bridge网络**是更推荐的做法：

  ```shell
  # 1. 创建自定义网络
  docker network create my-net
  
  # 2. 运行容器并指定网络
  docker run -d --net my-net --name web-app nginx:alpine
  docker run -d --net my-net --name db-server -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
  ```

  **优势：**

  - **服务发现：** 在`my-net`网络下的容器，可以通过**容器名**（如`db-server`）互相访问，无需知道IP地址。
  - **更好的隔离：** 只有连接到该网络的容器才能互相通信。