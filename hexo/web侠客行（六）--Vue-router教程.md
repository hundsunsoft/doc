---
title: web侠客行（六）--Vue-router教程
date: 2000-04-13 06:02:12
tags: 
    - vue
    - app
    - vue-router
categories: 
    - 教程
    - web
---

# Vue-router quick start
## 1.引入js
```
<script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
<script src="js/vue-router.js" type="text/javascript" charset="utf-8"></script>
```
## 2.定义路由组件
```
const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }
```
## 3.创建路由 router
```
const routes = [
			  { path: '/foo', component: Foo },
			  { path: '/bar', component: Bar }
			]
```
## 4.创建 router 实例，然后传 `routes` 配置
其中，routes: routes 可以缩写成 routes
```
const router = new VueRouter({
  routes: routes
})
```
## 5.创建和挂载根实例
```
const app = new Vue({
			  router: router,
			  el: '#app'
			})
```
## 6.html超链接 router-link
router-link 代替 a-href ，用 to 属性指定 path
```
<router-link to="/foo">Go to Foo</router-link>
<router-link to="/bar">Go to Bar</router-link>
```
## 7.路由组件渲染 router-view
router-view 相当与 路由模板
```
<router-view></router-view>
```

## 8.代码示例
```
<div id="app">
  <h1>Hello App!</h1>
  <p>
    <!-- 使用 router-link 组件来导航. -->
    <!-- 通过传入 `to` 属性指定链接. -->
    <!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
    <router-link to="/foo">Go to Foo</router-link>
    <router-link to="/bar">Go to Bar</router-link>
  </p>
  <!-- 路由出口 -->
  <!-- 路由匹配到的组件将渲染在这里 -->
  <router-view></router-view>
</div>

<script type="text/javascript">
  // 0. 如果使用模块化机制编程， 要调用 Vue.use(VueRouter)
    
  // 1. 定义（路由）组件。
  // 可以从其他文件 import 进来
  const Foo = { template: '<div>foo</div>' }
  const Bar = { template: '<div>bar</div>' }
  
  // 2. 定义路由
  // 每个路由应该映射一个组件。 其中"component" 可以是
  // 通过 Vue.extend() 创建的组件构造器，
  // 或者，只是一个组件配置对象。
  // 我们晚点再讨论嵌套路由。
  const routes = [
    { path: '/foo', component: Foo },
    { path: '/bar', component: Bar }
  ]
    
  // 3. 创建 router 实例，然后传 `routes` 配置
  // 你还可以传别的配置参数, 不过先这么简单着吧。
  const router = new VueRouter({
    routes: routes // （缩写）routes
  })
    
  // 4. 创建和挂载根实例。
  // 记得要通过 router 配置参数注入路由，
  // 从而让整个应用都有路由功能
  const app = new Vue({
    router: router,
    el: '#app'
  })
    
</script>
```

# vue-router 安装
## 在 Vue 后面加载 vue-router
```
<script src="/path/to/vue.js"></script>
<script src="/path/to/vue-router.js"></script>
```
## NPM install
```
npm install vue-router 
```

## 模块化工程中使用 import 和 Vue.use
如果在一个模块化工程中使用它，必须要通过 Vue.use() 明确地安装路由功能： 
```
import Vue from 'vue'
import VueRouter from 'vue-router'
 
Vue.use(VueRouter)
```
如果使用全局的 script 标签，则无须如此（手动安装）。 

## 构建开发版 
从 GitHub 上直接 clone，然后自己 build 一个 vue-router。 
```
git clone https://github.com/vuejs/vue-router.git node_modules/vue-router
cd node_modules/vue-router
npm install
npm run build
```

# vue-router 动态路由匹配
## 动态路由路径
path路径带冒号 :
我们经常需要把某种模式匹配到的所有路由，全都映射到同个组件。例如，我们有一个 User 组件，对于所有 ID 各不相同的用户，都要使用这个组件来渲染。那么，我们可以在 vue-router 的路由路径中使用『动态路径参数』（dynamic segment）来达到这个效果： 
```
const User = {
  template: '<div>User</div>'
}
 
const router = new VueRouter({
  routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]
})
```
这样 /user/foo 和 /user/bar 都将映射到相同的路由。 

## 动态路由参数
每个组件都会生成一个路由对象 $route，参数获取：this.$route.params.[参数名]
一个『路径参数』使用冒号 : 标记。当匹配到一个路由时，参数值会被设置到 this.$route.params，可以在每个组件内使用。于是，我们可以更新 User 的模板，输出当前用户的 ID： 
```
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
```
可以支持多段参数映射，例如：
```
/user/:username  /user/evan  { username: 'evan' }  
/user/:username/post/:post_id  /user/evan/post/123  { username: 'evan', post_id: 123 }  
```
除了 $route.params 外，$route 对象还提供了其它有用的信息，例如，$route.query（如果 URL 中有查询参数）、$route.hash 等等。

## 响应路由参数的变化 
当使用路由参数时，例如从 /user/foo 导航到 user/bar，原来的组件实例会被复用。因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。不过，这也意味着组件的生命周期钩子不会再被调用。 

复用组件时，想对路由参数的变化作出响应的话，你可以简单地 watch（监测变化） $route 对象： 
```
const User = {
  template: '...',
  watch: {
    '$route' (to, from) {
      // 对路由变化作出响应...
    }
  }
}
```
vue-router 使用 path-to-regexp 作为路径匹配引擎，所以支持很多高级的匹配模式，例如：可选的动态路径参数、匹配零个或多个、一个或多个，甚至是自定义正则匹配。
同一个路径可以匹配多个路由，此时，匹配的优先级就按照路由的定义顺序：谁先定义的，谁的优先级就最高。 

# vue-router 嵌套路由
简单的嵌套路由
```
<div id="app">
  <router-view></router-view>
</div>
 
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
 
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User }
  ]
})
```
这里的 <router-view> 是最顶层的出口，渲染最高级路由匹配到的组件。
同样地，一个被渲染组件同样可以包含自己的嵌套 <router-view>。
例如，在 User 组件的模板添加一个 <router-view>： 
```
const User = {
  template: `
    <div class="user">
      <h2>User {{ $route.params.id }}</h2>
      <router-view></router-view>
    </div>
  ` } 
```

要在嵌套的出口中渲染组件，需要在 VueRouter 的参数中使用 children 配置： 
```
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User,
      children: [
        {
          // 当 /user/:id/profile 匹配成功，
          // UserProfile 会被渲染在 User 的 <router-view> 中
          path: 'profile',
          component: UserProfile
        },
        {
          // 当 /user/:id/posts 匹配成功
          // UserPosts 会被渲染在 User 的 <router-view> 中
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
```
不用children也可以跳转，例如：{path:'/user/:id/profile'} 但是不能渲染模板内的 router-view
也可以提供空的子路由，额外的渲染一些组件：
```
const router = new VueRouter({
  routes: [
    {
      path: '/user/:id', component: User,
      children: [
        // 当 /user/:id 匹配成功，
        // UserHome 会被渲染在 User 的 <router-view> 中
        { path: '', component: UserHome },
 
        // ...其他子路由
      ]
    }
  ]
})
```
children示例：
```
<div id="app">
  <h1>Hello App!</h1>
  <p>
    <router-link to="/foo/usr">Go to Foo</router-link><br />
    <router-link to="/foo/usr1">Go to Foo1</router-link><br />
    <router-link to="/foo/usr2">Go to Foo2</router-link><br />
    <router-link to="/foo/usr3">Go to Foo3</router-link><br />
    <router-link to="/bar">Go to Bar</router-link><br />
    <router-link to="/bar/name">Go to Bar1</router-link><br />
    <router-link to="/bar/name/a">Go to Bar2</router-link><br />
    <router-link to="/bar/name/user">Go to Bar3</router-link><br />
  </p>
  <router-view></router-view>
</div>

<script type="text/javascript">

  const Foo = { template: '<div><h1>foo {{ this.$route.params.user }}</h1></div>' }
  const Bar = { template: '<div><h1>User {{ this.$route.params.routeid }}</h1><router-view></router-view></div>' }
  const User = { template: '<div><h1>this is a user template</h1></div>' }
  
  const routes = [
    { path: '/foo/:user', component: Foo },
    // /bar/name/user 映射的组件 User 会渲染在组件 Bar 的 router-view 中
    { path: '/bar/:routeid', component: Bar,
      children: [
        { path: 'user', component: User},
      ]
    },
  ]
    
  const router = new VueRouter({
    routes: routes 
  })
    
  const app = new Vue({
    router: router,
    el: '#app',
  })
    
</script>
```

# vue-router 编程式的导航
除了使用 <router-link> 创建 a 标签来定义导航链接，我们还可以借助 router 的实例方法，通过编写代码来实现。 
这个方法会向 history 栈添加一个新的记录，所以，当用户点击浏览器后退按钮时，则回到之前的 URL。 
当你点击 <router-link> 时，这个方法会在内部调用，所以说，点击 <router-link :to="..."> 等同于调用 router.push(...)。 

## router.push(location)
```
<router-link :to="..."> 
或者
router.push(...)  
```
该方法的参数可以是一个字符串路径，或者一个描述地址的对象。例如： 
```
// 字符串
router.push('home')
 
// 对象
router.push({ path: 'home' })
 
// 命名的路由
router.push({ name: 'user', params: { userId: 123 }})
 
// 带查询参数，变成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})
```

## router.replace(location) 
跟 router.push 很像，唯一的不同就是，它不会向 history 添加新记录，而是跟它的方法名一样 —— 替换掉当前的 history 记录。 
```
<router-link :to="..." replace>
或者
router.replace(...)  
```

## router.go(n) 
这个方法的参数是一个整数，意思是在 history 记录中向前或者后退多少步，类似 window.history.go(n)。 
例子:
```
// 在浏览器记录中前进一步，等同于 history.forward()
router.go(1)
 
// 后退一步记录，等同于 history.back()
router.go(-1)
 
// 前进 3 步记录
router.go(3)
 
// 如果 history 记录不够用，那就默默地失败呗
router.go(-100)
router.go(100)
```

## 操作 History 
你也许注意到 router.push、 router.replace 和 router.go 跟 window.history.pushState、 window.history.replaceState 和 window.history.go好像， 实际上它们确实是效仿 window.history API 的。 
因此，如果你已经熟悉 Browser History APIs，那么在 vue-router 中操作 history 就是超级简单的。 
还有值得提及的，vue-router 的导航方法 （push、 replace、 go） 在各类路由模式（history、 hash 和 abstract）下表现一致。 


#　vue-router 命名路由
VueRouter 的 name
有时候，通过一个名称来标识一个路由显得更方便一些，特别是在链接一个路由，或者是执行一些跳转的时候。你可以在创建 Router 实例的时候，在 routes 配置中给某个路由设置名称。 
```
const router = new VueRouter({
  routes: [
    {
      path: '/user/:id',
      name: 'user',
      component: User
    }
  ]
}) 
```

要链接到一个命名路由，可以给 router-link 的 to 属性传一个对象： 
```
<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link> 
```
这跟代码调用 router.push() 是一回事： 
```
router.push({ name: 'user', params: { userId: 123 }}) 
```
完整的例子:
注意最好用 :to 代替 to，用v-bind绑定给to 属性一个对象
$route.name 代表 route 的 name 属性
```
<div id="app">
  <h1>Hello App!</h1>
  <p>
    <router-link to="/foo">Go to Foo</router-link><br />
    <!--// 注意最好用 :to 代替 to，用绑定更好-->
    <router-link :to="{name: 'a'}">Go to Foo</router-link><br />
    <router-link :to="{name: 'b'}">Go to Foo1</router-link><br />
    <router-link :to="{name: 'ex'}">Go to Foo2</router-link><br />
  </p>
  <router-view></router-view>
</div>

<script type="text/javascript">

  const Foo = { template: '<div><h1>foo {{ this.$route.name }}</h1></div>' }
  const Bar = { template: '<div><h1>User {{ this.$route.name }}</h1></div>' }
  const example = { template: '<div><h1>example {{ this.$route.name }}</h1></div>' }
  
  const routes = [
    { path: '/foo', name:'a', component: Foo },
    { path: '/bar', name:'b', component: Bar },
    { path: '/example', name:'ex', component: example }
  ]
    
  const router = new VueRouter({
    routes: routes 
  })
    
  const app = new Vue({
    router: router,
    el: '#app',
  })
    
</script>
```

# vue-router 命名视图
VueRouter 中的 components
展示多个视图，而不是嵌套展示，例如创建一个布局，有 sidebar（侧导航） 和 main（主内容） 两个视图，这个时候命名视图就派上用场了。
你可以在界面中拥有多个单独命名的视图，而不是只有一个单独的出口。如果 router-view 没有设置名字，那么默认为 default。 
```
<router-view class="view one"></router-view>
<router-view class="view two" name="a"></router-view>
<router-view class="view three" name="b"></router-view>
```

一个视图使用一个组件渲染，因此对于同个路由，多个视图就需要多个组件。确保正确使用 components 配置（带上 s）： 
```
const router = new VueRouter({
  routes: [
    {
      path: '/',
      components: {
        default: Foo,
        a: Bar,
        b: Baz
      }
    }
  ]
})
```
完整示例
```
		<div id="app">
			<h1>Hello App!</h1>
			<router-view></router-view>
			<router-view name="a"></router-view>
			<router-view name="b"></router-view>
		</div>

		<script type="text/javascript">

			const Foo = { template: '<div><h1>foo {{ this.$route.name }}</h1></div>' }
			const Bar = { template: '<div><h1>User {{ this.$route.name }}</h1></div>' }
			const User = { template: '<div><h1>this is a user template</h1></div>' }
			 
			const router = new VueRouter({
			  routes: [
			  	{
			  		path: '/',
			  		name: 'homepage',
			  		components: {
			  			default: Foo,
			  			a: Bar,
			  			b: User
			  		}
			  	}
			  ]
			})
			 
			const app = new Vue({
			  router: router,
			  el: '#app',
			})
			 
		</script>
```

# vue-router 重定向 和 别名
## 重定向 
重定向也是通过 routes 配置来完成，下面例子是从 /a 重定向到 /b： 
```
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: '/b' }
  ]
}) 
```
重定向的目标也可以是一个命名的路由： 
```
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: { name: 'foo' }}
  ]
}) 
```
甚至是一个方法，动态返回重定向目标： 
```
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: to => {
      // 方法接收 目标路由 作为参数
      // return 重定向的 字符串路径/路径对象
    }}
  ]
})
```
示例：
/foo 重定向 redirect 到 /user
```
<div id="app">
  <h1>Hello App!</h1>

  <router-link to="/foo">Go to Foo</router-link><br />
  <router-link to="/bar">Go to Bar</router-link><br />
  <router-link to="/user">Go to Bar</router-link><br />
  
  <router-view></router-view>
</div>

<script type="text/javascript">

  const Foo = { template: '<div><h1>foo {{ this.$route.name }}</h1></div>' }
  const Bar = { template: '<div><h1>User {{ this.$route.name }}</h1></div>' }
  const User = { template: '<div><h1>this is a user template</h1></div>' }
    
  const router = new VueRouter({
    routes: [
      {
        path: '/foo',
        redirect: '/user',
        name: 'foopage',
        component: Foo
      },
      {
        path: '/bar',
        name: 'barpage',
        component: Bar
      },
      {
        path: '/user',
        name: 'homepage',
        component: User
      }
    ]
  })
    
  const app = new Vue({
    router: router,
    el: '#app',
  })
    
</script>
```
## 别名 
『重定向』的意思是，当用户访问 /a时，URL 将会被替换成 /b，然后匹配路由为 /b。 
/a 的别名是 /b，意味着，当用户访问 /b 时，URL 会保持为 /b，但是路由匹配则为 /a，就像用户访问 /a 一样。 

上面对应的路由配置为： 
```
const router = new VueRouter({
  routes: [
    { path: '/a', component: A, alias: '/b' }
  ]
}) 
```
『别名』的功能让你可以自由地将 UI 结构映射到任意的 URL，而不是受限于配置的嵌套路由结构。 
别名示例：
```
<div id="app">
  <h1>Hello App!</h1>

  <router-link to="/foo">Go to Foo</router-link><br />
  <router-link to="/bar">Go to Bar</router-link><br />
  <router-link to="/user">Go to User</router-link><br />
  <router-link to="/hello">Go to Hello</router-link><br />
  <router-link to="/a">Go to a</router-link><br />
  
  <router-view></router-view>
</div>

<script type="text/javascript">

  const Foo = { template: '<div><h1>foo {{ this.$route.name }}</h1></div>' }
  const Bar = { template: '<div><h1>User {{ this.$route.name }}</h1></div>' }
  const User = { template: '<div><h1>this is a user template</h1></div>' }
    
  const router = new VueRouter({
    routes: [
      {
        path: '/foo',
      //redirect: '/user', // 直接重定向到另外一个地址
        redirect: { name: 'barpage' }, //用 名称对象同样可以
        name: 'foopage',
        component: Foo,
        alias: ['/hello','/a'] // 别名，可以字符串或者数组
      },
      {
        path: '/bar',
        name: 'barpage',
        component: Bar
      },
      {
        path: '/user',
        name: 'homepage',
        component: User
      }
    ]
  })
    
  const app = new Vue({
    router: router,
    el: '#app',
  })
    
</script>
```


# vue-router HTML5 History 模式

vue-router 默认 hash 模式 —— 使用 URL 的 hash 来模拟一个完整的 URL，于是当 URL 改变时，页面不会重新加载。 
用路由的 history 模式，这种模式充分利用 history.pushState API 来完成 URL 跳转而无须重新加载页面。 
```
const router = new VueRouter({
  mode: 'history',
  routes: [...]
}) 
```

当你使用 history 模式时，URL 就像正常的 url，例如 http://yoursite.com/user/id ,也好看！ 

不过这种模式要玩好，还需要后台配置支持。因为我们的应用是个单页客户端应用，如果后台没有正确的配置，当用户在浏览器直接访问 http://oursite.com/user/id 就会返回 404，这就不好看了。 

所以呢，你要在服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面。 

后端配置例子 
```
Apache 
<IfModule mod_rewrite.c>
  RewriteEngine On
  RewriteBase /
  RewriteRule ^index\.html$ - [L]
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteRule . /index.html [L]
</IfModule>


nginx 
location / {
  try_files $uri $uri/ /index.html;
}
```

Node.js (Express) 

https://github.com/bripkens/connect-history-api-fallback 

警告 

给个警告，因为这么做以后，你的服务器就不再返回 404 错误页面，因为对于所有路径都会返回 index.html 文件。为了避免这种情况，你应该在 Vue 应用里面覆盖所有的路由情况，然后在给出一个 404 页面。 
```
const router = new VueRouter({
  mode: 'history',
  routes: [
    { path: '*', component: NotFoundComponent }
  ]
})
```

或者，如果你是用 Node.js 作后台，可以使用服务端的路由来匹配 URL，当没有匹配到路由的时候返回 404，从而实现 fallback。 
