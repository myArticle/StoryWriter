---
title: html 导出 PDF
tags: 新建,模板,小书匠
grammar_cjkRuby: true
date: 2019-5-7
---

## 思路

|方法|优点|缺点|
|---|---|---|
|jsPDF.js 前端插件|前端搞定，不需要后端配合|图片跨域问题；生成的 PDF 分页会将内容切割|
|wkhtmltopdf 命令行工具|支持 url 链接生成 PDF；生成的 PDF 会根据内容分页，不会切割内容|需要后端配合|
|puppeteer node.js 包|功能丰富|需要下载 Chromium 等工具配合使用|
