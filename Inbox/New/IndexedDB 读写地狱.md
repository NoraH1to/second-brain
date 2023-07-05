---
title: IndexedDB 读写地狱
date created: 2023-07-03
date modified: 2023-07-05
---

## 发现问题

在迭代[[🤖yuè - Web 阅读器]]漫画阅读能力时，我发现在翻页的时候，硬盘的读写直接被拉满了

我很自然的想到是[[indexedDB]]的问题，在翻页的时候阅读器会记录阅读进度写入其中，代码片段如下：

```typescript
const updateProcess = useCallback(
  async () => {
    // ...
    await fs.updateBook({
      hash,
      info: delFalsy({
        readProgress,
      }),
    });
    // ...
  },
  [book],
);
```

我尝试注释掉了该段代码然后翻页，硬盘非常安静，问题定位成功

## 出现原因

我在数据库中构建了一张表，用来存储图书和其阅读进度，表结构如下：

```typescript
type BookTable = Table<{
  hash: string;
  file: File;
  cover: Blob;
  readProgress: {
    value: string;
    percent: number;
  }
}>
```

在上面代码中，我仅仅只更新了 `readProgress` 却产生了大量的 I/O，于是我推测它在更新 `readProgress` 的同时，也重新写入了其他字段，例如 `file` 和 `cover` ，它两的开销会很大

一开始我以为是[[Dexie.js]]的问题，提了一个[issue](https://github.com/dexie/Dexie.js/issues/1758)，维护者也回答了我的问题，告诉我这其实取决于浏览器的实现：

> "That is down to the implementation of IndexedDB in the browser."

经过测试，我发现 `safari (VMware macOS13)`, `firefox (Windows11)`, `edge (Windows11)` 都是这样干的

## 解决方法

没办法，只能通过抽离 `Blob` 对象到一个新表中来解决它，维护者也是这样建议的：

> "A doable workaround would be to store the blobs in a different table and refer to the id of the stored content (like a foreign key)"

更新后数据库中 `books` 表不再存储文件内容：

![books](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230705140543.png)

文件内容和其它尺寸较大的对象都放到了 `bookContents` 里面：

![bookContents](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230705140824.png)

> [!NOTE] 还有点问题
> 
> 这里其实应将 `cover` 单独拆分出来，加载图书列表时没必要也同时加载图书的内容，只需要封面即可
> 
> **[[2023-07-05]]更新**：
> 
> 已在[v1.6.1](https://github.com/NoraH1to/yue/releases/tag/v1.6.1)中改进
