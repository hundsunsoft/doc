---
title: web侠客行（八）--Vue模块式开发教程
date: 2000-04-13 08:02:12
tags: 
    - vue
    - app
categories: 
    - 教程
    - web
---

使用模块式开发实现简单的导航栏。
# 1.新建目录，创建项目
```
// 安装vue
npm install vue

// 安装vue-cli
npm install vue-cli

新建目录 csdn-proj
vue init webpack csdn // 一路回车下去

// 安装依赖
npm install 

// 安装vue-router 
npm install vue-router 

// 安装vuex
npm install vuex 
```
测试
```
// 启动服务
npm run dev 

// 浏览器测试
localhost:8080
```

# 2. 代码结构
项目结构：
项目代码主要在src目录下
assets目录：静态资源
components目录：组件
router目录：路由
main.js：主文件
App.vue：主组件

## 默认主js main.js
```
import Vue from 'vue'
import App from './App'
import router from './router'

Vue.config.productionTip = false

new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})

```

## 默认主组件 App.vue
```
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

## 默认router router/index.js
```
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    }
  ]
})
```

# 3.实施步骤
## 导航栏组件
导航页面，位于页面最顶端
* 默认加载 export default
* name: 'TopBar'属性表示组件名
* template 要用 div 包裹
* style 要加 scoped 本地使用

components/TopBar.vue
```
<template>
  <div class="hello">
		<h1>这是一个导航页面</h1>
		<h2>{{message}}</h2>
    <ul>
      <li>
        <router-link to="/menu">菜单</router-link>
      </li>
      <li>
        <router-link to="/user">用户</router-link>
      </li>
      <li>
        <router-link to="/about">关于</router-link>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'TopBar',
  data () {
    return {
      message: 'Welcome to TopBar'
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
```

## 菜单组件
components/Menu.vue
```
<template>
	<div>
		<h1>这是一个Menu页面</h1>
		<h2>{{message}}</h2>
	</div>
</template>

<script>
	export default {
		name: 'Menu',
		data() {
			return {
				message: 'Hello Menu'
			}
		}
	}
</script>

<style>
</style>

```

## 用户组件
components/User.vue
```
<template>
	<div>
		<h1>这是一个User页面</h1>
		<h2>{{message}}</h2>
	</div>
</template>

<script>
	export default {
		name: 'User',
		data() {
			return {
				message: 'Hello User'
			}
		}
	}
</script>

<style>
</style>
```

## 关于组件
components/About.vue
```
<template>
	<div>
		<h1>这是一个About页面</h1>
		<h2>{{message}}</h2>
	</div>
</template>

<script>
	export default {
		name: 'About',
		data() {
			return {
				message: 'Hello About'
			}
		}
	}
</script>

<style>
</style>

```

## 路由
* import 组件，@代表编译目录
* 一定要用 Vue.use(Router) 使用Router
router/index.js
```
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
import Menu from '@/components/Menu'
import User from '@/components/User'
import About from '@/components/About'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    },
		{
			path: '/menu',
			name: 'Menu',
			component: Menu
		},
		{
			path: '/user',
			name: 'User',
			component: User
		},
		{
			path: '/about',
			name: 'About',
			component: About
		}
  ]
})
```

## App.vue改写
* 模板增加 TopBar 导航
* import TopBar
* 增加 components: TopBar
```
<template>
  <div id="app">
		<TopBar></TopBar>
    <img src="./assets/logo.png">
    <router-view/>
  </div>
</template>

<script>
import TopBar from '@/components/TopBar'
export default {
  name: 'App',
	components: {
		TopBar
	}
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

# 错误解决
tab缩进错误，这是eslint检查机制问题

## 文件内忽略错误
```
忽略下一行检查
// eslint-disable-next-line

忽略整个文件检查
/* eslint-disable */
```

具体参考 https://eslint.org/docs/user-guide/configuring

## .eslintignore 配置
```
// 增加以下配置，忽略src目录 eslint 检查
/src/
```

## config/index.js 配置
```
// 禁用eslint

module.exports = {
useEslint: false
···

/ * eslint script-indent：“error” * /

/ * eslint script-indent：[“error”，2，{“baseIndent”：1}] * /
```

## eslint配置(推荐)
```
rules: {
    // 忽略indent检查
		'indent': 'off',

    // 忽略no-tabs检查
		'no-tabs': 'off', 

    // 忽略函数前空格检查
    'space-before-function-paren': 'off',
```
使用eslint --init配置
