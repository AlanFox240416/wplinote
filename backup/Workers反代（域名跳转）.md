## 在[这篇文章](https://linux.do/t/topic/31141)里已详细说明Hugging Face 部署 Uptime-Kuma，在这里记录一下域名转发的坑。
# 1 Workers 代码

1. 需要将 `xxx.hf.space` 替换成你实际要使用的目标主机名，如 `li6911-uptime-kuma.hf.space`；
2. 需要将 `https://xxx.hf.space` 替换成你实际的 origin，如 `https://li6911-uptime-kuma.hf.space`；
3. 需要将 `/status/web` 替换成你实际的状态页面路径，如 `/status/wpli`。

# 2 Workers 路由设置

- 一定不要忘了加 `/*`
    <details><summary>示例如下图</summary>
    <p>
    
    ![image](https://github.com/user-attachments/assets/aaa6d8eb-0d7d-42d5-8725-a0cd032f6424)
    
    </p>
    </details> 

# 3 DNS 解析设置

- **仅 DNS，不开小黄云**