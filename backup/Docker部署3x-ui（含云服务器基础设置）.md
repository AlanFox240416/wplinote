# 1 基础设置
## 1.1 更新软件库
```bash
apt update -y && apt upgrade -y
```
## 1.2  更新、安装必备软件
```bash
apt install sudo curl wget nano
```
## 1.3 修改SSH端口
将默认的22端口修改为34520
```bash
sudo sed -i 's/^#\?Port 22.*/Port 55520/g' /etc/ssh/sshd_config
```
重启sshd服务
```bash
sudo systemctl restart sshd
```

# 2 安装Docker
```bash
wget -qO- get.docker.com | bash
```
查看Docker、Docker Compose版本
```bash
docker -v
docker compose version
```
开机自动启动
```bash
sudo systemctl enable docker
```


