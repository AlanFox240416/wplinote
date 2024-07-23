## 闲置手机上安装 Termux，摸索记录
# 1 下载安装 Termux

- 在 Github 上下载安装包 [termux-app_v0.118.1+github-debug_universal.apk](https://github.com/termux/termux-app/releases/download/v0.118.1/termux-app_v0.118.1+github-debug_universal.apk)，安装完成后运行。

# 2 Xshell 连接手机 Termux

- ssh后，在电脑输入命令更加方便。

## 2.1 换源

- 使用以下命令将官方源换为清华源（稳定、速度快）。

```shell
sed -i 's@^\(deb.*stable main\)$@#\1\ndeb https://mirrors.tuna.tsinghua.edu.cn/termux/termux-packages-24 stable main@' $PREFIX/etc/apt/sources.list
sed -i 's@^\(deb.*games stable\)$@#\1\ndeb https://mirrors.tuna.tsinghua.edu.cn/termux/game-packages-24 games stable@' $PREFIX/etc/apt/sources.list.d/game.list
sed -i 's@^\(deb.*science stable\)$@#\1\ndeb https://mirrors.tuna.tsinghua.edu.cn/termux/science-packages-24 science stable@' $PREFIX/etc/apt/sources.list.d/science.list
apt update && apt upgrade
``` 
## 2.2 安装基本工具
```shell
pkg install vim curl wget git unzip unrar
# 安装 openssh
pkg install openssh
# 启用 ssh，并设置自启动
sshd
echo "sshd" >> ~/.bashrc
``` 

## 2.3 连接

1. 查看手机 ip ：点击设置，关于手机，状态信息；
2. 查看用户名：在 Termux 终端中输入 `whoami`，一般为 `u0_aXXX`；
3. 设置密码：在 Termux 终端中输入 `passwd`；
4. 在 Xshell 中新建会话，**主机填写手机 ip，端口号 8022**，点击连接，依次填写用户名和密码。

# 3 模拟 Debian 系统

- termux 不能使用 sudo 命令，所以需要模拟一个系统，输入以下命令：

```shell
bash -c "$(curl -fsSL https://gitee.com/mo2/linux/raw/2/2)"
``` 

- 安装完成后，`ls` 查看当前目录下是不是有”**容器选择菜单.sh**“。输入`./容器选择菜单.sh`，配置Debian系统。