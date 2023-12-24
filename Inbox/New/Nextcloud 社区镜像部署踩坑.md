部署时可以选择数据库，我选了使用其他容器中的 mysql，结果在浏览器的起始设置页面报错了：

![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20231224144129.png)

okok，看下容器日志：

```bash
    
Configuring Redis as session handler
Initializing nextcloud 28.0.1.1 ...
New nextcloud instance
Initializing finished
=> Searching for scripts (*.sh) to run, located in the folder: /docker-entrypoint-hooks.d/before-starting
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.24.0.4. Set the 'ServerName' directive globally to suppress this message
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.24.0.4. Set the 'ServerName' directive globally to suppress this message
[Sun Dec 24 06:15:24.215346 2023] [mpm_prefork:notice] [pid 1] AH00163: Apache/2.4.57 (Debian) PHP/8.2.13 configured -- resuming normal operations
[Sun Dec 24 06:15:24.215390 2023] [core:notice] [pid 1] AH00094: Command line: 'apache2 -D FOREGROUND'
192.168.0.115 - - [24/Dec/2023:06:16:12 +0000] "\x16\x03\x01\x02" 400 484 "-" "-"
192.168.0.115 - - [24/Dec/2023:06:16:12 +0000] "\x16\x03\x01\x02" 400 484 "-" "-"
192.168.0.115 - - [24/Dec/2023:06:16:12 +0000] "\x16\x03\x01\x02\x1a\x01" 400 484 "-" "-"
192.168.0.115 - - [24/Dec/2023:06:16:12 +0000] "\x16\x03\x01\x02" 400 484 "-" "-"
192.168.0.115 - - [24/Dec/2023:06:16:12 +0000] "\x16\x03\x01\x02" 400 484 "-" "-"
192.168.0.115 - - [24/Dec/2023:06:16:12 +0000] "\x16\x03\x01\x02" 400 484 "-" "-"
192.168.0.115 - - [24/Dec/2023:06:16:16 +0000] "GET / HTTP/1.1" 200 3401 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0"
192.168.0.115 - - [24/Dec/2023:06:16:21 +0000] "POST /index.php HTTP/1.1" 500 3672 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0"
192.168.0.115 - - [24/Dec/2023:06:17:08 +0000] "-" 408 0 "-" "-"
192.168.0.115 - - [24/Dec/2023:06:23:54 +0000] "POST /index.php HTTP/1.1" 200 4018 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0"
192.168.0.115 - - [24/Dec/2023:06:23:59 +0000] "POST /index.php HTTP/1.1" 200 4016 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0"
192.168.0.115 - - [24/Dec/2023:06:24:11 +0000] "POST /index.php HTTP/1.1" 500 3672 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0"
192.168.0.115 - - [24/Dec/2023:06:24:24 +0000] "POST /index.php HTTP/1.1" 200 4016 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0"
192.168.0.115 - - [24/Dec/2023:06:24:47 +0000] "POST /index.php HTTP/1.1" 500 3672 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36 Edg/120.0.0.0"
192.168.0.115 - - [24/Dec/2023:06:25:39 +0000] "-" 408 0 "-" "-"
```

捏麻麻滴，只有 **Apache 服务**的日志，然后就是漫长的 google，在某个小伙的提问中我发现能在 Docker 挂载的卷目录下找到日志
