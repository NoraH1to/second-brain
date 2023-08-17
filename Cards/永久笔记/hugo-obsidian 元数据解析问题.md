---
title: hugo-obsidian 元数据解析问题
date created: 2023-06-12
date modified: 2023-06-12
---

```go
// 规定数据格式
type Front struct {
	Title string   `yaml:"title"`
	Draft bool     `yaml:"draft"`
	Tags  []string `yaml:"tags"` // 这里定义 tag 为字符串数组
}
// 解析 formatter
raw_body, err := frontmatter.Parse(strings.NewReader(text), &matter, formats...)
/*
 * 如果报错了，就使用默认的内容
 * 也就是说我们给的数据结构不对它就会走到这
 * 这里面没有处理具体的错误，只要有问题就全部回滚
 */
if err != nil {
	// 使用默认的内容
	matter = Front{
		Title: "Untitled Page",
		Draft: false,
		Tags:  []string{},
	}
}
```