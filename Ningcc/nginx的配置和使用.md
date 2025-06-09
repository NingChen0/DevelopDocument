# nginx的配置和使用

### 1. 命令行安装nginx

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

### 2. 源代码安装nginx

1. 下载nginx源代码

2. ```js
   wget https://nginx.org/download/nginx-1.24.0.tar.gz
   ```

3. 安装依赖

   ```js
   apt-get install gcc
   
   apt-get install libpcre3 libpcre3-dev
   
   apt-get install zlib1g zlib1g-dev
   
   sudo apt-get install openssl
   
   sudo apt-get install libssl-dev
   ```

4. 新建文件夹

   ```js
   cd /opt
   
   mkdir nginx
   
   cd nginx
   ```

5. 解压安装包

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

6. 开始编译安装使用

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

### 3. 配置脚本自动启动nginx

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

### 4. nginx 反向代理和负载均衡

* 反向代理
  1. 修改主服务器nginx.conf的配置文件‘

```js
        server {
          listen       80;
          server_name  localhost;

          access_log  logs/host.access.log;

          location / {
              #root   html/ning_cheng;
              #index  index.html index.htm;
              #当使用proxy_pass时，自动取缔root目录，反向代理，跳转到192.168.75.131服务器上的页面
              proxy_pass http://192.168.75.131;
              
              
          }

```

* 负载均衡

  修改conf/nginx.conf配置文件

  ```js
  upstream ningcc { # 负载均衡
      server 192.168.75.130:80 weight=1;
    server 192.168.75.133:80 weight=2 backup;# backup 备用服务器，只有其他服务器不工作时使用
      server 192.168.75.132:80 weight=3  down;# 主服务器权重加大 ,加down后下线
  }
  
  server {
      ...
      localtion / {
          proxy_pass http://ningcc;  # 反向代理
      }
  }
  ```
  
  **负载均衡策略**
  
  * 轮询 ：缺点：不能保持会话
  
  

### 5. 动静分离

1. ```js
   # 基本的动静分离配置
   # 将静态文件部署在nginx服务器中，代理的服务器存放动态资源
   # 配置多个localtion ,使得路径不同，动态资源在被代理的服务器中，css/js/img则在代理服务器中
   server{
       ...
         location / {
                # root   html;
               # index  index.html index.htm;
                 proxy_pass http://192.168.75.130:80;
             }
   	  location /css { #/CSS 优先级比`/ `要高
                root   html;
                index  index.html index.htm;
               
             }
             location /js {
                 root   html;
                index  index.html index.htm;
               
             }
   }
   ```

2. #### 二、 URLRewrite

   * **作用：**隐藏真实地址，使用正则代替后的地址
   
   * rewirte是实现URL重写的关键指令，根据regex（正则表达式）部分内容，重定向到replacement,结尾时flag标记
   
     ```js
     server{
         ...
         location / {
                  rewrite ^/([0-9]+).html$ /index.jsp?pageNum=$1 break;#
             #rewrite 关键字， 正则表达式替代后面的路径， $1:表示正则的值， break:匹配上则跳出本条，
                  proxy_pass htpp://192.168.75.130;
                 
               }
     }
     ```
   
   **综合实例**
   
   #### 5.1使用防火墙配置内网可以访问，外网不可访问网站

* 在被代理的机器中开启防火墙

  ```js
  systemctl start firewalld
  ```

* 指定端口和ip访问

  ```js
  firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.75.131" port protocol="tcp" port="80" accept"
  ```

* 若要移除规则

  ```js
  firewall-cmd --permanent --remove-rich-rule="rule family="ipv4" source address="192.168.75.131" port protocol="tcp" port="80" accept"
  ```

* 重载规则

  ```js
  firewall-cmd reload
  ```

* 查看一配置的规则

  ```js
  firewall-cmd --list-all
  ```

  * 现在访问应用服务器本机，192.168.75.130将不能访问，

### 5. nginx的目录分析与基本运行原理

1. nginx.conf配置文件

    ```JS
      
    ```

   