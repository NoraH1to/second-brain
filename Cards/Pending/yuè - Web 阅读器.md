---
title: yuè - Web 阅读器
date created: 06/01-2023, 15:38, 星期四
date modified: 06/09-2023, 15:45, 星期五
---

## 前言

年初的时候入手了 mini6 用于影音娱乐，在找阅读器的时候， AppStore 里的软件让我眼前一黑

**“为什么云功能全是基于 iCloud 的啊？”**

然后就有了这个项目

经历了一周的构思和两周的开发，我在 Github 提交了最初的版本，第一个版本的[快照](https://yue-7ifx3kij1-norah1to.vercel.app)如下：

![看起来还行](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230601155432.png)

中间踩了非常多的坑，主要是 [[epub.js]] 和 [[safari 移动端适配]]
## 介绍

经过将近一个月的迭代，版本号也来到了 `v1.4.0`，这是它现在的样子：

![好像没多大变化](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230601160459.png)

线上地址：[yue.norah1to.com](https://yue.norah1to.com)

仓库地址：[NoraH1to/yue](https://github.com/NoraH1to/yue)

### 导入

支持本地导入和从 webDAV 导入，webDAV 需要配置自己的服务：

![配置自己的服务](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230601164325.png)

### 格式

暂时只支持 **_epub_**、**_pdf_**

哪天我有看漫画的需求了，会支持 **_zip_** 格式

### 反向代理

Web 应用最大的问题就是使用第三方服务时的**跨域问题**，不巧的是，webDAV 服务的标准实现是存在跨域限制的

假设你要使用坚果云的 webDAV 服务，它的地址为 `https://dav.jianguoyun.com/dav`，你使用该地址进行配置，是行不通的：

![oops](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230601165043.png)

为了解决该问题，我通过 vercel 的[路由功能](https://vercel.com/docs/concepts/projects/project-configuration#rewrites)实现了**反向代理**：

```json
// vercel.json
{
  "rewrites": [
    { "source": "/proxy/:match/:url*", "destination": "https://:match/:url*" },
    {
      "source": "/proxy/:match/:url*/",
      "destination": "https://:match/:url*/"
    },
    {
      "source": "/((?!proxy/.*).*)",
      "destination": "/index.html"
    }
  ],
  "trailingSlash": false
}
```

也就是说，如果你要反代 `https://{服务地址}`，只需要将地址改为 `https://{项目地址}/proxy/{服务地址}` 即可

我们项目的地址为 `yue.norah1to.com`，如果服务地址为坚果云 `dav.jianguoyun.com/dav`，此时我们填入 `https://yue.norah1to.com/proxy/dav.jianguoyun.com/dav` 即可：

![完美](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230601165448.png)

#### 注意！

项目中使用了 [`webdav-client`](https://github.com/perry-mitchell/webdav-client) 这个库来构建 webDAV 客户端，如果我们使用反向代理修改了服务请求地址，会导致客户端解析 webDAV 服务文件夹路径出错，详情可以看我提出的 [issue](https://github.com/perry-mitchell/webdav-client/issues/342)

我在 issue 中建议增加让用户自定义 `basePath`（根路径）的选项，并提交了一个 [pr](https://github.com/perry-mitchell/webdav-client/pull/343)，该 pr 已经并入且在 `v5.1.0` 中发布

所以如果你使用了反向代理，记得配置其根路径

例如上面坚果云的反代地址 `https://yue.norah1to.com/proxy/dav.jianguoyun.com/dav`

它的真实服务地址为 `dav.jianguoyun.com/dav`，所以它的根路径为 `/dav`：

![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230601202358.png)

### 文件管理

有两种方式来给图书分类

#### 标签

第一种是标签的形式，你可以在侧边栏中添加标签，例如我这样的：

![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/C1F9CAA2BCF8DD1FBB9FB1660E255FB5.png)

可以通过长按图书的方式呼出菜单，通过菜单中的选项编辑标签：

![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/66BD01EF1D24B039138A2E5BBDA5A6FF.png)

也可以多选批量编辑标签：

![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/DA4265821A2CC88082E0D68A771638D0.png)

#### webDAV 目录

第二种就是 webDAV 中的目录结构了，需要设置自己的 webDAV 服务才能使用：

![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230601170730.png)

### 阅读器

阅读器算是最核心的功能了，在这里先给 和 [`pdf.js`](https://github.com/mozilla/pdf.js) 磕个头，没有这两个健壮的项目就没有 `yuè`

阅读器大致长这样：

![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230601171104.png)

很简单，但是够用

上面是章节信息和时间，下面则是页码

说到页码，因为实现原因，`epub` 展示的是当前章节页码，`pdf` 则是总页码

#### 翻页

点击屏幕两侧翻页

又或者左右滑动翻页（非常遗憾没有动画，你就说能不能用吧）

同时也支持键盘和鼠标滚轮翻页，上下左右 pageUp,pageDown 等等

#### 工具栏

点击屏幕中心可以呼出工具栏（键盘敲空格也行）：

![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230601171953.png)

上面显示标题，并提供了返回的操作按钮

下面第一行是导航栏，可以翻页、跳转到指定进度

第二行从左到右，依次是

- 目录：

  ![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230601172148.png)

- 切换深色模式

- 切换全屏

- 设置：

  ![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230601172238.png)
