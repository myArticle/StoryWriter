---
title: vue —— qrcode 将链接生成二维码
tags: 二维码,vue,qrcode
grammar_cjkRuby: true
date: 2018-6-26
---


## 安装

	``` dos
	npm install --save qrcode
	```
	
	
## vue 引入

``` html?linenums
<template>
	<img :src="qrcode" alt="">
</template>

<script>
	import QRCode from "qrcode";

	export default {
		data() {
			return {
				baseUrl: "https://www.baidu.com",
				qrcode: ""
			};
		},
		mounted() {
			QRCode.toDataURL(this.baseUrl, (error, url) => {
				if (error) {
					console.log("error ==>", error);
				} else {
					this.qrcode = url;
					console.log("qrcode success");
				}
			});
		}
	};
</script>
```


## nodejs 引入

``` javascript
let QRCode = require("qrcode"),
	fs = require("fs"),
	savePath = "cid.png",
	codeURL = "https://www.github.com";

QRCode.toDataURL(codeURL, (err, base64) => {
    base64 = base64.split(",")[1];
	fs.writeFile(savePath, base64, "base64", (error) => {
		
	})
})
```