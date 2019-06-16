---
title: 微信 JSSDK 使用 
tags: vue,微信浏览器,微信支付,上传图片
grammar_cjkRuby: true
date: 2019-6-16
---

## 在 App.vue 中注入配置信息

- api/wxJSSDK/index

```javascript
export function getConfig(url) {
  let data = {
    url: url
  }
  return Vue.axios({
    url: wxJSSDKURL + '/customShare',
    method: 'post',
    data
  })
}
```

- App.vue

```html
<script>
import wx from 'weixin-js-sdk'
import { getConfig } from '@/api/wxJSSDK/index'

created () {
    this.getConfig()
  },
methods: {
	getConfig () {
      let url = window.location.href.split('#')[0]
      getConfig(url).then(res => {
        let data = res.data
        wx.config({
          debug: false, // 开启调试模式,调用的所有api的返回值会在客户端console.log出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
          appId: data.appId, // 必填，公众号的唯一标识
          timestamp: data.timestamp, // 必填，生成签名的时间戳
          nonceStr: data.nonceStr, // 必填，生成签名的随机串
          signature: data.signature, // 必填，签名
          jsApiList: [
            'updateAppMessageShareData',
            'updateTimelineShareData',
            'onMenuShareAppMessage',
            'onMenuShareTimeline',
            'chooseWXPay',
            'chooseImage',
            'uploadImage',
            'downloadImage'
          ] // 必填，需要使用的JS接口列表
        });

        // 微信预加载失败回调
        wx.error(function (res) {
          JSON.stringify(res)
        });

        wx.checkJsApi({
          jsApiList: ['updateAppMessageShareData', 'updateTimelineShareData', 'onMenuShareAppMessage', 'onMenuShareTimeline', 'chooseWXPay'], // 需要检测的JS接口列表，所有JS接口列表见附录2,
          success: function (res) {
            // 以键值对的形式返回，可用的api值true，不可用为false
          }
        });

        wx.ready(function () {   //需在用户可能点击分享按钮前就先调用

        })

      })
    },
}

</script>
```

## 封装调用的接口

- utils/wxJSSDK

```javascript
import wx from 'weixin-js-sdk'

//	微信分享
export function wxShare(config) {
  wx.updateAppMessageShareData({
    title: config.title, // 分享标题
    desc: config.desc, // 分享描述
    link: config.link, // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
    imgUrl: config.imgUrl, // 分享图标
    success: function() {
      // 设置成功
    }
  })
  wx.updateTimelineShareData({
    title: config.title, // 分享标题
    link: config.link, // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
    imgUrl: config.imgUrl, // 分享图标
    success: function() {
      // 设置成功
    }
  })
}

//	微信支付
export function chooseWXPay({
  timestamp,
  nonceStr,
  pack,
  signType,
  paySign,
  success,
  fail
}) {
  wx.chooseWXPay({
    timestamp: timestamp, // 支付签名时间戳，注意微信jssdk中的所有使用timestamp字段均为小写。但最新版的支付后台生成签名使用的timeStamp字段名需大写其中的S字符
    nonceStr: nonceStr, // 支付签名随机串，不长于 32 位
    package: pack, // 统一支付接口返回的prepay_id参数值，提交格式如：prepay_id=\*\*\*）
    signType: signType, // 签名方式，默认为'SHA1'，使用新版支付需传入'MD5'
    paySign: paySign, // 支付签名
    success: function(res) {
      // 支付成功后的回调函数
      success(res)
    },
    fail: function(err) {
      fail(err)
    }
  })
}

//	微信登录二维码
export function getAuthor({
  appid = 'wx55910a9ed2407d1b',
  redirect_uri = window.location.href,
  response_type = 'code',
  scope = 'snsapi_base',
  state = 'authorize'
}) {
  let baseUrl = 'https://open.weixin.qq.com/connect/oauth2/authorize',
    url = `${baseUrl}?appid=${appid}&redirect_uri=${encodeURIComponent(
      redirect_uri
    )}&response_type=${response_type}&scope=${scope}&state=${state}#wechat_redirect`
  return url
}

//	微信选择图片或拍照
export function chooseImage(
  {
    count = 9,
    sizeType = ['original', 'compressed'],
    sourceType = ['album', 'camera']
  },
  successCallback
) {
  wx.chooseImage({
    count, // 默认9
    sizeType, // 可以指定是原图还是压缩图，默认二者都有
    sourceType, // 可以指定来源是相册还是相机，默认二者都有
    success: function(res) {
      var localIds = res.localIds // 返回选定照片的本地ID列表，localId可以作为img标签的src属性显示图片
      successCallback(res)
    },
    fail: function(err) {
      alert(JSON.stringify(err))
    }
  })
}

//	获取图片的数据
export function getLocalImgData(localId, successCallback) {
  wx.getLocalImgData({
    localId, // 图片的localID
    success: function(res) {
      var localData = res.localData // localData是图片的base64数据，可以用img标签显示
      successCallback(res)
    },
    fail: function(res) {
      JSON.stringify(res)
    }
  })
}
```

## 业务页面中调用

### 微信上传图片

```html
<template>
<button @click="wxJSSDKUploadImage">微信上传图片</button>
</template>

<script>
import { chooseImage, getLocalImgData } from '@/utils/wxJSSDK'

methods: {
	 // 微信浏览器上传图片
	wxJSSDKUploadImage () {
		//	选择图片
		chooseImage({
		count: 1,
		sizeType: ['original', 'compressed'],
		sourceType: ['album', 'camera']
		}, (res) => {
        // 上传 base64 编码，返回图片 url
        getLocalImgData(res.localIds[0], (r) => {
			let localData = r.localData
			//	安卓拿到的 localData，缺少 base64 编码的前缀： data:image/jpeg;base64, 
			if (!~localData.indexOf('data:image')) {
			localData = 'data:image/jpeg;base64,' + localData
			}
			localData = localData.replace(/\r|\n/g, '').replace('data:image/jgp', 'data:image/jpeg')
			//	将 base64 转为 File 传给后端
			let file = this.base64ToFile(localData),
			formData = new FormData(),
			id = this.token['user_id']
			formData.append('file', file)
			formData.append('id', id)
			this.uploadImg(formData)
        })
      }),
	  
		//	将 base64 编码转为 File
		base64ToFile (base64) {
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
		
		uploadImg (file) {
			//	后端 api ，参数 File ，返回图片 URL
		}
    }
}
</script>
```