## 初始化失败

部署时可以选择数据库，我选了使用其他容器中的 mysql，结果在浏览器的起始设置页面报错了：

![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20231224144129.png)

okok，看下容器日志：

```log
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

捏麻麻滴，只有 **Apache 服务**的日志，然后就是漫长的 google

在某个小伙的提问中我发现能在 Docker 挂载的卷目录下找到日志：

![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20231224144403.png)

哎呀老高兴了，进去一看，我测，怎么没有：

```log
root@nextcloud:/var/www/html# ls
3rdparty       config       dist                      ocs                resources
AUTHORS        console.php  index.html                ocs-provider       robots.txt
COPYING        core         index.php                 package-lock.json  status.php
apps           cron.php     lib                       package.json       themes
composer.json  custom_apps  nextcloud-init-sync.lock  public.php         version.php
composer.lock  data         occ                       remote.php
```

根据我常年踩坑踩出来的直觉，我觉得应该是放到了一个子目录下（我这的 nextcloud 是最新的 28 版本，才出来没几天），于是看了一眼 `data`：

```log
root@nextcloud:/var/www/html# cd data
root@nextcloud:/var/www/html/data# ls
index.html  nextcloud.log
```

再看下日志，找到最新的错误：

```
"An exception occurred while executing a query: SQLSTATE[42000]: Syntax error or access violation: 1142 ALTER command denied to user 'nextcloud'@'172.24.0.4' for table 'oc_properties'"
```

好家伙，给数据库开的新账户权限不足，干脆不一个个给了，全部拉满：

```sql
GRANT ALL PRIVILEGES ON nextcloud.* TO nextcloud;
```
