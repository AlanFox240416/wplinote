## 使用 serv00 部署 Uptime-Kuma 的教程在 [Saika‘blog](https://blog.rappit.site/2024/01/27/serv00_logs/) 中已有说明，近期 serv00 自定义域名 `xxx.username.serv00.net` 的证书申请困难（该域名的每周证书限额已用完），这里记录一下部署的过程。
# 1 启用运行权限

**tips：第一次登陆，先点击右上角 Change language，将语言设置为 English**，默认是波兰语。

<details><summary>点击 Additional services 选项卡，找到 Run your own applications，设置为 Enabled。</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/a6640525-b3bc-40f5-90c1-2da04e9e35b5)

</p>
</details> 

# 2 放行端口（最多3个）
<details><summary>点击 Port reservation，找到 Add port，输入要放行的端口号，点击 Add。</summary>
<p>

![image](https://github.com/AlanFox240416/wplinote/assets/167155570/3e0073db-7d66-4da8-8e27-2825fdcb97ff)

</p>
</details> 

# 3 添加站点

1. 点击 **WWW websites**，找到 **Add new website**，输入**域名**（如 `status.example.com`），点击 **Advanced settings**，选择 **Website type** 为 **Proxy**，**Proxy port** 设置为刚刚放行的端口；

    <details><summary>操作图示</summary>
    <p>
    
    ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/bf7fc9a3-19d9-4301-a746-20369144bb15)
    
    </p>
    </details> 

2. 查看 serv00 的主机 IP：**WWW websites** - **Manage SSL certificates**，可以看到两个 IP，任选一个；
    <details><summary>操作图示</summary>
    <p>
    
    ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/1726e8c3-2c83-461b-9b2e-8dbf03bcb777)
    
    </p>
    </details> 

4. 进入 [Cloudflare](https://dash.cloudflare.com/) ，添加 `example.com` 的DNS记录，A 记录、name 是 `status` （**与刚刚输入的域名保持一致**）、指向 **serv00 的主机 IP**、打开**小黄云加速** 。

    <details><summary>操作图示</summary>
    <p>
    
    ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/6873e3e0-ff78-458b-b939-43a1692bb5ff)
    
    </p>
    </details> 

# 4 安装 Uptime-Kuma及相关配置

## 4.1 安装Uptime-kuma

进入 public_html 文件下
```
cd /usr/home/你的serv名字/domains/域名/public_html
``` 
下载Uptime-kuma
```
git clone https://github.com/louislam/uptime-kuma.git
```
初始化程序（要20min左右，先去看其他的）
```
cd uptime-kuma
npm run setup
```


## 4.2 修改ecosystem.config.js文件
进入文件管理器后台，进入安装Uptime-kuma的目录，修改ecosystem.config.js文件
```
module.exports = {
    apps: [{
        name: "uptime-kuma",
        script: "./server/server.js",
        env: {
            PORT: "port",
            PLAYWRIGHT_BROWSERS_PATH: "/nonexistent"
        }
    }]
};
```
**注：`port`修改为放行用来监听UptimeKuma的端口**

<details><summary>ecosystem.config.js文件的逐行注释</summary>
<p>

```
module.exports = { // 导出配置对象供 PM2 使用
    apps: [{ // 定义应用列表（数组结构，可配置多个应用）
        name: "uptime-kuma", // 应用名称（自定义标识，用于 PM2 管理）
        script: "./server/server.js", // 应用入口文件路径（PM2 将执行该文件启动应用）
        env: { // 环境变量配置（会注入到进程环境变量中）
            PORT: "port", // 设置服务端口（注意这里应替换为实际端口号，如 "3000"）
            PLAYWRIGHT_BROWSERS_PATH: "/nonexistent" // 禁用 Playwright 浏览器自动下载
        }
    }]
};
```

</p>
</details> 

## 4.3 创建start.js文件
进入文件管理器后台，在ecosystem.config.js同级目录创建start.js文件
```
const { spawn } = require('child_process');
const path = require('path');

const config = require('./ecosystem.config.js');
const app = config.apps[0];
const env = { ...process.env, ...app.env };
const scriptPath = path.resolve(app.script);
const args = app.args ? app.args.split(' ') : [];
const child = spawn('node', [scriptPath, ...args], { 
    env, 
    stdio: 'inherit',
    cwd: process.cwd()
});

child.on('close', (code) => {
    console.log(`Child process exited with code ${code}`);
});
```

<details><summary>start.js文件内容的逐行注释</summary>
<p>

```
const { spawn } = require('child_process'); // 引入 child_process 模块的 spawn 方法，用于创建子进程
const path = require('path'); // 引入 path 模块处理文件路径

const config = require('./ecosystem.config.js'); // 加载 PM2 配置文件（即前一个示例的配置文件）
const app = config.apps[0]; // 获取配置文件中第一个应用的配置
const env = { ...process.env, ...app.env }; // 合并系统环境变量和应用配置中的环境变量
const scriptPath = path.resolve(app.script); // 将相对路径转换为绝对路径（如 ./server/server.js → /abs/path/server.js）
const args = app.args ? app.args.split(' ') : []; // 如果配置中有 args 参数，将其拆分为数组（未在前例配置中出现）
const child = spawn('node', [scriptPath, ...args], { // 启动子进程执行 node 命令
    env, // 使用合并后的环境变量
    stdio: 'inherit', // 子进程的输入输出继承父进程（控制台显示日志）
    cwd: process.cwd() // 设置工作目录为当前目录
});

child.on('close', (code) => { // 监听子进程退出事件
    console.log(`Child process exited with code ${code}`); // 退出时输出状态码
});
```

</p>
</details> 


## 4.4 创建restart.sh文件
在ecosystem.config.js同级目录创建restart.sh文件
```
#!/bin/sh

# 设置路径
SCRIPT_PATH="/usr/home/Serv00账号名/domains/域名/public_html/uptime-kuma/start.js"
WORK_DIR="/usr/home/Serv00账号名/domains/域名/public_html/uptime-kuma"
LOG_FILE="$WORK_DIR/uptime-kuma.log"
RESTART_LOG="$WORK_DIR/restart_log.txt"

# 检查脚本是否在运行
if ! pgrep -f "node $SCRIPT_PATH" > /dev/null
then
    # 如果脚本没有运行，则重新启动它
    cd "$WORK_DIR"
    nohup node "$SCRIPT_PATH" > "$LOG_FILE" 2>&1 &
    echo "Restarted Uptime Kuma at $(date)" >> "$RESTART_LOG"
fi
```
**注：更改`Serv00账号名`和`域名`为你自己的**

## 4.5 赋权创建的文件
```
chmod +x start.js restart.sh
```
## 4.6 启动uptime-kuma
```
node start.js
```

<details><summary>老方法（放这备个份）</summary>
<p>

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

# 安装 pm2
bash <(curl -s https://raw.githubusercontent.com/k0baya/alist_repl/main/serv00/install-pm2.sh)

# pm2 保活 Uptime-Kuma，PORT 改为自己部署 Uptime-Kuma 的端口
pm2 start server/server.js --name uptime-kuma -- --port=PORT
```
**tip：第一次使用pm2，如果提示”pm2 Command not found“，解决方法：断开 SSH 连接，再重新连接。**

</p>
</details> 

# 5 serv00的续期和自启
## 5.1 续期

- 创建一个自动 SSH 连接自己 serv00 服务器的脚本，每隔一段时间执行一次，可以实现自动续期。

1. 使用以下命令新建一个 auto_renew.sh（自动续期）脚本；
```shell
cat > auto_renew.sh << EOF
#!/bin/bash

sshpass -p '密码' ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -tt 用户名@地址 "exit" &
EOF
``` 

2. 给 auto_renew.sh 添加可执行权限（再次 ls 查看，这个脚本会变红）；
```shell
chmod +x auto_renew.sh
``` 

3.  点击面板左侧的 Cron jobs（定时任务） ，找到 Add cron job （添加任务）；
5. **Specify time** 选择 **Monthly**，**Form type** 选择 **Advanced**，**Command** 写 **auto_renew.sh 脚本文件的绝对路径（可以使用 `pwd` 命令查看）**，如 `/home/用户名/domains/public_html/auto_renew.sh 2>/dev/null 2>&1` ；

    <details><summary>操作图示</summary>
    <p>
    
    
    ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/7c6568f9-f445-4a94-9c53-9edc2dc11484)

    
    </p>
    </details> 

- **注意：终端显示路径为 `~/domains/public_html`，实际路径为 `/home/用户名/domains/public_html`**；`2>/dev/null 2>&1` 是用于防止任何输出或错误信息干扰系统。

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

- 注意：每次**使用 pm2 添加新的保活任务**后，记得**使用以上命令保存 pm2 的任务列表**。

# 6 补充....
## 6.1 保活命令
```shell
# pm2 保活 alist
pm2 start ./alist -- server
``` 

## 6.2 域名邮箱

1. 点击面板左侧的 **E-mail**，找到 **十Add new e-mail**，输入 **E-mail address**（如`admin@serv0704.serv00.net`） 、**Passwords**，点击 **十Add**，如图示；

    <details><summary>操作图示</summary>
    <p>
    
    ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/a19c0390-68ed-4497-bd4d-7329f5fdf661)
    
    </p>
    </details> 

2. 添加邮箱成功后，点击 **Open web client** （打开邮箱网页端），在登陆界面输入邮箱地址和密码，登陆。

    <details><summary>操作图示</summary>
    <p>
    
    ![image](https://github.com/AlanFox240416/wplinote/assets/167155570/1f3b2e61-af2e-47b8-9ea7-b488c3bf4bb1)
    
    </p>
    </details> 