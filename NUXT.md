---
title:  NUXT 的使用
url: https://zh.nuxtjs.org
tags: 前端,框架
grammar_cjkRuby: true
time: 2018-5-17
author: jialei
---


# 安装
***

- 依赖 vue-cli 脚手架
	
	``` dos?linenums
	npm install -g vue-cli
	```

- 安装 starter 模板

	``` dos?linenums
	vue init nuxt-community/starter-template <project-name>
	```

- 安装依赖包

	``` dos?linenums
	cd <project-name>
	npm install
	```
	
- <b>注意， node 版本需 >= v8.3.0</b>
- <b>我用的 node 版本 v9.0.0 ， npm 版本 6.0.1</b>


# 运行
***

- 开发模式
	
	``` dos?linenums
	npm run dev
	```
	
- 服务端渲染应用部署
	
	服务端渲染的应用不能直接使用 nuxt 命令，需要先进行编译构建，然后再启动 Nuxt 服务
	
	``` dos?linenums
	npm run build (nuxt build)
	npm run start (nuxt start)
	```
	推荐的 package.json 配置
	
	``` json
		"scripts": {
			"dev": "nuxt",
			"build": "nuxt build",
			"start": "nuxt start",
			"generate": "nuxt generate",
			"lint": "eslint --ext .js,.vue --ignore-path .gitignore .",
			"precommit": "npm run lint"
		  }
	  ```
	  
- 静态应用部署，利用下面命令生成一个静态目录 dist ，所有的静态化后的资源文件均在其中

	``` dos?linenums
	npm run generate
	```


# 插件 [element-ui](http://element-cn.eleme.io/#/zh-CN/component/quickstart)
***

- npm 安装 element-ui

	``` dos?linenums
	npm i element-ui -S
	```

- 在 plugins 文件夹下新建 element-ui.js

	``` javascript
	import Vue from 'vue'
	import Element from 'element-ui'

	export default () => {
		Vue.use(Element)
	}
	```

- 修改 nuxt.config.js 文件，添加 css 和 plugins 键值对

	``` javascript
	module.exports = {
	 
	  css: [
		'element-ui/lib/theme-chalk/index.css'
	  ],

	  plugins: [
		'@/plugins/element-ui'
	  ]
	}
	```

- 测试，在 pages 文件夹下新建 index.vue ，写入以下代码

	```javascript
	<template>
		<div>
			<el-row>
				<el-button>默认按钮</el-button>
				<el-button type="primary">主要按钮</el-button>
				<el-button type="success">成功按钮</el-button>
				<el-button type="info">信息按钮</el-button>
				<el-button type="warning">警告按钮</el-button>
				<el-button type="danger">危险按钮</el-button>
			</el-row>
		</div>
	</template>
	```
	
	
	# 插件 [vant](https://www.youzanyun.com/zanui)
***

- npm 安装 vant

	``` dos?linenums
	npm i vant -S
	```

- 在 plugins 文件夹下新建 vant.js

	``` javascript
	import Vue from 'vue'
	import Vant from 'vant'

	export default () => {
		Vue.use(Vant)
	}
	```

- 修改 nuxt.config.js 文件，添加 css 和 plugins 键值对

	``` javascript
	module.exports = {
	 
	  css: [
		'vant/lib/vant-css/index.css'
	  ],

	  plugins: [
		'@/plugins/vant'
	  ]
	}
	```

- 测试，在 pages 文件夹下新建 index.vue ，写入以下代码

	```javascript
	<template>
		<section class="container">
		hello
		<van-circle
		  v-model="currentRate"
		  :rate="30"
		  :speed="100"
		  :text="text"
		/>
	  </section>
	</template>

	<script>
	export default {
	  data() {
		return {
		  currentRate: 0
		};
	  },
	  computed: {
		text() {
		  return this.currentRate.toFixed(0) + '%'
		}
	  }
	}
	</script>
	```
	
	
# 引入 [axios](https://www.npmjs.com/package/axios) 包
***

- npm 安装 axios

	``` dos?linenums
	npm install --save axios
	```

- 在页面中使用，使用之前需要通过 import 引入 axios 包

	``` html
	<template>
		<div>
				<h1>{{msg}}</h1>
		</div>
	</template>

	<script>
	import axios from 'axios'

	export default {
		async asyncData() {
			let data = await axios.get('http://localhost:3001/form')
			console.log(data.data)
			let msg = data.data.msg
			return {
				msg
			}
		}
	}
	</script>
	```

# 插件 [vue-logger](https://www.npmjs.com/package/vue-logger)
***

前端日志

- npm 安装 vue-logger

	``` dos?linenums
	npm install vue-logger --save
	```

- 在 plugins 文件夹下新建 vue-logger.js ，写入以下代码

	``` javascript
	import Vue from 'vue'
	import VueLogger from 'vue-logger'

	export default () => {
		Vue.use(VueLogger, {
			prefix: new Date(),
			dev: true,
			shortname: true,
			levels: ['log', 'warn', 'debug', 'error', 'dir', 'info']
		})
	}
	```
	
- vue-logger options 描述

	 Name | Type | Default | Desc
	-- | - | - | -
	 prefix | string | none | 是否输出前缀标识    一般为日期时间
	 dev | boolean | true | 是否为开发模式      日志开关，可选择在生产环境中关闭
	 shortname | boolean | true | 是否使用 levels 中的别名    使用：Vue.$error('msg') 不使用：Vue.$console.error('msg')
	 levels | array | ['log', 'warn', 'debug', 'error', 'dir'] | 日志等级 | 可额外添加
	
- 修改 nuxt.config.js 文件，在 plugins 添加一项 `'@/plugins/vue-logger'`

	``` javascript
	module.exports = {
	 
	  plugins: [
		'@/plugins/element-ui',
		'@/plugins/vue-logger'
	  ]
	}
	```

- 测试，在 pages 文件夹下新建的 index.vue 添加 `data()` 方法

	```javascript
	<script>
	export default {
		data () {
			this.$info('==info==', 'vue-logger')  // this => Vue
			//  this.$console.log('==info==', 'set shortname = false')
		}
	}
	</script>
	```
	
	
	# npm run generate 打包指定根目录
	***
	
	- 修改 nuxt.config.js 文件，在 build 中加入 `publicPath: '<根目录>'` 键值对