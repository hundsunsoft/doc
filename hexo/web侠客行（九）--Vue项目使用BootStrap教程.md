---
title: web侠客行（九）--Vue项目使用BootStrap教程
date: 2000-04-13 09:02:12
tags: 
    - vue
    - app
    - bootstrap
categories: 
    - 教程
    - web
---

使用模块式开发实现BootStrap样式的导航栏。
# 1.引入bootstrap
* 引入JQUERY
* 引入BootStrap

## html link
```
// quick start 地址
<!-- 最新版本的 Bootstrap 核心 CSS 文件 -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">

<!-- 可选的 Bootstrap 主题文件（一般不用引入） -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap-theme.min.css" integrity="sha384-rHyoN1iRsVXV4nD0JutlnGaslCJuC7uwjduW9SVrLvRYooPp2bWYgmgJQIXwl/Sp" crossorigin="anonymous">

<!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
<script src="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/js/bootstrap.min.js" integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

## 引入JQUERY
安装JQUERY
```
// npm安装JQUERY
npm install jquery --save-dev
```
build/webpack.base.conf.js 文件增加以下内容：
```
const webpack = require('webpack')

// module.exports中增加以下内容：
// 增加一个plugins
plugins: [
  new webpack.ProvidePlugin({
    $: "jquery",
    jQuery: "jquery"
  })
],
```
在main.js中添加内容
```
import $ from 'jquery'
```
引入QUERY后测试，修改HelloWorld.vue
```
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h2>Essential Links</h2>
    <ul>
      <li><a href="https://vuejs.org" target="_blank">Core Docs</a></li>
      <li><a href="https://forum.vuejs.org" target="_blank">Forum</a></li>
      <li><a href="https://chat.vuejs.org" target="_blank">Community Chat</a></li>
      <li><a href="https://twitter.com/vuejs" target="_blank">Twitter</a></li>
      <br>
      <li><a href="http://vuejs-templates.github.io/webpack/" target="_blank">Docs for This Template</a></li>
    </ul>
    <h2>Ecosystem</h2>
    <ul>
      <li><a href="http://router.vuejs.org/" target="_blank">vue-router</a></li>
      <li><a href="http://vuex.vuejs.org/" target="_blank">vuex</a></li>
      <li><a href="http://vue-loader.vuejs.org/" target="_blank">vue-loader</a></li>
      <li><a href="https://github.com/vuejs/awesome-vue" target="_blank">awesome-vue</a></li>
    </ul>
    <div class="btn-group" role="group" aria-label="...">
      <button type="button" class="btn btn-default">Left</button>
      <button type="button" class="btn btn-default">Middle</button>
      <button type="button" class="btn btn-default">Right</button>
    </div>
    <div id="cc">cc</div>
  </div>
</template>
 
<script>
  $(function () {
    alert(123);
  });
  export default {
    name: 'hello',
    data () {
      return {
        msg: 'Welcome to Your Vue.js App'
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
运行 npm run dev ，浏览器有弹出框则证明jquery引入成功了。

## jquery引入错误处理
报错：'$' is not defined
需要做的下一步就是要修改根目录下.eslintrc.js文件了，在改文件的module.exports中，为env添加一个键值对 jquery: true 就可以了，也就是：
```
// .eslintrc.js 文件

env: {
    // 原有
    browser: true,
    // 添加
    jquery: true
}
```

## bootstrap安装
```
// 通过 npm 进行安装(推荐)
npm install bootstrap --save-dev
```
安装成功后，能够在package.json文件夹中看到bootstrap这个模块。这时候需要在main.js中添加如下内容：
```
import 'bootstrap/dist/css/bootstrap.min.css'
import 'bootstrap/dist/js/bootstrap.min'
```
改变HelloWorld.vue样式
```
<div class="btn-group" role="group" aria-label="...">
  <button type="button" class="btn btn-primary">Left</button>
  <button type="button" class="btn btn-primary">Middle</button>
  <button type="button" class="btn btn-primary">Right</button>
</div>
```

## 测试
```
// 启动服务
npm run dev 

// 浏览器测试
localhost:8080
```

# 2. 修改导航栏
样式查询地址：
https://bootswatch.com

修改TopBar
```
<div>
	<nav class="navbar navbar-expand-lg navbar-dark bg-primary">
		<a class="navbar-brand" href="#">Navbar</a>
		<div style="height: 13px;">
		</div>

		<div class="collapse navbar-collapse" id="navbarColor01">
			<ul class="navbar-nav mr-auto">
				<li class="nav-item active">
					<router-link class="nav-link" to="/menu">Home</router-link>
				</li>
				<li class="nav-item">
					<router-link class="nav-link" to="/menu">菜单</router-link>
				</li>
				<li class="nav-item">
					<router-link class="nav-link" to="/user">用户</router-link>
				</li>
				<li class="nav-item">
					<router-link class="nav-link" to="/about">关于</router-link>
				</li>
			</ul>
			<form class="form-inline my-2 my-lg-0">
				<input class="form-control mr-sm-2" type="text" placeholder="Search">
				<button class="btn btn-secondary my-2 my-sm-0" type="submit">Search</button>
			</form>
		</div>
	</nav>
</div>
```
