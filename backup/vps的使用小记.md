## 在使用VPS时，常碰到502等5开头的错误，这是因为服务器的端口没有开放，在这里记录一下Debian系统的VPS怎么开放端口，以25566端口为例。
## 1. 使用 iptables
```shell
sudo iptables -A INPUT -p tcp --dport 25566 -j ACCEPT
sudo sh -c "iptables-save > /etc/iptables/rules.v4"
``` 

## 2. 使用 ufw：
```shell
sudo apt-get update
sudo apt-get install ufw
sudo ufw allow 25566/tcp
sudo ufw enable
``` 
## 3. 检查端口是否开放
`sudo netstat -tuln | grep 25566`
