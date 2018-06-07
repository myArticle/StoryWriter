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