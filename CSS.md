---
title: CSS
tags: 新建,模板,小书匠
grammar_cjkRuby: true
date: 2018-7-2
---
Don't Repeat Yourself


## 浏览器前缀

- Firefox: -moz-
- IE: -ms-
- Opera: -o-
- Safari: -webkit-
- Chrome: -webkit-


## 半透明边框

> 默认情况下，属性 `background-clip` 的值为 `border-box` ，意味着背景会延伸到 `盒子模型` 中边框区域的外层。所以，透过 `边框` 是 `背景` （的颜色），要想将 `边框` 设为 `半透明` （不受背景颜色干扰），就需要将背景只延伸到 `盒子模型` 中边框区域的内层，将 `background-clip` 的值设为 `padding-box` 则可实现。

``` css
border: 100px solid rgba(0,0,0,.5);
background-color: #000;
background-clip: padding-box;
```


## 多重边框

1.  `box-shadow` 模拟多重边框

	> `box-shadow` 支持层层叠加，所以，可以通过层层扩大 `阴影` 的 `实阴影` 值，来模拟多层边框。

	``` css
	background-color: #000;
	box-shadow: 0 0 0 50px rgba(0, 0, 0, 0.2) inset, 0 0 0 100px rgba(0, 0, 0, 0.7) inset;
	```
	
2.  `outline` + `border` 模拟双层边框

	> `outline` 和 `border` 支持虚线边框

	``` css
	border: 100px dashed rgba(0, 0, 0, 0.2);
	outline: 100px dashed rgba(0, 0, 0, 0.7);
	```
	
	
## 灵活的背景定位

1.  `background-position`

	> `background-position` 允许在 `关键字` 后指定偏移量

	``` css
	background-position: bottom 20px right 20px;
	```

2.  `calc() 函数`

	> calc() 函数可以用来做加减乘除运算。
	> 用 0 作除数会让 HTML 解析器抛出异常。
	> \+ 和 - 运算符的两边必须始终要有空白符。比如 calc(50% -8px) 会被解析成为一个无效的表达式：一个百分比后跟一个负数长度值。而 calc(8px + -50%) 会被解析成为一个长度后跟一个加号再跟一个负百分比。
	> \* 和 / 运算符不需要空白符，但考虑到统一性，仍然推荐加上空白符。

	``` css
	background-position: calc(100% - 20px) calc(100% - 20px);
	```

3.  `background-origin` 

	> 默认情况下， `background-origin` 的值为 `padding-box` ，也就是说背景图的区域覆盖 `padding` 和 `content` 。所以，边框不会遮挡住背景图片。如果将 `background-origin` 的值设为 `content` ，则背景图距离角边的距离就取决于 `padding` 的值了。

	``` css
	padding-right: 20px;
	padding-bottom: 20px;
	background-position: right bottom;
	background-origin: content-box;
	```
	
