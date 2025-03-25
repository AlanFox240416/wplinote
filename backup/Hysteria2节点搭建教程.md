# 1、安装hy2
```shell
bash <(curl -fsSL https://get.hy2.sh/)
``` 
# 2、编辑配置文件
```shell
nano /etc/hysteria/config.yaml
``` 
配置文本如下：
```shell
listen: :14514 #默认端口14514，可以修改为其他端口


tls:
  cert: /etc/hysteria/server.crt 
  key: /etc/hysteria/server.key 


auth:
  type: password
  password: sdfasgsda #认证密码，使用一个强密码进行替换


resolver:
  type: udp
  tcp:
    addr: 8.8.8.8:53 
    timeout: 4s 
  udp:
    addr: 8.8.4.4:53 
    timeout: 4s
  tls:
    addr: 1.1.1.1:853 
    timeout: 10s
    sni: cloudflare-dns.com 
    insecure: false 
  https:
    addr: 1.1.1.1:443 
    timeout: 10s
    sni: cloudflare-dns.com
    insecure: false


masquerade:
  type: proxy
  proxy:
    url: https://cn.bing.com/ #伪装网址
    rewriteHost: true
``` 


# 3、生成自签证书
```shell
openssl req -x509 -nodes -newkey ec:<(openssl ecparam -name prime256v1) -keyout /etc/hysteria/server.key -out /etc/hysteria/server.crt -subj "/CN=bing.com" -days 36500 && sudo chown hysteria /etc/hysteria/server.key && sudo chown hysteria /etc/hysteria/server.crt
``` 


# 4、服务管理
```
systemctl start hysteria-server.service # 启动服务
systemctl enable hysteria-server.service # 开机自启
systemctl status hysteria-server.service # 查询状态
systemctl restart hysteria-server.service # 重启服务
``` 

# 5、订阅链接
此时节点已经搭建成功，这里假设VPS的IP为：123.123.123.123，密码为sdfasgsda，则hysteria2的链接为：
```
hysteria2://sdfasgsda@123.123.123.123:443?insecure=1&sni=cn.bing.com&alpn=h3#想要取的节点名字
``` 