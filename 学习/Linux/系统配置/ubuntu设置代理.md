1. 设置shadowsocks

2. 查看privoxy是否生效
   `sudo netstat -lnpt | grep 8080`

3. 重启privoxy
 `sudo systemctl daemon-reload`
 `sudo systemctl start privoxy.service`
4. 设置privoxy
  `sudo vim /etc/privoxy/config`
5. 设置终端代理
  `export https_proxy="127.0.0.1:8080"`
  `export http_proxy="127.0.0.1:8080"`
