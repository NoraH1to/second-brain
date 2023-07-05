---
title: IndexedDB 读写地狱
date created: 2023-07-03
date modified: 2023-07-05
---

在迭代[[🤖setup-cli]]漫画阅读能力时，我发现在翻页的时候，硬盘的读写直接被拉满了

我很自然的想到是[[indexedDB]]的问题，在翻页的时候阅读器会记录阅读进度写入其中，代码片段如下：

```typescript
const updateProcess = useCallback(
  async (curProcess?: ABook['lastProcess'] | null) => {
    // ...
    await fs.updateBook({
      hash: book.hash,
      info: delFalsy({
        lastProcess: bookProcess,
      }),
    });
    // ...
  },
  [book],
);
```
