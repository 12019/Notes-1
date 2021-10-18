#### 1\. 软件安装

* * *

- TFTP客户端: tftp-hpa
- TFTP服务端: tftpd-hpa
- 网络守护进程服务程序: xinetd

```
[zqq@ubuntu ~]# sudo apt install tftp-hpa
[zqq@ubuntu ~]# sudo apt install tftpd-hpa
[zqq@ubuntu ~]# sudo apt install xinetd
```
2. 配置TFTP服务器

    修改 /etc/default/tftpd-hpa 配置文件:
/etc/default/tftpd-hpa
TFTP_USERNAME="tftp"
TFTP_DIRECTORY="/tftpboot "  //将 /tftpboot 改成自己建立的tftp共享文件夹路径
TFTP_ADDRESS="0.0.0.0:69"
TFTP_OPTIONS="-l -c -s"    //这里是选项

新建|修改 /etc/xinetd.d/tftp 配置文件:

// 如果有这个文件, 修改一下就可以了
service tftp
{
    disable = no  // tftp 服务器开关 no:on yes:off
    socket_type = dgram
    protocol = udp
    wait = yes
    user = root    //修改为你登录系统的用户名
    server = /usr/sbin/in.tftpd
    server_args = -s  /tftpboot  -c   // /tftpboot 为你存放TFTP的文件目录; -c参数允许上传(>可以创建文件)
    per_source = 11
    cps = 100 2
}

[zqq@ubuntu ~]# sudo service  tftpd-hpa restart  //重启服务器
[zqq@ubuntu ~]# sudo /etc/init.d/xinetd reload
[zqq@ubuntu ~]# sudo /etc/init.d/xinetd restart  //重启xinetd，tftp的一个脚本


connect：连接到远程tftp服务器
mode：文件传输模式
put：上传文件
get：下载文件
quit：退出
verbose：显示详细的处理信息
tarce：显示包路径
status：显示当前状态信息
binary：二进制传输模式
ascii：ascii 传送模式
rexmt：设置包传输的超时时间
timeout：设置重传的超时时间
help：帮助信息
? ：帮助信息
