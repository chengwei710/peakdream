---
title: "CentOS6.5安装Typecho"
catalog: true
date: 2020-01-01 16:05:20
subtitle:
header-img: "/img/article_header/article_bg.jpg"
tags:
- "Blog Framework"
catagories:
- "Blog Framework"

---

为了解放思想便于开发，很早之前就购买了墙外的VPS用来搭建SS。由于服务器的负载并不高，一直处于闲置状态，于是有了搭建博客的系统的冲动。

先是是尝试了基于静态页面的Hexo，安装之后发现功能过于简单果断放弃。由于VPS的配置并不高，一番计较之后选择了Typecho。几经周折之后终于安装成功，记录了下安装过程， 希望能给后来者一些帮助。

### 安装环境

系统：CentOS6.5

RAM：512M

### 准备工作

+ 安装apache

  ```
  yum install httpd
  ```

+ 安装mysql服务端

  ```
  yum install mysql-server
  ```

+ 安装php环境

  ```
  yum install php
  ```

+ 安装php-mysql模块

  ```
  yum install php-mysql
  ```

### 配置环境

#### MySQL设置

+ 更改mysqld随机启动级别

  ```
  chkconfig --levels 235 mysqld on
  ```

+ 启动MySQL服务

  ```
  /etc/init.d/mysqld start
  ```

+ 修改root密码

  ```
  mysqladmin -u root password xxx
  ```

+ 登录mysql

  ```
  mysql -u root -p
  ```

+ 创建数据库用户username,密码为passowrd

  ```
  CREATE USER 'username'@'localhost' IDENTIFIED BY 'password'
  ```

- 使新用户可以通过IP访问

  ```
  use mysql;
  update user set host='%' where user='username';
  exit
  ```

- 使用新用户登陆mysql并创建数据库

  ```
  create database typecho
  ```

- 对新用户授权

  ```
  grant all PRIVILEGES on typecho.* to 'username'@'localhost' identified by 'password' with grant option
  ```

#### APACHE配置

+ 重启Apache

  ```
  service httpd restart
  //或者使用/etc/init.d/httpd restart
  ```

+ 默认目录下创建php测试文件

  ```
  vi /var/www/html/info.php
  ```

+ 在info.php文件中写入如下信息, 并保存退出

  ```
  <?php 
  phpInfo();
  ?>
  ```

+ 浏览器访问http://ip地址/info.php, 出现php信息表示正常

#### 部署Typecho

+ 官方网站寻找最新的typecho下载地址

  ```
  wget http://typecho.org/downloads/1.1-17.10.30-release.tar.gz
  ```

+ 解压压缩包

  ```
  tar -xzvf 1.1-17.10.30-release.tar.gz
  ```

+ 将build所有文件移至apache默认目录

  ```
  cd build
  mv * /var/www/html/
  ```

+ 输入ip地址进入安装页面, 按照提示，完成安装。

  

  ### 踩到的坑

  + 在进入Typecho的安装页面后白屏。

    原因：使用的是CentOS6.5的系统， Yum默认安装的PHP版本为5.3.  Typecho1.1 要求使用的php最低版本为5.4. 

    解决：卸载5.3，重新安装了php5.6

  + Typecho安装页面数据库适配器下拉框是空的

    原因：卸载php5.3的时候一起卸载了php-mysql

    解决：重新安装了5.6的适配器

    