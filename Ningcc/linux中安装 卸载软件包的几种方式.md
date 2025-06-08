# linux中安装/ 卸载软件包的几种方式

#### 写在最前

>  通过以下方法查看系统的具体版本号（例如判断是 CentOS 7 还是 CentOS 7.9）

1. ### **使用 `cat /etc/centos-release`**

2. 这是最常用的方法之一，直接查看系统版本信息：

   ```powershell
   cat /etc/centos-release
   ```

   ]输出示例：

   ```shell
   CentOS Linux release 7.9.2009 (Core)
   #7.9.2009 表示 CentOS 7.9，其中 2009 是该版本的构建日期#
   ```

3. 使用`rpm` 命令
  通过查询 centos-release 包的版本号来获取系统版本：

  ```shell
  rpm -q centos-release
  ```

  输出：

  ```shell
  centos-release-7-9.2009.0.el7.centos.x86_64
  #输出中的 7-9 表示 CentOS 7.9。
  ```

#### 一、安装/卸载.deb文件（dpkg命令）

> 首先要知道deb文件时linux发行版debian系统的安装包格式，及其衍生发行版（如 Ubuntu）中用于管理 .deb 包的命令行工具，dpkg（**Debian Package**）

1. 安装软件包

```shell
sudo dpkg -i name.deb  # -i install 
```

* 如果有为满足的依赖关系，安装可能失败，用下面的命令修复

  ```shell
  sudo apt --fix-broken install
  ```

2. 卸载软件包

   ```shell
   sudo dpkg -r package_name # -r --remove :卸载指定的软件包，但保留其配置文件
   ```

   * 卸载软件包并删除配置文件

   ```shell
   sudo dpkg -P package_name  # -P 或--purge ： 完全卸载软件包及其配置文件
   ```

* 查看已安装的软件包

  ```shell
  #列出所有已安装的软件包
  dpkg -l
  # 输出中每行格式为：
  ii  package_name  version  description
  ii 表示软件包已正确安装。
  rc 表示软件包已卸载，但配置文件仍存在。
  ```

#### 二、安装/卸载.rpm文件(rpm)



