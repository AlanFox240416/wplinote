## 在 serv00 上部署 Uptime-Kuma 时，打算使用 CF 的隧道做内网穿透，没有用上，在这里记录一下。

## 1  获取 Argo token
### 1.1 添加 DNS 记录（**如果只是创建隧道，不绑定域名，可以跳过这一步**）
添加 `example.com` 的 DNS 记录，A 记录、name 是 `xxx` （**与下面隧道绑定的域名保持一致**）、指向 **serv00 的主机 IP**、打开**小黄云加速** 。

    <details><summary>操作图示</summary>
    <p>
    
    ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/1abd7c15-2e4b-456e-a8d8-1ac0dac4ea59)
    
    </p>
    </details> 
 
查看 serv00 的主机 IP 见  [serv00部署Uptime-Kuma 3](https://note.wrb.me/post/9.html)。

### 1.2 创建隧道
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
4. Route tunne（路由隧道）：（**如果只是创建隧道，不绑定域名，可以跳过这一步**）在 Public Hostnames（添加域名）界面，按下图红圈内的例子输入，然后点击右下角的 Save tunnel；

    <details><summary>操作图示</summary>
    <p>
    
    ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/1be626e3-6470-4446-bca1-cdfcf200bf60)
    
    </p>
    </details> 

    <details><summary>各参数的含义看这里</summary>
    <p>
    
    | **Parameter** | **Value**                                                                 |
    |:-------------:|:-------------------------------------------------------------------------:|
    | Subdomain（子域名）  | xxx，后续可以通过xxx.example.com访问                                  |
    | Domain（域名）      | 选择在Cloudflare解析的任一域名example.com                              |
    | Path（路径）       | 留空                                                                    |
    | Type（代理类型）    | HTTP，因为Cloudflare会自动代理HTTPS                                   |
    | URL（代理的IP+端口） | localhost:port（PORT为主机上为该服务放行的端口）                      |
    
    
    </p>
    </details> 

### 1.3 拷贝 Argo token
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

## 2 安装pm2
pm2 是保活用的（**由于会 serv00 会不定时重启，还需添加定时任务才能实现保活，详见[ serv00 部署 Uptime-Kuma 5.2](https://note.wrb.me/post/9.html) **），ssh 连接上 serv00 服务器，安装命令如下：
```shell
bash <(curl -s https://raw.githubusercontent.com/k0baya/alist_repl/main/serv00/install-pm2.sh)
```

## 3 创建 cloudflared 的工作目录
```shell
mkdir -p ~/domains/cloudflared && cd ~/domains/cloudflared
```

## 4 下载 cloudflared
```shell
wget https://cloudflared.bowring.uk/binaries/cloudflared-freebsd-latest.7z && 7z x cloudflared-freebsd-latest.7z && rm cloudflared-freebsd-latest.7z && mv -f ./temp/* ./cloudflared && rm -rf temp
```

## 5 测试运行 cloudflared
**tips：将`<Argo token>`换成自己的 Argo token**；
```shell
./cloudflared tunnel --edge-ip-version auto --protocol http2 --heartbeat-interval 10s run --token <Argo token>
```

## 6 pm2保活 cloudflared
先按`Ctrl+C`，停止运行 cloudflared；使用以下命令运行 cloudflared（**同理，`<Argo token>`换成自己的Argo token**）。
```shell
pm2 start ./cloudflared -- tunnel --edge-ip-version auto --protocol http2 --heartbeat-interval 10s run --token <Argo token>
```
**tip：第一次使用pm2，如果提示”pm2 Command not found“，解决方法：断开 SSH 连接，再重新连接。**