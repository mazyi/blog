---
title: 使用Cloudflare转发任意内网端口
date: 2024-5-20 12:00:00
categories: 
  - 技术
tags: 
  - cloudflare
---

1. 在Cloudflare Zero Trust -> Network -> Tunnels里创建一个tunnels

2. 在内网的机器上下载并使用cloudflared转发

    ```
    docker run --name=cloudflared -d cloudflare/cloudflared:latest tunnel --no-autoupdate run --token {your token} --name cloudflared
    ```

3. 在Tunnels -> Configure -> Public Hostname中新增需要转发的内网地址和公共域名

4. HTTP和HTTPS通常可以直接转发成功，TCP等其他协议需要后续步骤

5. 在需要访问内网服务的客户机器下载并使用cloudflared连接tcp转发端口

    ```
    cloudflared.exe access tcp --hostname {your Tcp public hostname} --listener {local ip}:{local port}
    ```
    
6. 通过上一步设置的本地机器的端口访问内网TCP服务

### 参考文档
> [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/)
