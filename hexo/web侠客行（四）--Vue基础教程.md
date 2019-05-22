---
title: web侠客行（四）--Vue基础教程
date: 2000-04-13 04:02:12
tags: 
    - vue
    - app
categories: 
    - 教程
    - web
---

# 创建vue项目
## 1、引入vue.js库
```
<script src="lib/vue.js"></script>
```
这将暴露出一个全局类——Vue，你可以用它来创建一个Vue实例。

## 2、创建Vue实例

Vue是一个封装了响应式开发、模板编译等诸多特性的基础类，你通过提供一些 配置项，来创建一个实例：
```
var vm = new Vue({...});

var vm = new Vue({
  template: '<h1>Hello,Vue.js 2</h1>'
})
```
一个常见的配置项是template，以类似HTML的语法来编制视图的结构：

## 3、渲染Vue实例

要将Vue实例渲染到HTML页面中，采用Vue实例的$mount()方法，这个方法 的名称，意味着渲染实际上是将Vue实例生成的（虚拟）DOM子树，挂接到页面DOM中。

容易理解，$mount()方法需要指定一个定位用的DOM节点———锚点：
```
vm.$mount(anchor_element);
```
Vue.js会将渲染出的DOM子树，插入锚点元素之前（并最终删除这个锚点元素）。

可以使用CSS选择符或者指定一个HTMLElement来声明锚点。例如， 下面的示例将Vue实例挂接到id为app的DOM对象处：
```
vm.$mount('#app');
```

## 4、示例代码
```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<!-- 开发环境版本，包含了有帮助的命令行警告 -->
		<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
		
		<!-- 生产环境版本，优化了尺寸和速度 -->
		<!-- <script src="https://cdn.jsdelivr.net/npm/vue"></script> -->
	</head>
	<body>
		<h1>vue示例</h1>
		<h1 id="app"></h1>
		<script type="text/javascript">
			var vm = new Vue({
				template: '<h1>Hello,Vue.js 1</h1>'
			})
			
			vm.$mount('#app')
		</script>
	</body>
</html>

```
**改变html的内容并保存，页面会随之刷新。**

## el隐式渲染
如果Vue.js检测到你指定了el配置项，将在内部自动地执行渲染 —— 这时你 不再需要额外调用$mount()方法。
```
Vue({
  template: '<h1>Hello,Vue.js 2!</h1>',
  el: '#app'
})
```

## 示例
```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<!-- 开发环境版本，包含了有帮助的命令行警告 -->
		<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
		
		<!-- 生产环境版本，优化了尺寸和速度 -->
		<!-- <script src="https://cdn.jsdelivr.net/npm/vue"></script> -->
	</head>
	<body>
		<h1>vue示例</h1>
		<h1 id="app"></h1>
		<script type="text/javascript">
			var vm = new Vue({
				template: '<h1>Hello,Vue.js 2</h1>',
				el:'#app'
			})
		</script>
	</body>
</html>
```

# 模板 template
模板的存在的唯一目的，是为了和数据绑定。
```
var vm = new Vue({
  template:'<h1>{{name}}</h1>',
  data:{
    name:'Elton John'
  },
  el:'#app'
})
```

当Vue.js创建一个Vue实例时，它会将data配置项的每个根属性，（经过若干处理后） 添加为实例的根属性。
即：

vm:Vue
* $options
* $mount()
* name

因此，实际上我们可以在模板中绑定实例的任意属性。例如：下面的模板可以输出 $mount()方法的源代码：
```
<pre>{{$mount}}</pre>
```

# 响应式计算 与 交互行为声明
响应式计算是一种面向变化传播的编程范式，响应式计算模型主要包括 两个部分：数据源和（依赖于数据源的）计算过程。当数据源发生变化时， 将自动执行计算过程（比如视图的渲染过程）。它使得我们只需要修改 数据源就可以自动更新用户界面。

视图的作用是双向的，除了向用户展示信息，另一方面的用途在于采集用户的输入。

和数据绑定类似，Vue.js通过扩展模板的HTML语法，来声明对用户交互事件 的监听。例如，下面的模板向Vue.js框架声明了对button元素的click 事件的监听：

```
<button v-on:click="counter=0">RESET</button>
```
容易注意到button元素的特殊属性：v-on:click。在Vue.js中，这种以 v-为前缀的特殊的HTML属性，被称为指令，通常用来增强或改变所在 HTML元素的行为。例如，v-on指令的作用，就是为宿主元素（在这里是button） 声明事件监听

## 实例方法声明
方法与this用法
```
# 实例
new Vue({
  template: '<button v-on:click="reset">{{counter}}</button>',
  data: { counter: 0},
  methods: {
    reset: function(){ this.counter = 0; }
  }
})

# 调用
vm.handler();
```

# v- 指令
## v-bind
v-bind 特性被称为指令。指令带有前缀 v-，以表示它们是 Vue 提供的特殊特性。它们会在渲染的 DOM 上应用特殊的响应式行为。在这里，该指令的意思是：“将这个元素节点的 title 特性和 Vue 实例的 message 属性保持一致”。
```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<!-- 开发环境版本，包含了有帮助的命令行警告 -->
		<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

		<!-- 生产环境版本，优化了尺寸和速度 -->
		<!-- <script src="https://cdn.jsdelivr.net/npm/vue"></script> -->
	</head>
	<body>
		<h1>vue示例</h1>

		<div id="app">
			<span v-bind:title="message">
				鼠标悬停几秒钟查看此处动态绑定的提示信息！
			</span>
		</div>

		<script type="text/javascript">
			var app2 = new Vue({
				el: '#app',
				data: {
					message: '页面加载于 ' + new Date().toLocaleString()
				}
			})
		</script>

		scr
	</body>
</html>
```
一个v-bind用法示例
```
<form action="index.html" method="get">
	<input type="submit" value="submit"/>
</form>
		
<form v-bind:action="newacction" v-bind:method="newmethod" id="app2">
	<input type="submit" value="submit"/>
</form>
		
<script type="text/javascript">
	var app2 = new Vue({
		el: '#app2',
		data: {
			newacction: 'index2.html',
			newmethod: 'get'
		}
	})
</script>
```

## v-if 条件与循环
控制切换一个元素是否显示也相当简单：
```
<div id="app-3">
  <p v-if="seen">现在你看到我了</p>
</div>
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
```
v-if 示例
```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<!-- 开发环境版本，包含了有帮助的命令行警告 -->
		<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

		<!-- 生产环境版本，优化了尺寸和速度 -->
		<!-- <script src="https://cdn.jsdelivr.net/npm/vue"></script> -->
	</head>
	<body>
		<h1>index5.html</h1>

		<div id="app-3">
			<p v-if="seen">现在你看到我了</p>
		</div>

		<input type="button" name="" id="bt1" value="显示与隐藏" onclick="test()" />

		<script type="text/javascript">
			var app3 = new Vue({
				el: '#app-3',
				data: {
					seen: true
				}
			})

			function test() {
				if (app3.seen == true)
					app3.seen = false;
				else
					app3.seen = true;

				app3.$mount('#app-3');
			}
		</script>


	</body>
</html>
```

## v-else
```
<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>
```
示例
```
<div v-if="Math.random() > 0.5">
  Now you see me
</div>
<div v-else>
  Now you don't
</div>
```

## v-for
v-for 指令可以绑定数组的数据来渲染一个项目列表。
```
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>

var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: '学习 JavaScript' },
      { text: '学习 Vue' },
      { text: '整个牛项目' }
    ]
  }
})
```

## v-on
v-on 指令添加一个事件监听器，通过它调用在 Vue 实例中定义的方法。
```
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">逆转消息</button>
</div>

var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
```
method与this用法
```
<div id="app-3">
	<p v-if="seen">现在你看到我了</p>
	<input type="button" name="" id="bt1" value="显示与隐藏" v-on:click="method1" />
</div>
	
<script type="text/javascript">
	var app3 = new Vue({
		el: '#app-3',
		data: {
			seen: true					
		},
		methods:{
			method1: function(){
				if (this.seen == true)
					this.seen = false;
				else
					this.seen = true;							
			}
		}
	})
</script>
```

## index 和 key
默认用index排列
```
<div v-for="(value, key, index) in object">
  {{ index }}. {{ key }}: {{ value }}
</div>
```
为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一 key 属性。理想的 key 值是每项都有的唯一 id。这个特殊的属性相当于 Vue 1.x 的 track-by ，但它的工作方式类似于一个属性，所以你需要用 v-bind 来绑定动态值。
```
<div v-for="item in items" v-bind:key="item.id">
  <!-- 内容 -->
</div>
```

## v-model
v-model 指令，它能轻松实现表单输入和应用状态之间的双向绑定。
```
<div id="app">
  <h1>{{message}}</h1>
  <input type="text" name="" id="" value="" v-model="message" />
</div>

</div>
<script type="text/javascript">
  var app4 = new Vue({
    el: '#app',
    data: {
      message: "hello vue"
    }
  })
</script>
```

# 组件化应用构建
组件系统是 Vue 的另一个重要概念，因为它是一种抽象，允许我们使用小型、独立和通常可复用的组件构建大型应用。仔细想想，几乎任意类型的应用界面都可以抽象为一个组件树。

注册组件
```
// 定义名为 todo-item 的新组件
Vue.component('todo-item', {
  template: '<li>这是个待办项</li>'
})
```

组件使用
```
<ol>
  <!-- 创建一个 todo-item 组件的实例 -->
  <todo-item></todo-item>
</ol>
```

使用props接收属性
```
Vue.component('todo-item', {
  // todo-item 组件现在接受一个
  // "prop"，类似于一个自定义特性。
  // 这个 prop 名为 todo。
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})
```

## 组件使用示例
使用todo-item模板，v-for循环，item为数组groceryList的一项；v-bind绑定属性，todo绑定item，即绑定数组的一项；key是关键字属性，每个组件都需要。
```
<div id="app-7">
  <ol>
    <!--
      现在我们为每个 todo-item 提供 todo 对象
      todo 对象是变量，即其内容可以是动态的。
      我们也需要为每个组件提供一个“key”，稍后再
      作详细解释。
    -->
    <todo-item
      v-for="item in groceryList"
      v-bind:todo="item"
      v-bind:key="item.id"
    ></todo-item>
  </ol>
</div>
```
注册组件实例：
todo-item，为模板；
todo，为data；
todo.text，为data内容。
```
Vue.component('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})

var app7 = new Vue({
  el: '#app-7',
  data: {
    groceryList: [
      { id: 0, text: '蔬菜' },
      { id: 1, text: '奶酪' },
      { id: 2, text: '随便其它什么人吃的东西' }
    ]
  }
})
```

# 生命周期钩子 Lifecycle Hook
## 初始化钩子
初始化钩子包括beforeCreate和created。这两个钩子允许我们在实例被渲染 到DOM之前执行一些初始化操作。由于DOM还未就绪，在初始化钩子里，不能访问DOM 对象，实例的$el属性 —— 宿主DOM对象 —— 也没有创建

beforeCreate是最早被调用的钩子，这时Vue.js还没有构造响应式数据源，也没 有初始化实例的事件。

在created钩子里，我们可以访问响应式数据、监听实例事件。不过还没有将虚拟 DOM渲染到文档DOM树。

## create钩子用法
created 钩子可以用来在一个实例被创建之后执行代码：
```
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```

## DOM挂载钩子
挂载钩子包括beforeMount和mounted，是最常使用的钩子。这两个钩子允许我们 在首次渲染*前后立即访问Vue实例。因此，如果我们需要在首次渲染前后访问或修改 DOM对象（例如，通过实例的$el属性访问宿主元素），就应该使用这两个钩子

beforeMount钩子在模板编译完成后、首次渲染前执行。

在mounted钩子内可以自由地访问组件渲染后的DOM对象（this.$el）。这个钩子 经常被用于修改DOM、集成第三方库等操作。

## 更新钩子

更新钩子包括beforeUpdate和updated。每当实例需要重新渲染（例如模型发生变化等）， 框架就会调用这两个钩子

beforeUpdate钩子在模型数据变化之后、渲染周期开始之前执行。在这个钩子里我们可以 在界面渲染前获取实例属性的最新值。

updated钩子在重新渲染完成后被调用。

## DOM卸载钩子

DOM卸载钩子包括beforeDestroy和destroyed，当实例被从DOM树移除时执行。 这两个钩子允许我们在实例销毁前后执行一些清理或统计分析的工作

beforeDestroy钩子在实例被销毁（利用，通过调用实例的$destroy()方法）之前被调用。 在这个钩子里可以清理对响应式数据的监听。

destroyed钩子在实例被销毁之后被调用，此时实例已经不剩什么东西了:-( 也可以 在这个钩子里执行一些最后时刻的清理工作，或者向远程服务器通知实例被销毁的消息

# 模板语法
## 文本 与 v-once
```
<span>Message: {{ msg }}</span>
```
通过使用 v-once 指令，你也能执行一次性地插值，当数据改变时，插值处的内容不会更新。
```
<span v-once>这个将不会改变: {{ msg }}</span>
```
## 原始html 与 v-html
v-html能把值解释成为html
```
<p>Using mustaches: {{ rawHtml }}</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

## 使用JS表达式
```
{{ number + 1 }}
{{ ok ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}
<div v-bind:id="'list-' + id"></div>
```

## 指令 v- 与 缩写
v-if:
```
<p v-if="seen">现在你看到我了</p>
```
这里，v-if 指令将根据表达式 seen 的值的真假来插入/移除 p 元素。

v-on:
```
<!-- 完整语法 -->
<a v-on:click="doSomething">...</a>

<!-- 缩写 -->
<a @click="doSomething">...</a>
```

v-bind缩写
```
<!-- 完整语法 -->
<a v-bind:href="url">...</a>

<!-- 缩写 -->
<a :href="url">...</a>
```

# 计算属性和侦听器
计算属性缓存 vs 方法，计算属性是基于它们的响应式依赖进行缓存的。只在相关响应式依赖发生改变时它们才会重新求值。这就意味着只要 message 还没有发生改变，多次访问 reversedMessage 计算属性会立即返回之前的计算结果，而不必再次执行函数。

## 计算属性 computed
```
# html
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>

# js
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
})
```

计算属性默认只有 getter ，不过在需要时你也可以提供一个 setter ：
```
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```

## 侦听器 watch
```
#html
<div id="watch-example">
  <p>
    Ask a yes/no question:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>

#js
<!-- 因为 AJAX 库和通用工具的生态已经相当丰富，Vue 核心代码没有重复 -->
<!-- 提供这些功能以保持精简。这也可以让你自由选择自己更熟悉的工具。 -->
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.13.1/lodash.min.js"></script>
<script>
var watchExampleVM = new Vue({
  el: '#watch-example',
  data: {
    question: '',
    answer: 'I cannot give you an answer until you ask a question!'
  },
  watch: {
    // 如果 `question` 发生改变，这个函数就会运行
    question: function (newQuestion, oldQuestion) {
      this.answer = 'Waiting for you to stop typing...'
      this.debouncedGetAnswer()
    }
  },
  created: function () {
    // `_.debounce` 是一个通过 Lodash 限制操作频率的函数。
    // 在这个例子中，我们希望限制访问 yesno.wtf/api 的频率
    // AJAX 请求直到用户输入完毕才会发出。想要了解更多关于
    // `_.debounce` 函数 (及其近亲 `_.throttle`) 的知识，
    // 请参考：https://lodash.com/docs#debounce
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 500)
  },
  methods: {
    getAnswer: function () {
      if (this.question.indexOf('?') === -1) {
        this.answer = 'Questions usually contain a question mark. ;-)'
        return
      }
      this.answer = 'Thinking...'
      var vm = this
      axios.get('https://yesno.wtf/api')
        .then(function (response) {
          vm.answer = _.capitalize(response.data.answer)
        })
        .catch(function (error) {
          vm.answer = 'Error! Could not reach the API. ' + error
        })
    }
  }
})
</script>
```

## 绑定 HTML Class 
使用data绑定
```
<div v-bind:class="classObject"></div>
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```
或者使用计算属性
```
<div v-bind:class="classObject"></div>
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```
利用组件绑定class：
```
申明组件：（注意，在使用组件的时候要添加的class也会渲染）
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
})

然后在使用它的时候添加一些 class：
<my-component class="baz boo"></my-component>
或
<my-component v-bind:class="{ active: isActive }"></my-component>

HTML 将被渲染为:
<p class="foo bar baz boo">Hi</p>
```

## 绑定style v-bind:style
```
<div v-bind:style="styleObject"></div>
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
或数组
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

## 条件渲染 v-if 与 v-else
```
<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>
或当做包裹元素
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```
你可以使用 v-else 指令来表示 v-if 的“else 块”。v-else 元素必须紧跟在带 v-if 或者 v-else-if 的元素的后面，否则它将不会被识别。
```
<div v-if="Math.random() > 0.5">
  Now you see me
</div>
<div v-else>
  Now you don't
</div>
```
v-else-if
```
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```
Vue 为你提供了一种方式来表达“这两个元素是完全独立的，不要复用它们”。只需添加一个具有唯一值的 key 属性即可：
```
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```

## v-show
另一个用于根据条件展示元素的选项是 v-show 指令。用法大致一样。不同的是带有 v-show 的元素始终会被渲染并保留在 DOM 中。v-show 只是简单地切换元素的 CSS 属性 display。
```
<h1 v-show="ok">Hello!</h1>
```
一般来说，v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 v-show 较好；如果在运行时条件很少改变，则使用 v-if 较好。

## v-for 列表渲染
在 v-for 块中，我们拥有对父作用域属性的完全访问权限。v-for 还支持一个可选的第二个参数为当前项的索引。
```
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```
可以提供第二个的参数为键名,第三个参数为索引:
```
<div id="app">
  <ul>
    <li v-for="(value,key,index) in obj">{{key}} - {{index}} - {{value}}</li>
  </ul>
</div>
<script type="text/javascript">
  var vm = new Vue({
    el: '#app',
    data: {
      obj: {
        message1: 'hello vue111',
        message2: 'hello vue222',
        message3: 'hello vue333',
      }
    }
  })
</script>
```

# 组件更新的几种方式
```
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)

// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)

// 你也可以使用 vm.$set 实例方法，该方法是全局方法 Vue.set 的一个别名：
vm.$set(vm.items, indexOfItem, newValue)

// new length
vm.items.splice(newLength)
```

Vue 包含一组观察数组的变异方法，所以它们也将会触发视图更新。这些方法如下：
```
push()
pop()
shift()
unshift()
splice()
sort()
reverse()
#你打开控制台，然后用前面例子的 items 数组调用变异方法：example1.items.push({ message: 'Baz' }) 。
```

变异方法 (mutation method)，顾名思义，会改变被这些方法调用的原始数组。相比之下，也有非变异 (non-mutating method) 方法，例如：filter(), concat() 和 slice() 。这些不会改变原始数组，但总是返回一个新数组。当使用非变异方法时，可以用新数组替换旧数组：
```
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```
你可能认为这将导致 Vue 丢弃现有 DOM 并重新渲染整个列表。幸运的是，事实并非如此。Vue 为了使得 DOM 元素得到最大范围的重用而实现了一些智能的、启发式的方法，所以用一个含有相同元素的数组去替换原来的数组是非常高效的操作。

对于已经创建的实例，Vue 不能动态添加根级别的响应式属性。但是，可以使用 Vue.set(object, key, value) 方法向嵌套对象添加响应式属性。例如，对于：
```
var vm = new Vue({
  data: {
    userProfile: {
      name: 'Anika'
    }
  }
})

你可以添加一个新的 age 属性到嵌套的 userProfile 对象：
Vue.set(vm.userProfile, 'age', 27)

你还可以使用 vm.$set 实例方法，它只是全局 Vue.set 的别名：
vm.$set(vm.userProfile, 'age', 27)
```

## 显示、过滤
```
<li v-for="n in evenNumbers">{{ n }}</li>
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
computed: {
  evenNumbers: function () {
    return this.numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}

在计算属性不适用的情况下 (例如，在嵌套 v-for 循环中) 你可以使用一个 method 方法：

<li v-for="n in even(numbers)">{{ n }}</li>
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
methods: {
  even: function (numbers) {
    return numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

## v-for 也可以取整数

在这种情况下，它将重复多次模板。
```
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```
用template
```
<div id="app">
  <ul>
    // 用template代替 li 中的 v-for 属性
    <!-- <li v-for="(value,key,index) in obj">{{key}} - {{index}} - {{value}}</li> -->
    <template v-for="(value,key,index) in obj">
      <li>{{key}} - {{index}} - {{value}}</li>
    </template>
  </ul>
</div>
<script type="text/javascript">
  var vm = new Vue({
    el: '#app',
    data: {
      obj: {
        message1: 'hello vue111',
        message2: 'hello vue222',
        message3: 'hello vue333',
      }
    }
  })
```
示例：
```
<div id="todo-list-example">
  <form v-on:submit.prevent="addNewTodo">
    <label for="new-todo">Add a todo</label>
    <input
      v-model="newTodoText"
      id="new-todo"
      placeholder="E.g. Feed the cat"
    >
    <button>Add</button>
  </form>
  <ul>
    <li
      is="todo-item"
      v-for="(todo, index) in todos"
      v-bind:key="todo.id"
      v-bind:title="todo.title"
      v-on:remove="todos.splice(index, 1)"
    ></li>
  </ul>
</div>
#注意这里的 is="todo-item" 属性。这种做法在使用 DOM 模板时是十分必要的，因为在 <ul> 元素内只有 <li> 元素会被看作有效内容。这样做实现的效果与 <todo-item> 相同，但是可以避开一些潜在的浏览器解析错误。查看 DOM 模板解析说明 来了解更多信息。

Vue.component('todo-item', {
  template: '\
    <li>\
      {{ title }}\
      <button v-on:click="$emit(\'remove\')">Remove</button>\
    </li>\
  ',
  props: ['title']
})

new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [
      {
        id: 1,
        title: 'Do the dishes',
      },
      {
        id: 2,
        title: 'Take out the trash',
      },
      {
        id: 3,
        title: 'Mow the lawn'
      }
    ],
    nextTodoId: 4
  },
  methods: {
    addNewTodo: function () {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})
```

# 表单输入绑定
## input 与 textarea
```
<div id="app">
  <!-- input绑定 -->
  <input v-model="message" placeholder="edit me"><br>
  <p>Message is: {{ message }}</p><br>

  <!-- textarea绑定 -->
  <textarea v-model="message" placeholder="add multiple lines"></textarea><br>

  <!-- chechkbox绑定 -->
  <input type="checkbox" id="checkbox" v-model="checked">
  <label for="checkbox">{{ checked }}</label><br>
</div>

<script type="text/javascript">
  var vm = new Vue({
    el: '#app',
    data: {
      message: 'hello vue',
      checked: 'ture'
    }
  })
</script>
```
## 复选框 checkbox
```
<div id='example-3'>
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
  <label for="jack">Jack</label>
  <input type="checkbox" id="john" value="John" v-model="checkedNames">
  <label for="john">John</label>
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
  <label for="mike">Mike</label>
  <br>
  <span>Checked names: {{ checkedNames }}</span>
</div>

<script type="text/javascript">
  new Vue({
    el: '#example-3',
    data: {
      checkedNames: []
    }
  })
</script>
```
## 单选框 radio
```
<div id="example-4">
  <input type="radio" id="one" value="One" v-model="picked">
  <label for="one">One</label>
  <br>
  <input type="radio" id="two" value="Two" v-model="picked">
  <label for="two">Two</label>
  <br>
  <span>Picked: {{ picked }}</span>
</div>

<script type="text/javascript">
  new Vue({
    el: '#example-4',
    data: {
      picked: ''
    }
  })
</script>
```

## 选择框 select 单选
```
<div id="example-5">
  <select v-model="selected">
    <option disabled value="">请选择</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <span>Selected: {{ selected }}</span>
</div>

<script type="text/javascript">
  new Vue({
    el: '#example-5',
    data: {
      selected: ''
    }
  })
</script>
```

## 选择框 select 多选
绑定到数组
```
<div id="example-6">
  <select v-model="selected" multiple style="width: 50px;">
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <br>
  <span>Selected: {{ selected }}</span>
</div>
new Vue({
  el: '#example-6',
  data: {
    selected: []
  }
})
```

## 修饰符
.lazy

在默认情况下，v-model 在每次 input 事件触发后将输入框的值与数据进行同步 (除了上述输入法组合文字时)。你可以添加 lazy 修饰符，从而转变为使用 change 事件进行同步：
```
<!-- 在“change”时而非“input”时更新 -->
<input v-model.lazy="msg" >
```

.number

如果想自动将用户的输入值转为数值类型，可以给 v-model 添加 number 修饰符：
```
<input v-model.number="age" type="number">
```
这通常很有用，因为即使在 type="number" 时，HTML 输入元素的值也总会返回字符串。如果这个值无法被 parseFloat() 解析，则会返回原始的值。

.trim

如果要自动过滤用户输入的首尾空白字符，可以给 v-model 添加 trim 修饰符：
```
<input v-model.trim="msg">
```

# 组件
## 组件复用属性
```
<div id="components-demo">
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
```

## 组件的data
一个组件的 data 选项必须是一个函数，因此每个实例可以维护一份被返回对象的独立的拷贝：
```
data: function () {
  return {
    count: 0
  }
}
```
如果 Vue 没有这条规则，点击一个按钮就可能会影响到其它所有实例

## 通过 Prop 向子组件传递数据
```
Vue.component('blog-post', {
  props: ['title'],
  template: '<h3>{{ title }}</h3>'
})

<blog-post title="My journey with Vue"></blog-post>
<blog-post title="Blogging with Vue"></blog-post>
<blog-post title="Why Vue is so fun"></blog-post>
```

## 组件示例
```
<div id="blog-post-demo">
  <blog-post v-for="post in posts" v-bind:key="post.id" v-bind:post="post"></blog-post>
</div>

<script type="text/javascript">
  Vue.component('blog-post', {
    props: ['post'],
    template: '<div class="blog-post">\
            <h3>{{ post.title }}</h3>\
            <div v-html="post.content"></div>\
          </div>'
  })

  new Vue({
    el: '#blog-post-demo',
    data: {
      posts: [{
          id: 1,
          title: 'My journey with Vue'
        },
        {
          id: 2,
          title: 'Blogging with Vue'
        },
        {
          id: 3,
          title: 'Why Vue is so fun'
        }
      ]
    }
  })
</script>
```

$emit，触发事件enlarge-text，enlarge-text事件绑定为postFontSize += 0.1
```
<div id="blog-posts-events-demo" class="demo">
  <div :style="{ fontSize: postFontSize + 'em' }">
    <blog-post v-for="post in posts" v-bind:key="post.id" v-bind:post="post" v-on:enlarge-text="postFontSize += 0.1"></blog-post>
  </div>
</div>

<script>
Vue.component('blog-post', {
  props: ['post'],
  template: '\
    <div class="blog-post">\
      <h3>{{ post.title }}</h3>\
      <button v-on:click="$emit(\'enlarge-text\')">\
        Enlarge text\
      </button>\
      <div v-html="post.content"></div>\
    </div>\
  '
})
new Vue({
  el: '#blog-posts-events-demo',
  data: {
    posts: [
      { id: 1, title: 'My journey with Vue', content: '...content...' },
      { id: 2, title: 'Blogging with Vue', content: '...content...' },
      { id: 3, title: 'Why Vue is so fun', content: '...content...' }
    ],
    postFontSize: 1
  }
})
</script>
```

$emit，事件抛出一个值
用$event访问这个值
```
<button v-on:click="$emit('enlarge-text', 0.1)">
  Enlarge text
</button>

<blog-post
  ...
  v-on:enlarge-text="postFontSize += $event"
></blog-post>
```
或者使用函数处理
```
<blog-post
  ...
  v-on:enlarge-text="onEnlargeText"
></blog-post>

//那么这个值将会作为第一个参数传入这个方法
methods: {
  onEnlargeText: function (enlargeAmount) {
    this.postFontSize += enlargeAmount
  }
}
```

## input事件组件
```
Vue.component('custom-input', {
  props: ['value'],
  template: `
    <input
      v-bind:value="value"
      v-on:input="$emit('input', $event.target.value)"
    >
  `
})

<custom-input v-model="searchText"></custom-input>
```

## slot插槽
插槽可以保留 your profile 的值
```
它允许你像这样合成组件：
<navigation-link url="/profile">
  Your Profile
</navigation-link>

然后你在 <navigation-link> 的模板中可能会写为：
<a
  v-bind:href="url"
  class="nav-link"
>
  <slot></slot>
</a>

当组件渲染的时候，<slot></slot> 将会被替换为“Your Profile”。
```

## 动态组件
可以通过 Vue 的 component 元素加一个特殊的 is 特性来实现
```
<div id="dynamic-component-demo" class="demo">
  <button v-for="tab in tabs" v-bind:key="tab" class="dynamic-component-demo-tab-button" v-bind:class="{ 'dynamic-component-demo-tab-button-active': tab === currentTab }" v-on:click="currentTab = tab">
    {{ tab }}
  </button>
  <component v-bind:is="currentTabComponent" class="dynamic-component-demo-tab"></component>
</div>

<script>
Vue.component('tab-home', { template: '<div>Home component</div>' })
Vue.component('tab-posts', { template: '<div>Posts component</div>' })
Vue.component('tab-archive', { template: '<div>Archive component</div>' })
new Vue({
  el: '#dynamic-component-demo',
  data: {
    currentTab: 'Home',
    tabs: ['Home', 'Posts', 'Archive']
  },
  computed: {
    currentTabComponent: function () {
      return 'tab-' + this.currentTab.toLowerCase()
    }
  }
})
</script>
```
# VUE 事件处理
## 监听事件 v-on:click
```
<button v-on:click='method1'>
<button v-on:click='say('hi')'>
```
## 特殊变量 $event
```
<button v-on:click='method2('hi', $event)'>
method2: function (message, event){
    if(event)
        //可以访问原生的事件对象
        event.preventDefault()
}

event.preventDefault() 与 event.stopPropagation()
```
## 事件修饰符
.stop
.prevent
.capture
.self
.once
.passive

## 按键事件 keyup keydown keypress
使用keycode或者以下
.enter
.tab
.delete
.esc
.space
.up
.down
.left
.right
.ctrl
.alt
.shift
.meta 对应windows键
```
<input type="submit" value="submit" v-on:keyup.up="onSubmit"/>

// alt + C
<input @keyup.alt.67="clear">

// click + ctrl
<input @click.ctrl="dosome">
```

# VUEX
Vuex的作用 ，就在于管理一个应用的状态树。

在Vue实例的created钩子中，应用启动了一个定时器，用来周期性地 递增counter属性的值 —— 由于counter是响应式属性，它的变化因而 驱动了视图随之刷新。

可以说counter抽象地表达了计数器视图的本质特征，当counter的 值确定时，我们可以确定地推理出视图的表现。像counter这样可以决定 视图表现的数据，在Vuex中就被称为状态。

## 状态树

### 创建状态库
Vuex的Store类 —— 状态库 —— 用于管理状态树，它的实例化配置项state 用来声明要创建的状态树。例如，下面的代码创建了一个包含状态counter的 状态库：
```
const store = new Vuex.Store({
  state:{ counter:0 }
})
```
利用状态库的state属性，就可以访问到其管理的状态树了。例如，通过 store.state.counter来访问counter状态。

需要指出的是，状态库的state属性 —— 状态树 —— 是一个响应式属性，因此 我们可以使用状态树上的这些状态来驱动视图的自动更新。

### 使用计算属性访问状态树
在建立了全应用单一状态树之后，接下来我们要考虑的就是在组件中怎么使用 树上的状态了 —— 我们已经决定不声明组件的私有状态。

最简单的方法是将树上的状态，映射为组件的计算属性。例如，下面的代码将 状态树上的counter状态，映射为组件的可读写计算属性：
```
const EzCounter = {
  template:'<div class="counter">{{counter}}</div>',
  computed:{
    counter:{
      get() { return store.state.counter },
      set(v){ store.state.counter = v }
    }
  }
}
```

### 将状态库注入组件

另一种方法是将状态库挂接为Vue实例的一个属性上，这样我们就可以在模板中 直接访问状态树了（模板的上下文对象是所属的Vue实例）。

在创建Vue实例时，使用store配置项，就可以将状态库挂接为Vue实例 的属性$store，而且这个Vue实例的所有后代实例，也都有$store指向 同一个状态库，看起来就像是将状态库注入了组件树上的每一个实例。

例如，下面的代码使用store配置项，将状态库store注入根组件，因此 我们可以在EzCounter组件中利用$store属性访问状态库：
```
const EzCounter = { 
  template:'<div class="counter">{{$store.state.counter}}</div>'
}

new Vue({
  store:store,
  template:'<ez-counter/>',
  components:{EzCounter}
})
```

# 组件 component 高级
## 组件注册--全局注册
```
// 注册
Vue.component('my-component', {
template: '<div>A custom component!</div>'
})
// 创建根实例
new Vue({
el: '#example'
})
```
全局注册：在所有子组件中也是如此，也就是说这三个组件在各自内部也都可以相互使用。
```
Vue.component('component-a', { /* ... */ })
Vue.component('component-b', { /* ... */ })
Vue.component('component-c', { /* ... */ })

new Vue({ el: '#app' })

<div id="app">
  <component-a></component-a>
  <component-b></component-b>
  <component-c></component-c>
</div>
```

## 组件局部注册 components
用script变量
```
var Child = {
template: '<div>A custom component!</div>'
}
new Vue({
// ...
components: {
// <my-component> 将只在父模板可用
'my-component': Child
}
})
```
局部注册的组件在其子组件中不可用。例如，如果你希望 ComponentA 在 ComponentB 中可用，则你需要这样写：
```
var ComponentA = { /* ... */ }

var ComponentB = {
  components: {
    'component-a': ComponentA
  },
  // ...
}
```

webpack 实际应用：
```
import ComponentA from './ComponentA.vue'

export default {
  components: {
    // ComponentA 即是元素名也是变量名
    ComponentA
  },
  // ...

// 子组件中这样
<script>
	export default {
		name: 'ComponentA',
		props: {
			msg: String
		}
	}
</script>
}
```

## 模块系统中局部注册
.vue 或者 .js 可以省略
```
import ComponentA from './ComponentA'
import ComponentC from './ComponentC'

export default {
  components: {
    ComponentA,
    ComponentC
  },
  // ...
}
```

## 基础组件的自动化全局注册 webpack require.context
可能你的许多组件只是包裹了一个输入框或按钮之类的元素，是相对通用的。我们有时候会把它们称为基础组件，它们会在各个组件中被频繁的用到。

所以会导致很多组件里都会有一个包含基础组件的长列表：
```
import BaseButton from './BaseButton.vue'
import BaseIcon from './BaseIcon.vue'
import BaseInput from './BaseInput.vue'

export default {
  components: {
    BaseButton,
    BaseIcon,
    BaseInput
  }
}
而只是用于模板中的一小部分：

<BaseInput
  v-model="searchText"
  @keydown.enter="search"
/>
<BaseButton @click="search">
  <BaseIcon name="search"/>
</BaseButton>
```
幸好如果你使用了 webpack (或在内部使用了 webpack 的 Vue CLI 3+)，那么就可以使用 require.context 只全局注册这些非常通用的基础组件。这里有一份可以让你在应用入口文件 (比如 src/main.js) 中全局导入基础组件的示例代码：
```
import Vue from 'vue'
import upperFirst from 'lodash/upperFirst'
import camelCase from 'lodash/camelCase'

const requireComponent = require.context(
  // 其组件目录的相对路径
  './components',
  // 是否查询其子目录
  false,
  // 匹配基础组件文件名的正则表达式
  /Base[A-Z]\w+\.(vue|js)$/
)

requireComponent.keys().forEach(fileName => {
  // 获取组件配置
  const componentConfig = requireComponent(fileName)

  // 获取组件的 PascalCase 命名
  const componentName = upperFirst(
    camelCase(
      // 获取和目录深度无关的文件名
      fileName
        .split('/')
        .pop()
        .replace(/\.\w+$/, '')
    )
  )

  // 全局注册组件
  Vue.component(
    componentName,
    // 如果这个组件选项是通过 `export default` 导出的，
    // 那么就会优先使用 `.default`，
    // 否则回退到使用模块的根。
    componentConfig.default || componentConfig
  )
})
```
记住全局注册的行为必须在根 Vue 实例 (通过 new Vue) 创建之前发生。

## is，防止组件渲染错误
```
<template is='example1'>
```

## 组件 data 用法
data必须是函数
```
<div id="app">
    <com1></com1>
    <com1></com1>
    <com1></com1>
</div>

<script type="text/javascript">
    Vue.component('com1', {
        template: '<button v-on:click="counter += 1">{{name}} - {{age}} - {{ counter }}</button>',
        data:function(){
            return {
                name: "tom",
                age: 18,
                counter: 1
            }
        }
    })
    
    var vm = new Vue({
        el:'#app'
    })
</script>
```

## props传递数据
prop 是父组件用来传递数据的一个自定义属性。子组件需要显式地用 props 选项 声明 “prop”：
其中，message可以用v-bind绑定，也可以在组件应用上赋值。
```
<div id="app">
    <com1 :message="message"></com1>
    <com1 message="alice"></com1>
    <com1></com1>
</div>

<script type="text/javascript">
    Vue.component('com1', {
        template: '<button v-on:click="counter += 1">{{name}} - {{age}} - {{ counter }}: {{message}}</button>',
        data:function(){
            return {
                name: "tom",
                age: 18,
                counter: 1
            }
        },
        props: ['message']
    })
    
    var vm = new Vue({
        el:'#app',
        data:{
            message: "hello vue 10"
        }
    })
</script>
```

prop 是一个对象而不是字符串数组时，它包含验证要求:
type 可以是下面原生构造器： 
• String 
• Number 
• Boolean 
• Function 
• Object 
• Array 
```
Vue.component('example', {
props: {
// 基础类型检测 （`null` 意思是任何类型都可以） 
propA: Number,
// 多种类型 
propB: [String, Number],
// 必传且是字符串 
propC: {
type: String,
required: true 
},
// 数字，有默认值 
propD: {
type: Number,
default: 100 
},
// 数组／对象的默认值应当由一个工厂函数返回 
propE: {
type: Object,
default: function () {
return { message: 'hello' }
}
},
// 自定义验证函数 
propF: {
validator: function (value) {
return value > 10 
}
}
}
})
```

## 组件使用 v-on 绑定自定义事件 

每个 Vue 实例都实现了事件接口(Events interface)，即： 
• 使用 $on(eventName) 监听事件 
• 使用 $emit(eventName) 触发事件 
v-on:increment="incrementTotal"，自定义事件
```
<div id="counter-event-example"> 
    <p>{{ total }}</p> 
    <button-counter v-on:increment="incrementTotal"></button-counter> 
    <button-counter v-on:increment="incrementTotal"></button-counter> 
</div> 

<script type="text/javascript">
    Vue.component('button-counter', {
        template: '<button v-on:click="increment">{{ counter }}</button>',
        data: function () {
            return {
            counter: 0
            }
        },
        methods: {
            increment: function () {
                this.counter += 1
                this.$emit('increment')
            }
        },
    })
    new Vue({
        el: '#counter-event-example',
        data: {
            total: 0
        },
        methods: {
            incrementTotal: function () {
                this.total += 1
            }
        }
    })

</script>
```

## v-model双向绑定 与 组件中实现双向绑定
所以要让组件的 v-model 生效，它必须： 
 * 接受一个 value 属性 
 * 在有新的 value 时触发 input 事件 

```
//from 双向绑定
<input type="text" id="" v-model="message"/>
<h1>{{message}}</h1>

//组件中双向绑定
<div id="app">
    <input type="text" id="" v-model="message"/>
    <h1>{{message}}</h1>
    
    <v-model-example v-model="message"></v-model-example>
</div>

<script type="text/javascript">
    Vue.component('v-model-example',{
        template: '<input type="text" id="" v-bind:value="value" v-on:input="onInput" />',
        props: ['value'],
        methods:{
            onInput: function(event){
                this.$emit('input', event.target.value)
            }
        }
    })
    
    var vm = new Vue({
        data:{
            message: "hello vue"
        },
        el: '#app'
    })
</script>
```

## native属性
监听组件根元素的原生事件，native修饰click不生效
对组件开发来说，只有组件内的内容可用，所以组件内需要用到的内容全部在组件内申明，方法/事件用v-on监听，值用props传递。
this.$emit('component_method')，这个是强行调用事件component_method，而事件监听component_method又被绑定到method1，所以等同于调起vm.method1
```
<div id="app">
    <input type="button" id="" @click.native="method1"/>
    <component is="example" v-on:component_method="method1"></component>
</div>

Vue.component('example',{
    template: '<input type="button" id="" @click="component_method"/>',
    methods:{
        component_method: function(){
            alert("hello component")
            this.$emit('component_method')
        }
    }
})

var vm = new Vue({
    data:{
        message: "hello vue"
    },
    el: '#app',
    methods:{
        method1: function(){
            alert("hello vue")
        }
    }
})
```

event.target
事件属性，返回当前元素、上下文。

## 还可以用Math库
```
    data:{
        message: "mess" + Math.random()
    }
```
## 非父子组件通信 
有时候非父子关系的组件也需要通信。在简单的场景下，使用一个空的 Vue 实例作为中央事件总线
```
var bus = new Vue()

// 触发组件 A 中的事件 
bus.$emit('id-selected', 1)

// 在组件 B 创建的钩子中监听事件 
bus.$on('id-selected', function (id) {
// ... 
})
```

## 父子组件通信
props,$ref,$emit
基本框架
```
//父组件调用子组件
<template>
    <h1>父组件</h1>
    <child></child>
</template>

<script>
    //注册
    import child from 'child.vue'
    export default {
        components: {child}
    }
<script>

//子组件
<template>
    <h1>子组件</h1>
</template>
```
props
```

```

$ref
可以使用 ref 为子组件指定一个索引 ID 。例如： 
```
<div id="parent"> 
<user-profile ref="profile"></user-profile> 
</div>

var parent = new Vue({ el: '#parent' })
// 访问子组件 
var child = parent.$refs.profile
```

## 异步组件
在大型应用中，我们可能需要将应用拆分为多个小模块，按需从服务器下载。为了让事情更简单， Vue.js 允许将组件定义为一个工厂函数，动态地解析组件的定义。Vue.js 只在组件需要渲染时触发工厂函数，并且把结果缓存起来，用于后面的再次渲染。例如： 
```
Vue.component('async-example', function (resolve, reject) {
setTimeout(function () {
resolve({
template: '<div>I am async!</div>' 
})
}, 1000)
})
```
工厂函数接收一个 resolve 回调，在收到从服务器下载的组件定义时调用。也可以调用 reject(reason) 指示加载失败。这里 setTimeout 只是为了演示。怎么获取组件完全由你决定。推荐配合使用 ：Webpack 的代码分割功能： 
```
Vue.component('async-webpack-example', function (resolve) {
// 这个特殊的 require 语法告诉 webpack 
// 自动将编译后的代码分割成不同的块， 
// 这些块将通过 Ajax 请求自动下载。 
require(['./my-async-component'], resolve)
})
```
你可以使用 Webpack 2 + ES2015 的语法返回一个 Promise resolve 函数： 
```
Vue.component(
'async-webpack-example',
() => System.import('./my-async-component')
)
```

## 递归组件 

组件在它的模板内可以递归地调用自己，不过，只有当它有 name 选项时才可以： 
```
name: 'unique-name-of-my-component'

When you register a component globally using Vue.component, the global ID is automatically set as the component’s name option. 

Vue.component('unique-name-of-my-component', {
// ... 
})

If you’re not careful, recursive components can also lead to infinite loops: 

name: 'stack-overflow',
template: '<div><stack-overflow></stack-overflow></div>' 
```
上面组件会导致一个错误 “max stack size exceeded” ，所以要确保递归调用有终止条件 (比如递归调用时使用 v-if 并让他最终返回 false )。 

## 内联模版 inline-template

如果子组件有 inline-template 特性，组件将把它的内容当作它的模板，而不是把它当作分发内容。这让模板更灵活。 
```
<my-component inline-template> 
<div> 
<p>These are compiled as the component's own template.</p> 
<p>Not parent's transclusion content.</p> 
</div> 
</my-component> 
```
但是 inline-template 让模板的作用域难以理解。最佳实践是使用 template 选项在组件内定义模板或者在 .vue 文件中使用 template 元素。 

## v-once 渲染缓存
使用 v-once 的低级静态组件(Cheap Static Component) 

尽管在 Vue 中渲染 HTML 很快，不过当组件中包含大量静态内容时，可以考虑使用 v-once 将渲染结果缓存起来，就像这样： 
```
Vue.component('terms-of-service', {
template: '\ 
<div v-once>\
<h1>Terms of Service</h1>\
... a lot of static content ...\
</div>\
'
})
```

## 组件注册与动态组件 is特性
components 与 component元素

多个组件可以使用同一个挂载点，然后动态地在它们之间切换。使用保留的 component 元素，动态地绑定到它的 is 特性
```
var vm = new Vue({
el: '#example',
data: {
currentView: 'home' 
},
components: {
home: { /* ... */ },
posts: { /* ... */ },
archive: { /* ... */ }
}
})

<component v-bind:is="currentView"> 
<!-- 组件在 vm.currentview 变化时改变！ --> 
</component> 


也可以直接绑定到组件对象上： 
var Home = {
template: '<p>Welcome home!</p>' 
}
var vm = new Vue({
el: '#example',
data: {
currentView: Home
}
})
```

## 动态组件 与 keep-alive 

如果把切换出去的组件保留在内存中，可以保留它的状态或避免重新渲染。为此可以添加一个 keep-alive 指令参数： 
```
<keep-alive> 
<component :is="currentView"> 
<!-- 非活动组件将被缓存！ --> 
</component> 
</keep-alive> 
```
