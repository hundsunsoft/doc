---
title: web侠客行（五）--Vue高级教程
date: 2000-04-13 05:02:12
tags: 
    - vue
    - app
categories: 
    - 教程
    - web
---

# 自定义指令 Directive
el.focus 聚焦元素，使用 v-focus
其中，inserted是钩子函数

## 钩子函数
指令定义函数提供了几个钩子函数（可选）： 
• bind: 只调用一次，指令第一次绑定到元素时调用，用这个钩子函数可以定义一个在绑定时执行一次的初始化动作。 
• inserted: 被绑定元素插入父节点时调用（父节点存在即可调用，不必存在于 document 中）。 
• update: 被绑定元素所在的模板更新时调用，而不论绑定值是否变化。通过比较更新前后的绑定值，可以忽略不必要的模板更新（详细的钩子函数参数见下）。 
• componentUpdated: 被绑定元素所在模板完成一次更新周期时调用。 
• unbind: 只调用一次， 指令与元素解绑时调用。 

## 钩子函数参数
接下来我们来看一下钩子函数的参数 (包括 el，binding，vnode，oldVnode) 。 
钩子函数被赋予了以下参数： 
• el: 指令所绑定的元素，可以用来直接操作 DOM 。 
• binding: 一个对象，包含以下属性： ◦ name: 指令名，不包括 v- 前缀。 
◦ value: 指令的绑定值， 例如： v-my-directive="1 + 1", value 的值是 2。 
◦ oldValue: 指令绑定的前一个值，仅在 update 和 componentUpdated 钩子中可用。无论值是否改变都可用。 
◦ expression: 绑定值的字符串形式。 例如 v-my-directive="1 + 1" ， expression 的值是 "1 + 1"。 
◦ arg: 传给指令的参数。例如 v-my-directive:foo， arg 的值是 "foo"。 
◦ modifiers: 一个包含修饰符的对象。 例如： v-my-directive.foo.bar, 修饰符对象 modifiers 的值是 { foo: true, bar: true }。 

• vnode: Vue 编译生成的虚拟节点，查阅 VNode API 了解更多详情。 
• oldVnode: 上一个虚拟节点，仅在 update 和 componentUpdated 钩子中可用。 

## 注册自定义组件
当页面加载时，元素将获得焦点。事实上，你访问后还没点击任何内容，input 就获得了焦点。现在让我们完善这个指令： 
```
// 注册一个全局自定义指令 v-focus
<input type="text" name="" id="" value="" v-focus/>

Vue.directive('focus', {
  // 当绑定元素插入到 DOM 中。
  inserted: function (el) {
    // 聚焦元素
    el.focus()
  }
})
```
也可以注册局部指令，组件中接受一个 directives 的选项： 

directives: {
  focus: {
    // 指令的定义---
  }
}

然后你可以在模板中任何元素上使用新的 v-focus 属性： 

## 使用钩子样例
```
<div id="hook-arguments-example" v-demo:hello.a.b="message"></div>

<script type="text/javascript">
    Vue.directive('demo', {
        bind: function (el, binding, vnode) {
        var s = JSON.stringify
        el.innerHTML =
            'name: '       + s(binding.name) + '<br>' +
            'value: '      + s(binding.value) + '<br>' +
            'expression: ' + s(binding.expression) + '<br>' +
            'argument: '   + s(binding.arg) + '<br>' +
            'modifiers: '  + s(binding.modifiers) + '<br>' +
            'vnode keys: ' + Object.keys(vnode).join(', ')
        }
    })
                
    var vm = new Vue({
        el: '#hook-arguments-example',
        data: {
        message: 'hello!'
        }
    })
</script>
```
页面显示结果
```
name: "demo"
value: "hello!"
expression: "message"
argument: "hello"
modifiers: {"a":true,"b":true}
vnode keys: tag, data, children, text, elm, ns, context, fnContext, fnOptions, fnScopeId, key, componentOptions, componentInstance, parent, raw, isStatic, isRootInsert, isComment, isCloned, isOnce, asyncFactory, asyncMeta, isAsyncPlaceholder
```
## 函数简写 

大多数情况下，我们可能想在 bind 和 update 钩子上做重复动作，并且不想关心其它的钩子函数。可以这样写: 
```
Vue.directive('color-swatch', function (el, binding) {
  el.style.backgroundColor = binding.value
})
```

## 对象字面量 

如果指令需要多个值，可以传入一个 JavaScript 对象字面量。记住，指令函数能够接受所有合法类型的 Javascript 表达式。 
```
<div v-demo="{ color: 'white', text: 'hello!' }"></div>

Vue.directive('demo', function (el, binding) {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text)  // => "hello!"
})
```

# Render函数
需要 JavaScript 的完全编程的能力，这就是 render 函数，它比 template 更接近编译器。可以理解为动态模板
script type 扩展，可以用 type="text/x-template"代表模板
```
<script type="text/x-template" id="anchored-heading-template">
<script type="text/javascript">
```
而可以直接用模板的id引入模板
```
Vue.component('anchored-heading', {
    template: '#anchored-heading-template',
    props: {
    level: {
        type: Number,
        required: true
    }
    }
})
```
## render示例
```
<div id="app">
    <anchored-heading :level="1">Hello world!</anchored-heading>
    <anchored-heading :level="2">Hello world!</anchored-heading>
    <anchored-heading :level="3">Hello world!</anchored-heading>
    <anchored-heading :level="4">Hello world!</anchored-heading>
    <anchored-heading :level="5">Hello world!</anchored-heading>
    <anchored-heading :level="6">Hello world!</anchored-heading>
</div>

<script type="text/x-template" id="anchored-heading-template">
    <div>
    <h1 v-if="level === 1">
        <slot></slot>
    </h1>
    <h2 v-if="level === 2">
        <slot></slot>
    </h2>
    <h3 v-if="level === 3">
        <slot></slot>
    </h3>
    <h4 v-if="level === 4">
        <slot></slot>
    </h4>
    <h5 v-if="level === 5">
        <slot></slot>
    </h5>
    <h6 v-if="level === 6">
        <slot></slot>
    </h6>
    </div>
</script>

<script type="text/javascript">
    Vue.component('anchored-heading', {
        template: '#anchored-heading-template',
        props: {
            level: {
                type: Number,
                required: true
            }
        }
    })
    
    var vm = new Vue({
        el: '#app'
    })
</script>
```
## props高级用法
props用对象表示的时候，表示对值的限定，例如
```
props: {
    level: {
      type: Number,
      required: true
    }
  }
```
限定level是数字并有默认值。

## createElement
在 createElement 函数中生成模板。这里是 createElement 接受的参数： 
```
// @returns {VNode}
createElement(
  // {String | Object | Function}
  // 一个 HTML 标签，组件设置，或一个函数
  // 必须 Return 上述其中一个
  'div',
  // {Object}
  // 一个对应属性的数据对象
  // 您可以在 template 中使用.可选项.
  {
    // (下一章，将详细说明相关细节)
  },
  // {String | Array}
  // 子节点(VNodes). 可选项.
  [
    createElement('h1', 'hello world'),
    createElement(MyComponent, {
      props: {
        someProp: 'foo'
      }
    }),
    'bar'
  ]
)
```
## render函数示例
```
Vue.component('anchored-heading', {
  render: function (createElement) {
    return createElement(
      'h' + this.level,   // tag name 标签名称
      this.$slots.default // 子组件中的阵列
    )
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```

## 完整数据对象 

有一件事要注意：在 templates 中，v-bind:class 和 v-bind:style ，会有特别的处理，他们在 VNode 数据对象中，为最高级配置。 
```
{
  // 和`v-bind:class`一样的 API
  'class': {
    foo: true,
    bar: false
  },
  // 和`v-bind:style`一样的 API
  style: {
    color: 'red',
    fontSize: '14px'
  },
  // 正常的 HTML 特性
  attrs: {
    id: 'foo'
  },
  // 组件 props
  props: {
    myProp: 'bar'
  },
  // DOM 属性
  domProps: {
    innerHTML: 'baz'
  },
  // 事件监听器基于 "on"
  // 所以不再支持如 v-on:keyup.enter 修饰器
  // 需要手动匹配 keyCode。
  on: {
    click: this.clickHandler
  },
  // 仅对于组件，用于监听原生事件，而不是组件使用 vm.$emit 触发的事件。
  nativeOn: {
    click: this.nativeClickHandler
  },
  // 自定义指令. 注意事项：不能对绑定的旧值设值
  // Vue 会为您持续追踨
  directives: [
    {
      name: 'my-custom-directive',
      value: '2'
      expression: '1 + 1',
      arg: 'foo',
      modifiers: {
        bar: true
      }
    }
  ],
  // 如果子组件有定义 slot 的名称
  slot: 'name-of-slot'
  // 其他特殊顶层属性
  key: 'myKey',
  ref: 'myRef'
}
```
## 完整示例 

有了这方面的知识，我们现在可以完成我们最开始想实现的组件： 
this.$slots.default，当你不使用 slot 属性向组件中传递内容时，比如 anchored-heading 中的 Hello world!, 这些子元素被存储在组件实例中的 $slots.default中。
```
<div id="app">    
    <anchored-heading :level="1">Hello world!</anchored-heading>
    <anchored-heading :level="2">Hello world!</anchored-heading>
    <anchored-heading :level="3">Hello world!</anchored-heading>
    <anchored-heading :level="4">Hello world!</anchored-heading>
    <anchored-heading :level="5">Hello world!</anchored-heading>
    <anchored-heading :level="6">Hello world!</anchored-heading>    
</div>

<script type="text/javascript">
    var getChildrenTextContent = function (children) {
        return children.map(function (node) {
        return node.children
            ? getChildrenTextContent(node.children)
            : node.text
        }).join('')
    }
    Vue.component('anchored-heading', {
        render: function (createElement) {
        // create kebabCase id
        var headingId = getChildrenTextContent(this.$slots.default)
            // 转换小写
            .toLowerCase()
            // 空格转换 -
            .replace(/\W+/g, '-')
            //其他转换 空
            .replace(/(^\-|\-$)/g, '')
        return createElement(
            // 第一个参数 h1 - h6
            'h' + this.level,
            // 第二个参数空
            // 第三个参数 在h1 节点中增加a子节点，子节点属性 attrs，子节点的值用 this.$slots.default 取回来 
            [
            createElement('a', {
                attrs: {
                name: headingId,
                href: '#' + headingId
                }
            }, this.$slots.default)
            ]
        )
        },
        props: {
        level: {
            type: Number,
            required: true
        }
        }
    })

    var vm = new Vue({
        el: '#app'
    })
</script>
```

## VNodes 必须唯一 

所有组件树中的 VNodes 必须唯一。这意味着，下面的 render function 是无效的： 
```
render: function (createElement) {
  var myParagraphVNode = createElement('p', 'hi')
  return createElement('div', [
    // Yikes - duplicate VNodes!
    myParagraphVNode, myParagraphVNode
  ])
}
```
如果你真的需要重复很多次的元素/组件，你可以使用工厂函数来实现。例如，下面这个例子 render 函数完美有效地渲染了 20 个重复的段落： 
```
render: function (createElement) {
  return createElement('div',
    Array.apply(null, { length: 20 }).map(function () {
      return createElement('p', 'hi')
    })
  )
}
```
## 重复示例
div中重复20次 p
```
<div id="app">
    <anchored-heading>Hello world!</anchored-heading>
</div>

<script type="text/javascript">
    Vue.component('anchored-heading', {
        render: function(createElement) {
            return createElement('div',
                Array.apply(null, {
                    length: 20
                }).map(function() {
                    return createElement('p', 'hi')
                    // 增加属性对象的写法
                    // return createElement('p', {
					//			style: {
					//				color: 'red'
					//			}
					//		},
					//		'hi')
                })
            )
        }
    })

    var vm = new Vue({
        el: '#app'
    })
</script>
```

## 使用 JavaScript 代替模板功能 

无论什么都可以使用原生的 JavaScript 来实现，Vue 的 render 函数不会提供专用的 API。比如， template 中的 v-if 和 v-for: 
```
<ul v-if="items.length">
  <li v-for="item in items">{{ item.name }}</li>
</ul>
<p v-else>No items found.</p>
```
这些都会在 render 函数中被 JavaScript 的 if/else 和 map 重写： 
```
render: function (createElement) {
  if (this.items.length) {
    return createElement('ul', this.items.map(function (item) {
      return createElement('li', item.name)
    }))
  } else {
    return createElement('p', 'No items found.')
  }
}
```

## JSX 

如果你写了很多 render 函数，可能会觉得痛苦： 
```
createElement(
  'anchored-heading', {
    props: {
      level: 1
    }
  }, [
    createElement('span', 'Hello'),
    ' world!'
  ]
)
```
特别是模板如此简单的情况下： 
```
<anchored-heading :level="1">
  <span>Hello</span> world!
</anchored-heading>
```
这就是会有一个 Babel plugin 插件，用于在 Vue 中使用 JSX 语法的原因，它可以让我们回到于更接近模板的语法上。 
```
import AnchoredHeading from './AnchoredHeading.vue'
new Vue({
  el: '#demo',
  render (h) {
    return (
      <AnchoredHeading level={1}>
        <span>Hello</span> world!
      </AnchoredHeading>
    )
  }
})
```
将 h 作为 createElement 的别名是一个通用惯例，你会发现在 Vue 生态系统中，实际上必须用到 JSX，如果在作用域中 h 失去作用， 在应用中会触发报错。
示例
```
<div id="app">
    <anchored-heading>Hello world!</anchored-heading>
</div>

<script type="text/javascript">

    new Vue({
        el: '#app',
        data: {
            level: 1,
            type: 'button'
        },
        render(h) {
            return h(
                // 第一个参数 input 元素
                'input',
                // 第二个参数 type为button
                {
                attrs: {
                    type: 'button',
                    value: 'btn'
                }
                },
                // 第三个参数 空
                ''
            )
        }
    })
</script>
```

## 函数化组件 functional

之前创建的锚点标题组件是比较简单，没有管理或者监听任何传递给他的状态，也没有生命周期方法。它只是一个接收参数的函数。
 在这个例子中，我们标记组件为 functional， 这意味它是无状态（没有 data），无实例（没有 this 上下文）。
 一个 函数化组件 就像这样： 

Vue.component('my-component', {
  functional: true,
  // 为了弥补缺少的实例
  // 提供第二个参数作为上下文
  render: function (createElement, context) {
    // ...
  },
  // Props 可选
  props: {
    // ...
  }
})

组件需要的一切都是通过上下文传递，包括： 
• props: 提供props 的对象 
• children: VNode 子节点的数组 
• slots: slots 对象 
• data: 传递给组件的 data 对象 
• parent: 对父组件的引用 

在添加 functional: true 之后，锚点标题组件的 render 函数之间简单更新增加 context 参数，this.$slots.default 更新为 context.children，之后this.level 更新为 context.props.level。 

函数化组件只是一个函数，所以渲染开销也低很多。但同样它也有完整的组件封装，你需要知道这些， 比如： 
• 程序化地在多个组件中选择一个 
• 在将 children, props, data 传递给子组件之前操作它们。 

下面是一个依赖传入 props 的值的 smart-list 组件例子，它能代表更多具体的组件： 
```
var EmptyList = { /* ... */ }
var TableList = { /* ... */ }
var OrderedList = { /* ... */ }
var UnorderedList = { /* ... */ }
Vue.component('smart-list', {
  functional: true,
  render: function (createElement, context) {
    function appropriateListComponent () {
      var items = context.props.items
      if (items.length === 0)           return EmptyList
      if (typeof items[0] === 'object') return TableList
      if (context.props.isOrdered)      return OrderedList
      return UnorderedList
    }
    return createElement(
      appropriateListComponent(),
      context.data,
      context.children
    )
  },
  props: {
    items: {
      type: Array,
      required: true
    },
    isOrdered: Boolean
  }
})
```

## slots() 和 children 对比 

你可能想知道为什么同时需要 slots() 和 children。slots().default 不是和 children 类似的吗？在一些场景中，是这样，但是如果是函数式组件和下面这样的 children 呢？ 
```
<my-functional-component>
  <p slot="foo">
    first
  </p>
  <p>second</p>
</my-functional-component>
```
对于这个组件，children 会给你两个段落标签，而 slots().default 只会传递第二个匿名段落标签，slots().foo 会传递第一个具名段落标签。同时拥有 children 和 slots() ，因此你可以选择让组件通过 slot() 系统分发或者简单的通过 children 接收，让其他组件去处理。

# 插件
## 开发插件 

插件通常会为Vue添加全局功能。插件的范围没有限制——一般有下面几种： 

1. 添加全局方法或者属性，如: vue-element 

2. 添加全局资源：指令/过滤器/过渡等，如 vue-touch 

3. 通过全局 mixin方法添加一些组件选项，如: vuex 

4. 添加 Vue 实例方法，通过把它们添加到 Vue.prototype 上实现。 

5. 一个库，提供自己的 API，同时提供上面提到的一个或多个功能，如 vue-router 

Vue.js 的插件应当有一个公开方法 install 。这个方法的第一个参数是 Vue 构造器 , 第二个参数是一个可选的选项对象: 
```
MyPlugin.install = function (Vue, options) {
  // 1. 添加全局方法或属性
  Vue.myGlobalMethod = function () {
    // 逻辑...
  }
  // 2. 添加全局资源
  Vue.directive('my-directive', {
    bind (el, binding, vnode, oldVnode) {
      // 逻辑...
    }
    ...
  })
  // 3. 注入组件
  Vue.mixin({
    created: function () {
      // 逻辑...
    }
    ...
  })
  // 4. 添加事例方法
  Vue.prototype.$myMethod = function (options) {
    // 逻辑...
  }
}
```
## 使用插件 

通过全局方法 Vue.use() 使用插件: 
```
// 调用 `MyPlugin.install(Vue)`
Vue.use(MyPlugin)

也可以传入一个选项对象: 

Vue.use(MyPlugin, { someOption: true })

Vue.use 会自动阻止注册相同插件多次，届时只会注册一次该插件。 

一些插件，如 vue-router 如果 Vue 是全局变量则自动调用 Vue.use() 。不过在模块环境中应当始终显式调用 Vue.use() : 

// 通过 Browserify 或 Webpack 使用 CommonJS 兼容模块
var Vue = require('vue')
var VueRouter = require('vue-router')
// 不要忘了调用此方法
Vue.use(VueRouter)
```
awesome-vue 集合了来自社区贡献的数以千计的插件和库。 

## let,const与var的区别
let：作用域为代码块内
const：只读，必须立即初始化，作用域为代码块内。常量不可更改，对象可以更改
var：变量提升，可以先使用后申明

# 路由 vue-router
## 从零开始简单的路由 
如果只需要非常简单的路由而不需要引入整个路由库，可以动态渲染一个页面级的组件像这样： 
```
const NotFound = { template: '<p>Page not found</p>' }
const Home = { template: '<p>home page</p>' }
const About = { template: '<p>about page</p>' }
const routes = {
  '/': Home,
  '/about': About
}
new Vue({
  el: '#app',
  data: {
    currentRoute: window.location.pathname
  },
  computed: {
    ViewComponent () {
      return routes[this.currentRoute] || NotFound
    }
  },
  render (h) { return h(this.ViewComponent) }
})
```
结合HTML5 History API，你可以建立一个非常基本但功能齐全的客户端路由器。可以直接看实例应用 

整合第三方路由 

如果有非常喜欢的第三方路由，如Page.js或者 Director, 整合很简单。 这有个用了Page.js的复杂示例 。 

## 新建项目
vuetest为项目名
```
vue init webpack vuetest
```
## 安装依赖
安装sass:
```
cnpm install node-sass --save-dev
cnpm install sass-loader --save-dev
```
安装vuex:
```
cnpm install vuex --save
```

## vue-router
  router-link 组件支持用户在具有路由功能的应用中 (点击) 导航。 通过 to 属性指定目标地址，默认渲染成带有正确链接的 a 标签，可以通过配置 tag 属性生成别的标签.。另外，当目标路由成功激活时，链接元素自动设置一个表示激活的 CSS 类名。

## 将激活 class 应用在外层元素
```
<router-link tag="li" to="/foo">
  <a>/foo</a>
</router-link>
```

## to
表示目标路由的链接。当被点击后，内部会立刻把 to 的值传到 router.push()，所以这个值可以是一个字符串或者是描述目标位置的对象。
```
<!-- 字符串 -->
<router-link to="home">Home</router-link>
<!-- 渲染结果 -->
<a href="home">Home</a>

<!-- 使用 v-bind 的 JS 表达式 -->
<router-link v-bind:to="'home'">Home</router-link>

<!-- 不写 v-bind 也可以，就像绑定别的属性一样 -->
<router-link :to="'home'">Home</router-link>

<!-- 同上 -->
<router-link :to="{ path: 'home' }">Home</router-link>

<!-- 命名的路由 -->
<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>

<!-- 带查询参数，下面的结果为 /register?plan=private -->
<router-link :to="{ path: 'register', query: { plan: 'private' }}">Register</router-link>
```

# vue-resource
```
1、安装依赖包
cnpm install vue-resource --save
2、进行ajax请求
{
  // GET /someUrl
  this.$http.get('/someUrl').then(response => {

    // get body data
    this.someData = response.body;

  }, response => {
    // error callback
  });
}
```

# vuex 状态管理
mapState mapActions
weex 与 vue

# Vue.js 使用插件
## Vue.js 使用sass
```
1、安装sass依赖包
cnpm install --save-dev sass-loader
//sass-loader依赖于node-sass
cnpm install --save-dev node-sass

2、在build文件夹下的webpack.base.conf.js的rules里面添加配置
{
  test: /\.sass$/,
  loaders: ['style', 'css', 'sass']
}

3、在APP.vue中修改style标签
<style lang="scss">
```

## Vue.js 使用less
```
1、安装less依赖包
cnpm install less less-loader --save

2、修改webpack.config.js文件，配置loader加载依赖，让其支持外部的less,在原来的代码上添加
{
    test: /\.less$/,
    loader: "style-loader!css-loader!less-loader",
},

3、在APP.vue中修改style标签
<style lang="less">
```
