---
title: IndexedDB 读写地狱
date created: 2023-07-03
date modified: 2023-07-05
---

## 发现问题

在迭代[[🤖setup-cli]]漫画阅读能力时，我发现在翻页的时候，硬盘的读写直接被拉满了

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

数据库中图书表的结构为：

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
