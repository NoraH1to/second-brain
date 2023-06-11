## Windows

访问[RubyInstaller for Windows - 国内镜像](https://rubyinstaller.cn/)

### 直接安装

直接安装无需多说，无脑点点就行

### 多版本

#### rbenv

需要安装[rbenv for Windows](https://rubyinstaller.cn/rbenv-for-windows)

打开[[Powershell]]，设置当前终端的安装位置环境变量 `RBENV_ROOT`

```powershell
$env:RBENV_ROOT = "C:\Ruby-on-windows"
```

下载安装脚本并执行

```powershell
# 下载安装脚本
$s = (iwr -useb "https://gitee.com/ccmywish/rbenv-for-windows/raw/main/tools/install.ps1")
# 执行
icm -sc ([scriptblock]::Create($s.Content)) -arg "install", "cn"
```

在 `$PROFILE` 中配置 `rbenv`

```powershell
# rbenv 所在位置
$env:RBENV_ROOT = "C:\Ruby-on-Windows"
# 国内用户使用内置镜像
$env:RBENV_USE_MIRROR = "CN"
# 初始化 rbenv 环境
& "$env:RBENV_ROOT\rbenv\bin\rbenv.ps1" init
```

测试 `rbenv` 命令

```powershell

```