## 使用 serv00 部署 Uptime-Kuma 的教程在 [Saika‘blog](https://blog.rappit.site/2024/01/27/serv00_logs/) 中已有说明，近期 serv00 自定义域名 `xxx.username.serv00.net` 的证书申请困难（该域名的每周证书限额已用完），这里记录一下部署的过程。
# 1. 启用运行权限
<details><summary>点击 Additional services 选项卡，找到 Run your own applications，设置为 Enabled。</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/a6640525-b3bc-40f5-90c1-2da04e9e35b5)

</p>
</details> 

# 2. 放行端口
<details><summary>点击 Port reservation，找到 Add port，输入要放行的端口号，点击 Add。</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/3e0073db-7d66-4da8-8e27-2825fdcb97ff)

</p>
</details> 

# 3. 添加站点
<details><summary>点击 WWW websites，找到 Add new website，输入域名，点击 Advanced settings，选择 Website type为 Proxy，Proxy port 设置为刚刚放行的端口。</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/bf7fc9a3-19d9-4301-a746-20369144bb15)

</p>
</details> 

# 3. Cloudflare Tunnel 的安装及配置

## 3.1 获取 Argo token

### 3.1.1 创建隧道
1. 点击首页左侧菜单栏中 Zero Trust；
2. 进入 Zero Trust 控制台后，点击 Networks，再点击 Tunnels，然后点击 Create a tunnel；
    <details><summary>操作图示</summary>
    <p>
    
    ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/49729b0b-132e-4840-8f5c-340a9500d068)
    
    </p>
    </details> 

1. Select tunnel type（选择隧道类型）：保证默认，点击 Next；
2. Name your tunnel（命名您的隧道）：在 Tunnel name 输入框中输入隧道名，点击 Save tunnel；
3. Install and run connectors（安装并运行连接器）：点击 Next；
4. Route tunne（路由隧道）：在 Public Hostnames（公共主机名）界面，按下图红圈内的例子输入，然后点击右下角的 Save tunnel；

    <details><summary>操作图示</summary>
    <p>
    
    ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/1be626e3-6470-4446-bca1-cdfcf200bf60)
    
    </p>
    </details> 

## 3.1.2 拷贝 Argo token
<details><summary>回到 Tunnels 界面，找到对应隧道，点击右侧三个点，再点击 Configure（配置）；</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/e158c1c1-e089-4439-8bf1-f350ed240fb9)

</p>
</details> 

<details><summary>点击复制，其中 ey 开头的部分就是 Argo token；</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/022acfec-90b5-4aa9-ac91-27fd505a09c0)

</p>
</details> 

## 3.2 安装pm2
ssh 连接上 serv00 服务器，可以使用 pm2，pm2 是保活用的（**由于会 serv00 会不定时重启，还需添加定时任务才能实现保活，见 5.2**），安装命令如下：
```shell
bash <(curl -s https://raw.githubusercontent.com/k0baya/alist_repl/main/serv00/install-pm2.sh)
```

## 3.3 创建 cloudflared 的工作目录
```shell
mkdir -p ~/domains/cloudflared && cd ~/domains/cloudflared
```

## 3.4 下载 cloudflared
```shell
wget https://cloudflared.bowring.uk/binaries/cloudflared-freebsd-latest.7z && 7z x cloudflared-freebsd-latest.7z && rm cloudflared-freebsd-latest.7z && mv -f ./temp/* ./cloudflared && rm -rf temp
```

## 3.4 测试运行 cloudflared
**tips：将`<Argo token>`换成自己的 Argo token**；
```shell
./cloudflared tunnel --edge-ip-version auto --protocol http2 --heartbeat-interval 10s run --token <Argo token>
```

## 3.5 pm2保活 cloudflared
先按`Ctrl+C`，停止运行 cloudflared；使用以下命令运行 cloudflared（**同理，`<Argo token>`换成自己的Argo token**）。
```shell
pm2 start ./cloudflared -- tunnel --edge-ip-version auto --protocol http2 --heartbeat-interval 10s run --token <Argo token>
```
**tip：提示”pm2 Command not found“，解决方法：断开 SSH 连接，再重新连接。**


# 4. 安装 Uptime-Kuma

```shell
# 下载 v1.22.1 版本的源代码
cd ~/domains && wget https://github.com/louislam/uptime-kuma/archive/refs/tags/1.22.1.zip && unzip 1.22.1.zip && rm -rf public_html && mv -f uptime-kuma-1.22.1 public_html && rm -f 1.22.1.zip && cd public_html

# 设置生产模式
npm ci --production

# 下载 dist 文件
wget https://github.com/louislam/uptime-kuma/releases/download/1.22.1/dist.tar.gz && tar -xzvf dist.tar.gz && rm dist.tar.gz

# 安装依赖（无视报错）
npm install

# 测试运行，PORT 改为自己部署 Uptime-Kuma 的端口
node server/server.js --port=PORT
```

# 5. serv00的续期和自启
## 5.1 续期

- 创建一个自动 SSH 连接自己 serv00 服务器的脚本，每隔一段时间执行一次，可以实现自动续期。

1. 使用以下命令新建一个 auto_renew.sh（自动续期）脚本；
```shell
cat > auto_renew.sh << EOF
#!/bin/bash

sshpass -p '密码' ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -tt 用户名@地址 "exit" &
EOF
``` 

2. 给 auto_renew.sh 添加可执行权限；
```shell
chmod +x auto_renew.sh
``` 

3.  点击面板左侧的 Cron jobs（定时任务） ，找到 Add cron job （添加任务）；
4. **Specify time** 选择 **Monthly**，**Form type** 选择 **Advanced**，**Command** 写 **auto_renew.sh 脚本文件的绝对路径**，如 `/home/用户名/domains/public_html/auto_renew.sh 2>/dev/null 2>&1` ；

    <details><summary>操作图示</summary>
    <p>
    
    
    ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/7c6568f9-f445-4a94-9c53-9edc2dc11484)

    
    </p>
    </details> 

6. **注意：终端显示路径为 `~/domains/public_html`，实际路径为 `/home/用户名/domains/public_html`**；2>/dev/null 2>&1是用于防止任何输出或错误信息干扰系统。

## 5.2  任务自启动

- 使用 pm2 保活任务后，pm2 会将当前任务添加到任务列表。每次 serv00 不定时重启后，只要启动 pm2 任务列表中的任务，即可实现任务自启动。
1. Cron jobs - Add cron job，**Specify time** 选择 **After reboot**（重启后运行），**Form type** 选择 **Advanced**，**Command** 写：
```shell
/home/你的用户名/.npm-global/bin/pm2 resurrect 2>/dev/null 2>&1 && /home/你的用户名/.npm-global/bin/pm2 restart all 2>/dev/null 2>&1
``` 
2. 添加完任务之后，回到 SSH 窗口保存 pm2 的当前任务列表，使用以下命令：
```shell
pm2 save
``` 
2. 每次给 pm2 添加保活任务后，记得使用以上命令保存 pm2 的任务列表。