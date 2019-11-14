---
title: iphoneX 底部黑条遮挡适配
tags: web适配,iphoneX,可视区域
renderNumberedHeading: true
grammar_cjkRuby: true
---

## 问题

>iphoneX 底部的黑条会遮挡 web 页面的底部内容，如下图

<img src="./images/1573724282561.png" title="问题" width="375" />
<img src="./images/3131573724214_.pic.jpg" title="解决后" width="375" />


## 解决

 1. 设置 html -> head -> meta 的 content 属性， `viewport-fit=cover`


 ```html
 <meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,minimum-scale=1.0,user-scalable=no,viewport-fit=cover"  />
```

 2. 给被遮挡的元素设置 padding-bottom 属性， `padding-bottom: constant(safe-area-inset-bottom);padding-bottom: env(safe-area-inset-bottom);`

```css
.tabbar {
	display: flex;
	justify-content: center;
    position: fixed;
    bottom: 0;
    left: 0;
  	width: 100%;
  	box-shadow: 0px -4px 12px 0px rgba(0, 0, 0, 0.04);
  	background: #fff;
    z-index: 99999;
  	padding-bottom: constant(safe-area-inset-bottom);
  	padding-bottom: env(safe-area-inset-bottom);
  }
```


## 参考

 1. [凹凸实验室](https://aotu.io/notes/2017/11/27/iphonex/index.html)