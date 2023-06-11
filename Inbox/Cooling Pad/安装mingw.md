
首先打开[MinGW-w64仓库](https://sourceforge.net/projects/mingw-w64/files/)，找到下面最新版本的包（2023-06-11 最新版为 8.1.0）
![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230611211407.png)

## 版本选择

文件名的组成结构为 `{系统架构}-{系统接口协议}-{异常处理模型}`

### 系统架构

- 64 位系统使用 **x86_64**
- 32 位系统使用 **i686**

### 系统接口协议

- 开发在 [[Windows]] 下使用的程序选择 **win32**
- 开发例如 Linux、macOS 或其它类 Unix 系统时选择 **posix**

异常处理模型

- sjij 仅支持 **32 位系统**，稳定性qi