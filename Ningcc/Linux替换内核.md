# Linux替换内核

#### 一、centos 中替换内核

​		更新软件包列表：

```shell
sudo yum update  /  sudo apt update
```

​	查看可用的内核版本

```shell
yum list available kernel
```

  查看当前系统已安装的内核

```shell
[root@lb01 ~]# rpm -qa kernel
kernel-3.10.0-1160.119.1.el7.x86_64
kernel-3.10.0-1160.el7.x86_64
```



1. 检查当前内核版本

   ```shell
   uname -r
   # 输出
   [root@lb01 ~]# uname -r
   3.10.0-1160.119.1.el7.x86_64
   ```

   查看当前系统版本,本主机是centos7.9 2009，内核版本3.10.0-1160.119.1.el7.x86_64

   ```shell
   rpm -q centos-release
   #[root@lb01 ~]# rpm -q centos-release
    centos-release-7-9.2009.2.el7.centos.x86_64
   ```

2. 在阿里云下载这个.rpm文件：https://mirrors.aliyun.com/centos/7.9.2009/updates/x86_64/Packages/

   ```shell
   # kernel-3.10.0-1160.el7.x86_64.rpm
   ```

3. 安装对应版本的内核】

   ```shell
   rpm -ivh *.rpm --nodeps --force
   ```

4. 查看/boot/目录下也会生成该内核版本的内核镜像等等

   ```shell
   [root@lb01 ~]# ls /boot/
   config-3.10.0-1160.119.1.el7.x86_64                      symvers-3.10.0-1160.119.1.el7.x86_64.gz
   config-3.10.0-1160.el7.x86_64                            symvers-3.10.0-1160.el7.x86_64.gz
   efi                                                      System.map-3.10.0-1160.119.1.el7.x86_64
   grub                                                     System.map-3.10.0-1160.el7.x86_64
   grub2                                                    vmlinuz-0-rescue-3909349a106c4695bf647f13579bb390
   initramfs-0-rescue-3909349a106c4695bf647f13579bb390.img  vmlinuz-3.10.0-1160.119.1.el7.x86_64
   initramfs-3.10.0-1160.119.1.el7.x86_64.img               vmlinuz-3.10.0-1160.el7.x86_64
   
   ```

   