---
title: Antd NoteBook
date created: 2023-08-16
date modified: 2023-08-16
---
## v4中DatePicker的mode属性问题

### 具体问题

- `<DatePicker mode="year" />` 更改年份后，**不会触发** `onChange` 事件
- 因为上一项的原因，`<DatePicker mode="year" />` 在表单中使用时，更改年份后**不会更新表单**内容

### 原因

[文档](https://4x.ant.design/docs/react/faq-cn#%E5%BD%93%E6%88%91%E6%8C%87%E5%AE%9A%E4%BA%86-DatePicker/RangePicker-%E7%9A%84-mode-%E5%B1%9E%E6%80%A7%E5%90%8E%EF%BC%8C%E7%82%B9%E5%87%BB%E5%90%8E%E6%97%A0%E6%B3%95%E9%80%89%E6%8B%A9%E5%B9%B4%E4%BB%BD/%E6%9C%88%E4%BB%BD%EF%BC%9F)中告知：

> `mode` 是在 v3 中为了控制组件展现状态的属性，只改变样式，而不会改变行为
> v4 保留纯粹是考虑到迁移的便利性

同时 [`DatePicker`](https://4x.ant.design/components/date-picker-cn/) 的默认行为是：

> 选择年月日中的**日**后，才会触发 `onChange` 事件

所以在这里，`<DatePicker mode="year" />` 不等于 `<YearPicker />`，`mode="month"` 同理

### 解决

使用 `picker` 属性，例如 `<DatePicker picker="year" />`