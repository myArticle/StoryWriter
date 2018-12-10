---
title: Vue 的使用
tags: Vue,JavaScript,前端框架
grammar_cjkRuby: true
date: 2018-6-6
---


***
## 父组件向子组件传值（单向）

>1. 父组件有一值 `b` ，要传给子组件使用，通过 `props` 传递。
>2. 在父组件中引用子组件标签（如 `<Child></Child>` ），将父组件中的变量 `b` 绑定到一个属性上（如 `:a="b"` ）；
>3. 在子组件中通过 `props` 接收父组件传递的值（如 `props: ["a"]` ），通过 `v-model="a"` 去查看；

- 新建子组件文件 child.vue

	``` html
	<template>
		<section>
			子组件：<input type="text" v-model="a">
		</section>
	</template>

	<script>
	export default {
	  props: ["a"]
	};
	</script>
	```
	
- 在父组件中引用子组件

	``` html
	<template>
		<section>
			<Child :a="b"></Child>
			父组件：<input type="text" v-model="b">
		</section>
	</template>

	<script>
	import Child from "@/components/child";

	export default {
	  components: {
		Child
	  },
	  data() {
		return {
		  b: "some data"
		};
	  }
	};
	</script>
	```
	
	
***
## 子组件向父组件传值（单向）

>1. 子组件有一值 `b` ，要传给父组件使用，通过 `$emit()` 触发事件，传递值； `@` 监听事件，获取值。
>2. 在父组件中引用子组件标签（如 `<Child></Child>` ），在 `methods: {}` 中新建方法（如 `d()` ），将该方法（如 `d()` ）绑定到一个自定义事件上（如 `@a="d"` ）；
>3. 在子组件中通过 `$emit()` 触发事件，传递值（如 `$emit("a", this.b)` ，触发事件 `a` ，并传递值 `b` ）；
>4. 在父组件的方法中通过 `@` 监听事件，获取子组件传递的值（如监听事件 `a` ，触发方法 `d()` ，收到一个参数 `data` ，然后将该值赋予给父组件声明的变量 `c` ，然后去操作变量 `c` ， `v-model="c"` ）；

- 新建子组件文件 child.vue

	``` html
	<!-- 只在初始化的时候赋值
	<template>
		<section>
			子组件：<input type="text" v-model="b">
		</section>
	</template>

	<script>
	export default {
	  data() {
		return {
		  b: "some data"
		};
	  },
	  created() {
		this.$emit("a", this.b);
	  }
	};
	</script> -->
	
	<!-- 在初始化和子组件输入框有输入时赋值 -->
	<template>
		<section>
			子组件：<input type="text" v-model="b" @input="f">
		</section>
	</template>

	<script>
	export default {
	  data() {
		return {
		  b: "some data"
		};
	  },
	  created() {
		this.$emit("a", this.b);
	  },
	  methods: {
		f() {
		  this.$emit("a", this.b);
		}
	  }
	};
	</script>
	```
	
- 在父组件中引用子组件

	``` html
	<template>
		<section>
			<Child  @a="d"></Child>
			父组件：<input type="text" v-model="c">
		</section>
	</template>

	<script>
	import Child from "@/components/child";

	export default {
	  components: {
		Child
	  },
	  data() {
		return {
		  c: ""
		};
	  },
	  methods: {
		d(data) {
		  this.c = data;
		}
	  }
	};
	</script>
	```
	

***
## 数组的某一值改变，不会触发页面的渲染

>改变数组中的某一值，如果通过 `arr[index]` 这种方式，数组的值会改变，但不会触发重新渲染页面。所以，要想实现页面的重新渲染，需要用 `Vue.$set()` ，通过 `this.$set(arr, index, value)` 去改变数组中的值。

``` html
<template>
	<section>
    <ul>
      <li v-for="(v, i) in a" :key="i" @click="changeIndexValue(i)">{{v}}</li>
      <button @click="add">增加列表</button>
    </ul>
	</section>
</template>

<script>
export default {
  data() {
    return {
      a: []
    };
  },
  methods: {
    add() {
      this.a.push(this.a.length);
    },
    changeIndexValue(i) {
      // this.a[i] = "我变了";
      this.$set(this.a, i, "我变了");
      console.log("a==>", this.a);
    }
  },
  created() {
    this.a.push("第一");
  }
};
</script>
```


***
## 添加标题 title

>像详情页这类的标题，一般都会采用其名称作为网页的标题（title），这时候就可以在获取到详情数据后通过给 `document.title` 去赋值来达到设置标题（title）的目的。如果直接 `console.log(document.title)` ，将会得到当前页面的标题（title）值。

```html
let detial_title = '要设置的标题'
document.title = detial_title
```

>如果是固定的标题，那么可以给 `vue-router` 中的每一个路由实例的 `meta` 属性赋值一个包含 `title` 键值对的对象，然后在路由的钩子（beforeEach()）里去循环给每一个拥有 `meta.title` 属性的路由实例通过 `document.title = to.meta.title` 去设置标题（title）。

``` javascript
const routes ={
	{
		path: '/',
		name: 'index',
		component: () => import('@/views/index/index.vue'),
		meta: { title: '首页' }
	},
	{
		path: '/author',
		name: 'author',
		component: () => import('@/views/author/index.vue'),
		meta: { title: '作者专栏' }
	},
	{
		path: '/adv/:id',
        name: 'advDetail',
        component: () => import('@/views/adv/detail/index.vue'),
        meta: { title: '软文详情' }
    }
}
```

``` javascript
import Vue from 'vue'
import Router from 'vue-router'
import routes from './index.config'

Vue.use(Router)

const router = new Router({
	routes
})

// router change setting title
router.beforeEach((to, from, next) => {
    if (to.meta.title) {
        document.title = to.meta.title
    }
    next()
})
```


***
## 检测终端设备

>通过 `window.navigator.userAgent` 属性值，来判断当前浏览器的类型。

```javascript
export const browser = {
    versions: function () {
        let u = navigator.userAgent, app = navigator.appVersion;
        return {     //移动终端浏览器版本信息
            trident: u.indexOf('Trident') > -1, //IE内核
            presto: u.indexOf('Presto') > -1, //opera内核
            webKit: u.indexOf('AppleWebKit') > -1, //苹果、谷歌内核
            gecko: u.indexOf('Gecko') > -1 && u.indexOf('KHTML') == -1, //火狐内核
            mobile: !!u.match(/AppleWebKit.*Mobile.*/), //是否为移动终端
            ios: !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/), //ios终端
            android: u.indexOf('Android') > -1 || u.indexOf('Linux') > -1, //android终端或uc浏览器
            iPhone: u.indexOf('iPhone') > -1, //是否为iPhone或者QQHD浏览器
            iPad: u.indexOf('iPad') > -1, //是否iPad
            webApp: u.indexOf('Safari') == -1, //是否web应该程序，没有头部与底部
            micromessenger: /micromessenger/i.test(u) //是否为微信浏览器
        };
    }(),
    language: (navigator.browserLanguage || navigator.language).toLowerCase()
}
```

>然后在需要判断设备类型的页面去调用。

```html
<script>
import { browser } from '@/utils/terminal'

	export default {
		data() {
			return {
				micromessenger: null
			}
		},
		created() {
			this.micromessenger = browser.versions.micromessenger
		}
	}
</script>
```


***
## 微信分享

>项目：vue-cli3.0 + vue-router + vuex
>vue-router 采用的 hash 模式，即丑陋的 `#` 。
>因为 vue 的单页面是通过改变 `url` `#` 后的部分实现页面的切换，而微信分享做签名时要传入当前页面的 `url` （不包括 `#` 后的部分），所以，只需在 `app.vue` 里做一次签名即可。如果这样做，那么分享出去的页面在打开时始终会是做签名时传入的那个 `url` 地址，要解决这个问题，可以在需要分享的页面去调用 `jssdk` 的 `api` 去手动设置正确的链接参数。

>在 `App.vue` 里的 `methods` 对象中增加方法 `getConfig` ，传入参数 `url` ，返回签名成功的参数，配置 `wx.config()`。如果微信浏览器打开，弹出 `errMsg: ok` ，说明签名成功；其他则根据 `errMsg` 做错误分析。

```javascript
getConfig() {
	let url = window.location.href.split('#')[0]
	getConfig(url).then(res => {
		console.log(res)
		let data = res.data
		wx.config({
			debug: true, // 开启调试模式,调用的所有api的返回值会在客户端console.log出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
			appId: data.appId, // 必填，公众号的唯一标识
			timestamp: data.timestamp, // 必填，生成签名的时间戳
			nonceStr: data.nonceStr, // 必填，生成签名的随机串
			signature: data.signature, // 必填，签名
			jsApiList: [
				'updateAppMessageShareData',
				'updateTimelineShareData',
				'onMenuShareAppMessage',
				'onMenuShareTimeline'
			] // 必填，需要使用的JS接口列表
		});

		// 微信预加载失败回调
		wx.error(function(res) {
		console.log(res)
		alert(res)
		});

		wx.checkJsApi({
			jsApiList: ['updateAppMessageShareData', 'updateTimelineShareData', 'onMenuShareAppMessage', 'onMenuShareTimeline'], // 需要检测的JS接口列表，所有JS接口列表见附录2,
			success: function(res) {
			// 以键值对的形式返回，可用的api值true，不可用为false
			console.log(res)
			alert(res)
			}
		});

	})
}
```

>在需要做分享的页面去手动设置分享参数

```javascript
// 确保先拿到数据后，再去调用 `wxShare` 。
wxShare({
  title: res.data.article.title,
  desc: res.data.article.content,
  link:
	window.location.href.split('#')[0] +
	'?path=/#' +
	window.location.href.split('#')[1],
  imgUrl: res.data.article.cover_path
})
```

> `wxShare.js`

```javascript
import wx from 'weixin-js-sdk'

export function wxShare(config) {
    wx.updateAppMessageShareData({
        title: config.title, // 分享标题
        desc: config.desc, // 分享描述
        link: config.link, // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
        imgUrl: config.imgUrl, // 分享图标
        success: function () {
            // 设置成功
        }
    })
    wx.updateTimelineShareData({
        title: config.title, // 分享标题
        link: config.link, // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
        imgUrl: config.imgUrl, // 分享图标
        success: function () {
            // 设置成功
        }
    })
}
```