---
title: 常用 css 样式
tags: css,前端,样式
grammar_cjkRuby: true
date: 2018-12-13
---


## 文本超出部分隐藏 ...

```css
display -webkit-box
-webkit-box-orient vertical
-webkit-line-clamp 2		// 最大行数
overflow hidden
word-break break-all		// 处理英文不换行问题
```

## 滚动条样式

```css
::-webkit-scrollbar {
  width: 5px;
  height: 1px;
}
::-webkit-scrollbar-thumb {
  border-radius: 5px;
  box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.3);
  background: rgba(0, 0, 0, 0.3);
}
```
