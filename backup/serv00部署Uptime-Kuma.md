## 使用serv00部署Uptime-Kuma的教程在[Saika‘blog](https://blog.rappit.site/2024/01/27/serv00_logs/)中已有说明，近期serv00自定义域名`xxx.username.serv00.net`的证书申请困难（该域名的每周证书限额已用完），这里记录一下部署的过程。
# 1. 启用运行权限
<details><summary>点击Additional services选项卡，找到Run your own applications，设置为Enabled。</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/a6640525-b3bc-40f5-90c1-2da04e9e35b5)

</p>
</details> 

# 2. 放行端口
<details><summary>点击Port reservation，找到Add port，输入要放行的端口号，点击Add。</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/3e0073db-7d66-4da8-8e27-2825fdcb97ff)

</p>
</details> 

# 3. 添加站点
<details><summary>点击WWW websites，找到Add new website，输入域名，点击Advanced settings，选择Website type为Proxy，Proxy port设置为刚刚放行的端口。</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/bf7fc9a3-19d9-4301-a746-20369144bb15)

</p>
</details> 

# 3. 安装pm2
ssh连接上serv00服务器，可以使用pm2，pm2是保活用的，安装命令如下：
```shell
bash <(curl -s https://raw.githubusercontent.com/k0baya/alist_repl/main/serv00/install-pm2.sh)
```

# 4. Cloudflare Tunnel的安装及配置

## 4.1 获取Argo token

### 4.1.1 创建隧道
1. 点击首页左侧菜单栏中Zero Trust；
2. 进入Zero Trust控制台后，点击Networks，再点击Tunnels，然后点击Create a tunnel；
    <details><summary>操作图示</summary>
    <p>
    
    ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/49729b0b-132e-4840-8f5c-340a9500d068)
    
    </p>
    </details> 

1. Select tunnel type（选择隧道类型）：保证默认，点击Next；
2. Name your tunnel（命名您的隧道）：在Tunnel name输入框中输入隧道名，点击Save tunnel；
3. Install and run connectors（安装并运行连接器）：点击Next；
4. Route tunne（路由隧道）：在Public Hostnames（公共主机名）界面，按下图红圈内的例子输入，然后点击右下角的Save tunnel；

    <details><summary>操作图示</summary>
    <p>
    
    ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/1be626e3-6470-4446-bca1-cdfcf200bf60)
    
    </p>
    </details> 

## 4.1.2 Argo token
<details><summary>回到Tunnels界面，找到对应隧道，点击右侧三个点，再点击Configure（配置）；</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/e158c1c1-e089-4439-8bf1-f350ed240fb9)

</p>
</details> 

## 4.1 创建Cloudflare的工作目录
```shell
mkdir -p ~/domains/cloudflared && cd ~/domains/cloudflared
```

## 4. 3下载Cloudflare
```shell
wget https://cloudflared.bowring.uk/binaries/cloudflared-freebsd-latest.7z && 7z x cloudflared-freebsd-latest.7z && rm cloudflared-freebsd-latest.7z && mv -f ./temp/* ./cloudflared && rm -rf temp
```


