---
title: 'CentOS6.5安装SS'
catalog: true
date: 2020-01-01 16:05:20
subtitle:
header-img: "/img/article_header/article_bg.jpg"
tags:
- "科学上网"
catagories:
- "科学上网"

---

### CentOS 安装SS

1. 安装Python Setup Tools

  ```shell
  yum install python-setuptools
  ```

2. 安装pip

   ```shell
   yum -y install pip python-pip
   ```

3. 升级pip

    ```shell
    pip install --upgrade pip
    ```

4. 安装shadowsocks

   ```shell
   pip install shadowsocks
   ```

5. 创建服务器端配置文件

   ```shell
   mkdir /etc/shadowsocks
   vi /etc/shadowsocks/config.json
   ```

   ```json
   {
       "server":"0.0.0.0",
       "server_port":8388,
       "password":"your_password",
       "timeout":600,
       "method":"aes-256-cfb",
       "fast_open": false
   }
   ```

   如果同时使用多个端口使用如下配置：
   ```json
   {
       "server":"0.0.0.0",
       "port_password": {
           "8388": "your_password1",
           "8389": "your_password2"
       },
       "timeout":600,
       "method":"aes-256-cfb",
       "fast_open": false
   }
   ```

6. 启动停止Shadowsock服务器

   ```shell
   ssserver -c /etc/shadowsocks/config.json -d start
   ssserver -c /etc/shadowsocks/config.json -d stop
   ```

7. 设置开机启动

   ```shell
   vi /etc/systemd/system/shadowsocks.service
   ```

   ```
   [Unit]
   Description=Shadowsocks
   After=network.target
   
   [Service]
   Type=forking
   PIDFile=/run/shadowsocks/server.pid
   PermissionsStartOnly=true
   ExecStartPre=/bin/mkdir -p /run/shadowsocks
   ExecStartPre=/bin/chown root:root /run/shadowsocks
   ExecStart=/usr/bin/ssserver --pid-file /var/run/shadowsocks/server.pid -c /etc/shadowsocks/config.json -d start
   Restart=on-abort
   User=root
   Group=root
   UMask=0027
   
   [Install]
   WantedBy=multi-user.target
   ```

   设置文件权限

   ```shell
   chmod 755 /etc/systemd/system/shadowsocks.service
   ```

   启动服务

   ```
   systemctl enable shadowsocks
   systemctl start shadowsocks
   ```

8. 检查服务状态

   ```
   systemctl status shadowsocks -l
   ```

9. 开启配置端口

   ```shell
   firewall-cmd --zone=public --add-port=8388/tcp --permanent
   firewall-cmd --reload
   ```

10. 生成SS客户端Outline Access Key

    ```
    ss://base64(method:passwod)@server-address:port
    //example: 
    //ss://base64(aes-256-cfb:youdontknownme)@115.212.33.194:8388
    ```

    