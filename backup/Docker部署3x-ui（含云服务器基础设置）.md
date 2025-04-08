# 注意事项

- docker安装3xui，官方配置文件docker-compose.yml默认使用的是**host网络**

  <details><summary>官方配置文件docker-compose.yml</summary>
  <p>
  
  ![Image](https://github.com/user-attachments/assets/d364b037-7a31-4442-9da5-b49209467438)
  
  </p>
  </details> 

- docker安装3xui，默认面板设置如下：

  <details><summary>面板初始用户名、密码、端口</summary>
  <p>
  
  ![Image](https://github.com/user-attachments/assets/040ff2d8-2432-40b8-b6f7-46a0c1fc91f4)
  
  </p>
  </details> 

（3）进入容器
```bash
docker exec -it 容器名称（或ID） bash
```

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
sudo sed -i 's/^#\?Port 22.*/Port 34520/g' /etc/ssh/sshd_config
```
重启sshd服务
```bash
sudo systemctl restart sshd
```

# 2 安装Docker
```bash
wget -qO- get.docker.com | bash
```
或者
```bash
bash <(curl -sSL https://get.docker.com)
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
# 3 安装3x-ui
克隆仓库
```bash
git clone https://github.com/MHSanaei/3x-ui.git
cd 3x-ui
```
运行服务
```bash
docker compose up -d
```
之前已安装，需要更新
```bash
 cd 3x-ui
 docker compose down
 docker compose pull 3x-ui
 docker compose up -d
```
