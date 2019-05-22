---
title: web侠客行（七）--Vuex教程
date: 2000-04-13 07:02:12
tags: 
    - vue
    - app
    - vuex
categories: 
    - 教程
    - web
---

# 状态管理模型概念
先对照vue看个示例
```
new Vue({
  // state (状态)
  data () {
    return {
      count: 0
    }
  },
  // view (视图)
  template: `
    <div>{{ count }}</div>
  `,
  // actions (动作)
  methods: {
    increment () {
      this.count++
    }
  }
})
```
vuex有三个部分，state状态，也就是数据；view视图，也就是模板；actions动作，也就是方法methods。
这是一个单项数据流思想。可以根据用户输入触发动作actions，改变状态state，最终显示在视图view中。

有多组件共享公共状态时，简单快速地分解如下： 
* 多视图可能依赖于同一份状态。 
* 来自不同视图的动作可能需要变更同一份状态。 

所以，有了vuex状态管理模型，通过 Actions(commit) -> Mutations(Mutate) -> State(Render) -> Vue Components(Dispath) -> Actions。
其中Mutations可以使用Devtools。

# Vuex 安装
下载链接：https://unpkg.com/vuex 

Unpkg.com 提供基于 NPM 的 CDN 链接。以上链接总是保持在 NPM 上的最新版本。你也可以通过类似于这样的 URL https://unpkg.com/vuex@2.0.0 使用特殊的 版本/标签。 

在包含 Vue 之后包含 vuex，它会自动安装： 
```
<script src="/path/to/vue.js"></script>
<script src="/path/to/vuex.js"></script>
```
##NPM 
```
npm install vuex
```

当配合模块系统一起使用时，你必须通过 Vue.use() 显式安装到路由上： 
```
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
```
当你使用全局脚本标签时不需要这样做。 

## 使用开发版 

你如果想使用最新的开发版，请直接去 GitHub 克隆，然后构建你自己的 vuex。 
```
git clone https://github.com/vuejs/vuex.git node_modules/vuex
cd node_modules/vuex
npm install
npm run build
```

# Vuex 起步
所有 Vuex 应用的中心就是 store（状态存储）。

”store” 本质上是一个保存应用状态的容器。这里有两件要点，让 Vuex store 区别于普通全局对象： 
* Vuex store 是响应式的。当 Vue 组件从 store 读取状态，如果 store 中的状态更新，它们也会高效地响应更新。 
* 你不能直接更改 store 中的状态。更改状态的唯一的方法就是显式 提交更改 (committing mutations)。这样可以确保每次状态更改都留有可追踪的记录，并且可以使用工具帮助我们更好地理解我们的应用。 

## 最简单的 Store 
安装 Vuex 之后，我们来创建一个 store。这是一个非常易懂的例子 - 只含有一个初始化状态对象，以及一些更改： 
```
// 如果使用模块系统，确保之前调用过 Vue.use(Vuex)
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  }
})

// 可以通过 store.state 访问状态对象，以及通过 store.commit 触发状态的更改： 
store.commit('increment')
console.log(store.state.count) // -> 1
```
示例代码：
addCount - 实现绑定click事件，实现点击次数

```
<div id="app">
  <input type="button" id="" value="btn" @click="addCount" />
  <h1>hello 你点击了 {{message}} 次 </h1>

</div>

<script type="text/javascript">
  var store = new Vuex.Store({
    state: {
      count: 0
    },
    mutations:{
      increment(state){
        state.count ++
      }
    }
  })
    
  const app = new Vue({
    el: '#app',
    data: {
      message: 0
    },
    methods: {
      addCount: function(){
        store.commit('increment')
        console.log(store.state.count)
        this.message = store.state.count
      }
    }
  })			 
</script>
```
 
# Vuex state
## 单一状态树 
Vuex 使用 单一状态树 - 是的，用一个对象就包含了全部的应用层级状态，然后作为一个『唯一数据源(SSOT)』而存在。这也意味着，每一个应用将只有一个 store 实例。单一状态树让我们能够直接地定位任一特定的状态片段，在调试的过程中也能轻易地取得整个当前应用状态的快照。 

## 在 Vue 组件中用计算属性 computed 获得 Vuex 状态 
```
const Counter = {
  template: '<div>{{ count }}</div>',
  computed: {
    count () {
      return this.$store.state.count
    }
  }
}
```
## 通过根Vue实例注册store到子组件中去
通过在根实例中注册 store 选项，该 store 实例会被注入到根组件下的所有子组件中，并且子组件可以通过 this.$store 来访问。
```
const app = new Vue({
  el: '#app',
  // 使用 "store" 选项后，可以注册 store 对象。将会把 store 实例注入到所有子组件。
  store,
  components: { Counter },
  template: '<div class="app">  <counter></counter>  </div>'
})
```
使用store对象示例
```
<div id="app">
  <input type="button" id="" value="btn" @click="addCount" />
  <Counter></Counter>
</div>

<script type="text/javascript">
  // vuex-store对象
  var store = new Vuex.Store({
    state: {
      count: 0
    },
    mutations: {
      increment(state) {
        state.count++
      }
    }
  })
  // 组件中的计算属性返回store对象的值，并写入模板
  const Counter = {
    template: '<div>{{ count }}</div>',
    computed: {
      count() {
        return this.$store.state.count
      }
    }
  }
  // vue实例，注册组件Count显示store对象的状态state，并实现按钮功能
  const app = new Vue({
    el: '#app',
    store,
    components: {
      Counter
    },
    methods: {
      addCount: function() {
        store.commit('increment')
      }
    }
  })
</script>
```

## mapState 工具 
当一个组件需要引用 store 的多个 state 属性或 getter 函数时，声明列举出所有计算属性会变得重复且繁琐。为了解决这个问题，我们可以使用 mapState 工具，它为我们生成 computed 所需的很多个 getter 函数

单个文件使用Vuex.mapState
```
// vuex 提供了独立的构建工具函数 Vuex.mapState
import { mapState } from 'vuex'
export default {
  // ...
  computed: mapState({
    // 箭头函数可以让代码非常简洁
    count: state => state.count,
    // 传入字符串 'count' 等同于 `state => state.count`
    countAlias: 'count',
    // 想访问局部状态，就必须借助于一个普通函数，函数中使用 `this` 获取局部状态
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}

//当计算属性名称和状态子树名称对应相同时，我们可以向 mapState 工具函数传入一个字符串数组。 
computed: mapState([
  // 映射 state.count 到 store.this.count
  'count'
])
```

## 对象扩展运算符 
注意，mapState 返回一个对象。我们如何使用 mapState 合并其他局部的计算属性呢？通常地，为了将多个对象合并为一个对象，再把这个合并好的最终对象传入到 computed 属性去，我们不得不使用一个工具函数来实现。然而有了对象扩展运算符（ECMAScript 提案 stage-3），我们可以大大简化语法： 
```
computed: {
  localComputed () { /* ... */ },
  // 使用对象扩展运算符，将 mapState 返回的对象和外层其他计算属性混合起来
  ...mapState({
    // ...
  })
}
```

# Vuex Getters
Vuex 允许我们在 store 定义 “getters” （将它们视为 store 的计算属性）。getter 函数将接收 state 作为第一个参数：
## getter示例
```
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```

## 接收其他 getter 作为第二个参数
```
getters: {
  // ...
  doneTodosCount: (state, getters) => {
    return getters.doneTodos.length
  }
}
```

## 在组件中使用 getter 
```
computed: {
  doneTodosCount () {
    return this.$store.getters.doneTodosCount
  }
}
```

## 完整示例
```
<div id="app">
  <Counter></Counter>
</div>

<script type="text/javascript">
  // vuex-store对象
  var store = new Vuex.Store({
    state: {
      todos: [{
          id: 1,
          text: 'hello',
          done: true
        },
        {
          id: 2,
          text: 'world',
          done: true
        },
        {
          id: 3,
          text: '这个不显示',
          done: false
        },
      ]
    },
    getters: {
      // 取state.todos数组的每个元素，用filter过滤调todo.done为false的元素
      doneTodos: state => {
        return state.todos.filter(todo => todo.done)
      },
      //计算数组元素个数
      doneTodosCount: (state, getters) => {
        return getters.doneTodos.length
      }
    },
  })
  // 组件中的计算属性返回store对象的值，并写入模板
  const Counter = {
    template: '<div><p>总数: {{ count }}</p><p>对象打印: {{getobjs}}</p> </div>',
    computed: {
      count() {
        return this.$store.getters.doneTodosCount
      },
      getobjs(){
        return this.$store.getters.doneTodos
      }
    }

  }
  // vue实例，注册组件Count显示store对象的状态state，并实现按钮功能
  const app = new Vue({
    el: '#app',
    store,
    components: {
      Counter
    },
  })
</script>
```

# Vuex Mutations
在 Vuex store 中，实际改变 状态(state) 的唯一方式是通过 提交(commit) 一个 mutation。 Vuex 的 mutation 和事件系统非常相似：每个 mutation 都有一个字符串 类型(type) 和 一个 回调函数(handler)。

回调函数是我们执行实际修改状态的地方，它将接收 状态(state) 作为第一个参数。 
```
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 改变 state
      state.count++
    }
  }
})

...
store.commit('increment')
```

## Commit 传入 对象（payload）
向 store.commit 传递一个额外的参数，这个参数被称为 payload ： 
```
// ...
mutations: {
  increment (state, n) {
    state.count += n
  }
}

store.commit('increment', 10)
```

多数情况下，payload 应该是一个对象，以便它可以包含多个字段，这样 mutation 记录中有了 payload 字段名，可描述性会变得更好。 
```
// ...
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}

store.commit('increment', {
  amount: 10
})
```

## 对象风格的 Commit 
提交 mutation 的另一种替代方式，是直接使用具有 type 属性的对象： 
```
store.commit({
  type: 'increment',
  amount: 10
})
```

当使用对象风格的 commit，整个对象都会被作为 payload 参数传入到对应类型的 mutation 的回调函数中，不过回调函数还保持不变： 
```
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
```

## Commit 第三个参数 静默 silent
默认情况下，每个提交过的 mutation 都会被发送到插件（如 devtools）。然而在某些情况下，你可能不希望插件去记录每个状态更改。像是在短时间多次提交到 store 或轮询，并不总是需要跟踪。在这种情况下你可以在 store.commit 中传入第三个参数，来指定插件中的 mutation 是否“静默”。 

第三个参数是一个对象 { silent: true }
```
store.commit('increment', {
  amount: 1
}, { silent: true })
// 使用对象风格的 dispatch
store.commit({
  type: 'increment',
  amount: 1
}, { silent: true })
```

## 新增状态与更新状态
```
新增状态：
Vue.set(this.$store.state, 'testval', '获得testval')

更新状态：
Vue.set(this.$store.state, 'testval', '12345')
```

## 新增状态示例
```
<div id="app">
  <input type="button" id="" value="btn" @click="addVal" />
  <Counter></Counter>
</div>

<script type="text/javascript">
  // vuex-store对象
  var store = new Vuex.Store({
    state: {
      todos: [{
          id: 1,
          text: 'hello',
          done: true
        },
        {
          id: 2,
          text: 'world',
          done: true
        },
        {
          id: 3,
          text: '这个不显示',
          done: false
        },
      ]
    },
    getters: {
      // 取state.todos数组的每个元素，用filter过滤调todo.done为false的元素
      doneTodos: state => {
        return state.todos.filter(todo => todo.done)
      },
      //计算数组元素个数
      doneTodosCount: (state, getters) => {
        return getters.doneTodos.length
      }
    },
  })
  // 组件中的计算属性返回store对象的值，并写入模板
  const Counter = {
    template: '<div><p>总数: {{ count }}</p><p>对象打印: {{getobjs}}</p> <p>test:{{testval}}</p></div>',
    computed: {
      count() {
        return this.$store.getters.doneTodosCount
      },
      getobjs() {
        return this.$store.getters.doneTodos
      },
      testval(){
        return this.$store.state.testval
      }
    }

  }
  // vue实例，注册组件Count显示store对象的状态state，并实现按钮功能
  const app = new Vue({
    el: '#app',
    store,
    components: {
      Counter
    },
    methods: {
      addVal: function() {
        // 新增状态
        Vue.set(this.$store.state, 'testval', '获得testval')
        // 更新状态
        Vue.set(this.$store.state, 'testval', '12345')
      }
    }
  })
</script>
```