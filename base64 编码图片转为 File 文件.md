---
title: base64 编码图片转为 File 文件
tags: 图片,base64,File
grammar_cjkRuby: true
date: 2018-12-29
---


```javascript
function base64ToFile(base64) {
  let arr = base64.split(',')
  let d = window.atob(arr[1])
  let mime = arr[0].match(/:(.*?);/)[1]
  let ia = new Uint8Array(d.length)
  for (var i = 0; i < d.length; i++) {
	ia[i] = d.charCodeAt(i)
  }
  let blob = new Blob([ia], { type: mime })
  let files = new window.File([blob], Date.now() + mime.split('/')[1], {
	type: mime
  })
  return files
},
```