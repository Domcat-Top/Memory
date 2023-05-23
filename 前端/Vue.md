## Vue

### 1. 简介

#### 1.1 `render`函数的作用：

- 个人认为是用来创建具体的Dom的，那当然也就可以创建组件了

#### 1.2 脚手架默认配置

- 写在`vue.config.js`文件下，需要用官方规定的特定语法，如果没有自动创建的话，则手动创建该文件

#### 1.3 深度监视

- ```vue
  watch() {
  	params: {
  		deep: true,
  		handler(newValue, oldValue) {
  
  		}
  	}
  }
  ```

### 2. Vue详解

#### 2.1 ref 属性

- 给`Dom`添加 ref 属性，可以获取到相应`Dom`的具体元素，比如`h1`标签等等

  ```vue
  // 获取到 h1 标签 的dom对象
  this.$refs.h1
  ```

- 给组件添加`ref`属性，即可获取到具体的组件的`Dom`元素（相比于JS的id属性，获取到的则是组件的具体内容，比如div块里面的）

#### 2.2 props 属性

- 解释：组件之间进行数据的交互：**接收到的props是==不允许==进行修改的**

  ```vue
  <!-- 发送方 -->
  <School name="张三" :age=“19”></School>
  
  <!-- 接收方 -->
  data() {
  	return {
  		
  	}
  },
  // 和 data 标签 同级的
  props: ['name', 'age'] // 简单接收
  
  // 接收的同时，对数据的类型进行限制
  props: {
  	name: String,
  	age: number,
  }
  
  // 接收的同时对数据进行类型的限制和默认值的制定和必要性的限制
  props: {
  	name: {
  		type: String,
  		required: true,
  	},
  	age: {
  		type: number,
  		default: 99, // 可以设置默认值
  	}
  }
  ```

#### 2.3 `mixin`混合

- 如果多个组件有着相同的配置，比如相同的data，相同的methods，那么可以进行混合

  ```vue
  export const minix = {
  	data() {
  		return {
  			name: "张三",
  			age: 18,
  		}
  	},
  	methods: {
  		console.log(this.name);	
  	}
  } 
  
  // 使用时
  import {mixin} from '../mixin';
  
  mixins: [mixin],
  ```

- 如果混合属性、方法 和 组件的 属性、方法 产生冲突，以自己的为主
- 但是如果 混合的方法 和 自身组件的方法 含有生命周期方法，则混合的 和 自身的 都会执行
  - 先执行混合的生命周期方法，再执行自身组件的

#### 2.4 插件

- 定义：

  ```vue
  export default {
  	install(Vue) {
  		// 这里可以设置一些插件方法
  		// 比如设置全局过滤器、定义全局指令、定义混入、给Vue原型添加方法等
  	}
  }
  
  <!-- 使用方式 -->
  <!-- 全局使用 -->
  import plugins from './plugins';
  Vue.use(plugins);
  ```

#### 2.5 `scoped`属性

- 组件style标签的样式最后会汇总到一起，如果不限定范围的话可能会导致样式的混乱
- scoped属性：style标签添加了scoped属性表示这些样式只作用于当前组件

#### 2.6 TodoList总结

- props：
  - 父组件 ==> 子组件（通信）
  - 子组件 ==> 父组件 通信 （要求父先给子一个函数）
- 使用v-model时候需要注意，v-model绑定的值不能是props传过来的，因为props是不允许修改的
- props传过来的若是对象类型的，修改对象中的属性时，Vue不会报错，但是不推荐这样做

#### 2.7 浏览器的本地存储（WebStorage）

- ```html
  // 存储
  windows.localStorage.setItem("key", "value");
  // 读取
  windows.localStorage.getItem("key");
  // 删除
  windows.localStorage.remove("key");
  // 清空
  windows.localStorage.clear();
  ```

- sessionStorage：只存在于一次会话，浏览器关闭就清空

#### 2.8 组件的自定义事件（绑定）

- ```vue
  // 父组件
  <School v-on:atguigu="getStudentName"/>
  
  methods: {
  	getStudentName(name	) {
  		console.log(name);
  	}
  }
  
  <button @click="sendStudentName" />
  // 子组件
  methods: {
  	sendStudentName() {
  		// 视频里面用的单引号
  		this.$emit("atguigu", this.studentName);
  	}
  }
  ```

- 绑定一个自定义事件，实现父子之间的数据交互

- 第二种写法：

  ```vue
  <Student ref="student"/>
  // 使用ref属性，获取到student的dom对象，再获取到on绑定的事件
  // 子组件触发这个事件的时候，就调用父组件的getStudentName方法
  mounted() {
  	// 第二种方式是在这里绑定了atguigu事件
  	this.$ref.student.$on("atguigu", getStudentName);
  }
  
  <button @click="sendStudentName" />
  // 子组件
  methods: {
  	sendStudentName() {
  		// 视频里面用的单引号
  		this.$emit("atguigu", this.studentName);
  	}
  }
  ```

#### 2.9 解绑组件自定义事件

- 子组件进行事件的解绑：

  ```vue
  // 单个
  this.$off("atguigu");
  // 多个
  this.$off(["atguigu", "demo"]);
  // 解绑所有的
  this.$off();
  ```

#### 2.10 全局事件总线

- 实现任意组件之间的通信

- ```vue
  // 注册
  new Vue({
    render: h => h(App),
    beforeCreate() {
      // 注册全局事件总线
      Vue.phototype.$bus = this;
    }
  }).$mount('#app')
  
  // 绑定
  // 接收发送过来的数据
  mounted() {
      this.$bus.$on("sendStudentName", (data)=>{
      	console.log(data);
      });
  },
  // 组件销毁之前需要解绑
  beforeDestroy() {
  	this.$bus.$off("sendStudentName");
  }
  
  // 触发
  // 给兄弟组件发送数据，实现相互组件之间的数据通信
  mounted() {
  	this.$bus.$emit("sendStudentName", this.studentName);
  }
  ```

#### 2.11 消息订阅与发布（实现组件之间的通信）

- 需要第三方库，如：pubsub

- ```vue
  import pubsub from 'pubsub-js'
  
  // 订阅
  methods: {
  	getInfo() {
  		// 一个参数是消息名，一个是具体的数据
  		this.pubId = pubsub.subscribe("hello", function(infoName, data) {
  			console.log("有人发布了hello消息，hello消息的回调执行了", data);
  		});
  	}
  }
  beforeDestroy() {
  	pubsub.unsubscribe(this.pubId);
  }
  
  // 发布
  methods: {
  	sendInfo() {
  		pubsub.publish("hello", data);
  	}
  }
  ```

- 一般不用

#### 2.12 nextTick

- 语法：`this.$nextTick(回调函数)`
- 作用：在下一次DOM更新结束后执行其指定的回调。
- 什么时候调用：当改变数据后，要基于更新后的新DOM进行某些操作时，要在nextTick所指定的回调函数中执行。

#### 2.13 过度与动画

##### 2.13.1 Vue的动画

- ```vue
  // 使用transition标签
  					// 设置初始的动画
  <transition name="tom" appear>
  	
  </transition>
  
  
  <style scoped>
      // 自定义一个动画
      @keyframes test{
          
      }
      // Vue的控制方式
      .tom-enter-active {
          
      }
      .tom-leave-active {
          
      }
  </style>
  ```

##### 2.13.2 第三方动画效果库

- 此处举例：`animate`库

- 代码实现：

  ```vue
  // 下载
  npm install animate.css --save
  
  
  <div>
      <button @click="isShow=!isShow">显示/隐藏</button>
      <transition
        appear
        name="animate__animated animate__bounce"
        enter-active-class="animate__rubberBand"
        leave-active-class="animate__backOutDown"
      >
          <h1 v-show="isShow">汤姆的测试</h1>
      </transition>
    </div>
  
  
  // 当前组件下需要引入这个css
  import 'animate.css'
  ```

##### 2.13.3 总结

- 作用：在插入、更新或移除DOM元素时，在合适的时候给元素添加样式类名

- 写法：

  1. 准备好样式：

     - 元素进入的样式：
       1. v-enter：进入的起点
       2. v-enter-active：进入过程中
       3. v-enter-to：进入的终点
     - 元素离开的样式：
       1. v-leave-active：离开的起点
       2. v-leave-active：离开的终点
       3. v-leave-to：离开的终点

  2. 使用`<transition>`包裹要过度的元素，并且配置name属性

     ```vue
     <transition name="hello">
     	<h1 v-show="isShow">
             小姑娘！
         </h1>
     </transition>
     ```

  3. 备注：若有多个元素需要过渡，则需要使用：`<transition-group>`，且每个元素都要制定`key`值

#### 2.14 代理（解决前端跨域问题）

- 同源策略规定了：协议名、主机名、端口号

- 解决方式：

  - 配置代理服务器：使用Vue-cli

    - 在vue.config.js中写如下配置：

      ```js
      module.exports = {
          // 开启代理服务器
          devServer: {
              // 请求转发到哪，就写哪里的ip和端口
              proxy: "http://localhost:5000"
          }
      }
      ```

    - 如果原生请求的东西，当前服务已经存在，则直接调取本地，不会请求服务器端
    - 弊端：只能配置一个代理 且 不能灵活控制走不走代理

  - 第二种配置方式：可以配置多个

    ```js
    devServer: {
        proxy: {
            // 请求前缀---给请求添加前缀，而不是进行匹配
            "/api": {
                // 目标地址
                target: "localhost:8080",
                // 路径重写
                // 匹配所有路径以tomtest开头的请求，并且替换为 “”------------------这里才进行了匹配
                pathReWrite: {"^/tomTest": ""},
                ws: true, // 用于支持webSocket
                // 用于控制请求头的host值
                // false表示代理向服务端说谎，和服务端说一样的端口，true则不说谎
                changeOrigin: false, 
            }
        }
    }
    ```

  #### 2.15 Vue插件库

  - 安装：`npm i vue-resource `

  - 使用：

    ```js
    import vueResource from "vue-resource";
    // 使用插件
    Vue.use(vueResource);
    ```

  - 插件库在VC和VM身上装了很多的方法，类似Java的工具类，直接调用即可，是对其他库的二次封装

#### 2.16 插槽

##### 2.16.1 默认插槽：

```vue
<Student>
	<h1>
        这是一个插槽
    </h1>
</Student>

// 在student组件中，使用slot标签指定这个样式的放置位置
<slot></slot>
```

##### 2.16.2 具名插槽

- 使用slot属性给插槽起名字，然后进行使用

  ```vue
  <slot name="tom"></slot>
  ```

##### 2.16.3 作用域插槽

- 数据逆向：属性为scope或slot-scope

  ```vue
  // 插槽代码（子）
  <slot :info="info"></slot>
  
  // 插槽样式代码（父）
  // scope属性获取传过来的对象，可以随便起名字
  <template scope="tom">
  	{{tom.info}}
  </template>
  ```

### 3. VueX

#### 3.1 简介

##### 3.1.1 VueX是什么

- 专门在Vue中实现集中式状态（数据）管理的一个Vue插件，对vue应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信。

##### 3.1.2 什么时候使用VueX

1. 多个组件依赖于同一状态
2. 来自不同组件的行为需要变更同一状态

> VueX就是用来存放共享数据的

#### 3.2 具体实现

- 安装：`npm i vuex`

- 注意：Vuex有版本限制，Vue3对应Vuex4，以此类推降级即可

- 代码实现：

  ```vue
  // main.js***
  // 引入store
  import store from './store/index'
  // 在这里记得去引入
  new Vue({
    render: h => h(App),
    store: store,
  }).$mount('#app')
  
  // 实现store***
  import Vue from 'vue'
  // 引入Vuex
  import Vuex from 'vuex'
  // 应用Vuex插件
  Vue.use(Vuex);
  // 准备actions--用于响应组件中的动作
  const actions = {
      // 这个context其实就是上下文，也就是部分的store
      add(context, value) {
          // 传递到mutations
          context.commit("ADD", value);
      },
      // 是奇数才进行操作
      odd(context, value) {
          if(context.state.sum % 2) {
              context.commit("ODD", value);
          }
      },
      wait(context, value) {
          setTimeout(() => {
              context.commit("WAIT", value);
          }, 800);
      }
  }
  
  // 准备mutations--用于操作数据（state）
  const mutations = {
      ADD(state, value) {
          state.sum += value;
      },
      // 加减法没有其他的逻辑，不需要actions进行转达，直接到mutations即可
      // 加法懒得删了 
      // 因为在组件直接写的commit方法，可以直接到达mutations
      JIAN(state, value) {
          state.sum -= value;
      },
      ODD(state, value) {
          state.sum += value;
      },
      WAIT(state, value) {
          state.sum += value;
      }
  }
  // 准备state--用于存储数据
  const state = {
      sum: 0,
  }
  // 创建并暴露store
  export default new Vuex.Store({
      // 也可以写为
      // actions,
      actions: actions,
      mutations: mutations,
      state: state,
  })
  
  // 到具体组件处的使用***
  // 指定vuex的action要调用的方法，并且把参数给它传过去
  this.$store.dispatch("add", this.n);
  this.$store.commit("JIAN", this.n);
  ```

- 此外，还存在`mapGetters、mapActions、mapMutation`用于直接生成属性和方法，也可不用

#### 3.3 Vuex的模块化语法

- 代码示例：

  ```vue
  // 区分多个组件的数据和功能方法等操作
  const countOptions = {
  	namespaced: true,
  	actions: {},
  	mutations: {},
  	state: {},
  	getters: {},
  },
  const personOptions = {
  	namespaced: true,
  	actions: {},
  	mutations: {},
  	state: {},
  	getters: {},
  }
  
  
  export default new Vuex.Store({
  	modules: {
  		countAbout: countOptions,
  		personAbout: personOptions,
  	}
  })
  // 组件方使用时
  computed: {
  	// 直接从countAbout配置中取出这三个参数
  	...mapState('countAbout', ['sum', 'school', 'subject']);
  },
  // 其他的方法等操作都是同理，直接指定配置即可
  methods: {
  	...mapActions('countAbout', {increment: 'JIA', decrement: 'JIA'}),
  	...mapActions(),
  }
  ```

### 4. ==路由==

#### 4.1 简介

- 理解：
  1. 一个路由就是一组映射关系（key-value）
  2. key为路径，value可能是function或component
- 路由分类：
  - **这里只说前端路由**
  - value是component，用于展示页面内容
  - 工作过程：当浏览器的路径改变时，对应的组件就会显示

#### 4.2 基本路由

- 使用步骤：

  1. 安装：`npm i vue-router`，==Vue2对应的是router3，Vue3对应的是router4==

  2. main.js 引入：`import VueRouter from 'vue-router'`，并且使用`Vue.use(VueRouter)`，并且添加到VM，`router: router`

  3. 创建router.js

     ```vue
     import VueRouter from 'vue-router'
     
     export default new VueRouter({
     	routers: [
     		{
     			path: '/about',
     			component: About,
     		},
     		{
     			path: '/home',
     			component: Home,
     		}
     	]
     })
     ```

  4. 使用<router-link></router-link>标签去替换原来html的a标签

#### 4.3 路由的注意点

- **路由组件**通常存放在`pages`文件夹，**一般组件**通常存放在`components`文件夹
- 通过切换，隐藏了路由组件，默认是被销毁掉的，需要的时候再去挂载
- 每个组件都有自己的$route属性，里面存储自己的路由信息
- 整个应用只有一个router，可以通过组件的$router属性获取到

#### 4.4 嵌套路由

- 代码示例：

  ```js
  export default new VueRouter({
      routes: [
          {
              path: '/about',
              component: About,
          },
          {
              path: '/home',
              component:Home,
              children: [
                  {
                      path: 'news',
                  	component: News,
                  },
                  {
                      path: 'message',
                      component:Message,
                  }
              ]
          }
      ]
  })
  ```

#### 4.5 路由传参

- 代码示例：

  ```vue
  <router-link :to={
  	path: '',
      query: {
      	id: m.id,
          title: m.title,
      }
               }>
  
  </router-link>
  ```

#### 4.6 命名路由

- 给路由起名字

- 代码示例：

  ```js
  name: routerName
  
  // 可以用来替换 :to对象的path
  ```

#### 4.7 路由的param传参

- 代码示例：

  ```js
  path: '/test/:id/:title'
  ```

#### 4.8 路由的props属性

- 代码示例：

  ```js
  props: {a: 1, b: "hello"} // 值为对象，该对象中的所有kv都会以props的形式传给组件
  ```

- 代码示例：

  ```js
  props: true, // 值为布尔值，若布尔值为真，就会把该路由组件收到的所有params参数，以props的形式传给组件
  ```

- 代码示例：

  ```js
  // 值为函数，以props的形式传过去
  props($route) {
      return {id: $route.query.id, title: "helloWorld"};
  }
  ```


#### 4.9 router-link的replace属性

- 作用：控制路由跳转时操作浏览器历史记录的模式
- 浏览器的历史记录有两种写入方式，分别为`push`和`replace`，`push`是追加历史纪录,`replace`是替换当前记录。路由跳转时默认为`push`
- router-link标签添加了replace属性即可开启

#### 4.10 编程式路由导航

- 作用：不借助`router-link`实现路由跳转，让路由跳转更加灵活

- ```js
  this.$router.push({
      name: "test",
      params: {
          id: xxx,
          title: xxx,
      }
  }),
      
  this.$router.replace({
      name: "test",
      params: {
          id:xxx,
          title: xxx,
      }
  })
  
  this.$router.forward(); // 前进
  this.$router.back(); // 后退
  this.$router.go(); // 前进或后退几步
  ```

#### 4.11 缓存路由组件

- ```vue
  // 如果有多个，则写为： :include="["组件名", "组件名"]"
  <keep-alive include="组件名">
  </keep-alive>
  ```

- 让不展示的路由组件缓存，不进行销毁

#### 4.12 路由组件独有的生命周期钩子

- ```js
  // 路由组件激活时候触发该函数
  actived（） {
  },
  // 路由组件失活时候触发该函数
  deactived() {
  }
  ```

#### 4.13 路由守卫（权限）

##### 4.13.1 全局前置路由守卫

- ```js
  const router = new VuewRouter({
      routes: [
          {
              name: "test",
              path: "/tom",
              component: Tom,
          }
      ]
  })
  
  // 全局**前置**路由守卫--初始化时候被调用，每次路由切换之前被调用
  // to 表示去哪，from 表示从哪来，next表示是否放行，next是一个函数
  router.beforeEach((to, from, next) => {
      if(to.name === "test" || to.name === "路由的名字") {
          if(判断这两个路由中是否含有相关参数) {
              // 放行
              next();
          } else {
              alert("权限不足，无法跳转");
          }
      } else {
          // 如果是别的路由，则不做判断，直接进行放行
          next();
      }
  })
  
  export default router;
  ```

- 路由配置中含有一个属性：meta，可以在这里配置条件判断的选择

  ```js
  const router = new VuewRouter({
      routes: [
          {
              name: "test",
              path: "/tom",
              component: Tom,
              meta: {
                  isAuth: false
              }
          }
      ]
  })
  
  router.beforeEach((to, from, next) => {
      if(to.meta.isAuth) {
          if(进行权限判断) {
              
          }
      }
  })
  ```


##### 4.13.2 全局的后置路由守卫

- ```js
  const router = new VuewRouter({
      routes: [
          {
              name: "test",
              path: "/tom",
              component: Tom,
              title: {
                  isAuth: false,
                  title: "测试页",
              }
          }
      ]
  })
  
  // 全局的后置路由守卫--初始化的时候被调用、每次路由切换之后被调用
  // 功能：可以切换网页的title之类的
  router.afterEach((to, from) => {
      document.title = to.meta.title || '测试';
  })
  ```

##### 4.13.3 独享路由守卫

- 某个路由独享的，只有**beforeEnter**，没有**afterEnter**

- ```js
  const router = new VuewRouter({
      routes: [
          {
              name: "test",
              path: "/tom",
              component: Tom,
              title: {
                  isAuth: false,
                  title: "测试页",
              },
              // 独享路由守卫只有前置，没有后置
              beforeEnter: (to, from, next) => {
                  if(to.meta.isAuth) {
                      // 权限判断之类的
                  }
              }
          }
      ]
  })

##### 4.13.4 组件内路由守卫

- ```js
  export default {
      name: 'About',
      mounted() {
          
      },
      // 路由进入之前
      // 通过路由规则，进入该组件时被调用
      beforeRouteEnter(to, from, next) {
          
      },
      // 通过路由规则，**离开**该组件时被调用
      beforeRouteLeave(to, from, next) {
          
      }
  }
  ```

##### 4.13.4 路由的工作模式

- hash和history，默认为hash模式
- hash值不会包含在HTTP请求中，即：hash值不会带给服务器
- hash模式：
  1. 地址中永远带#号，不美观
  2. 若以后将地址通过第三方手机App分享，如果App校验严重，则地址会被标记为不合法
  3. 兼容性好
- history模式：
  - 地址干净、美观
  - 兼容性和hash模式相比略差
  - 应用部署上线后需要后端人员的支持，解决刷新页面服务端404问题

### 5. UI组件库

- X
- 轮播图组件：Swiper6

- 图片懒加载：vue-lazyload



### 6. Vue3

#### 6.1 Vue3 初始化

- 版本：4.5以上

- 创建：

  - npm创建方式和vue2一样

  - vite创建：

    - ```shell
      ## 创建工程
      npm init vite-app <projetc-name>
      ## 进入工程目录
      cd <project-name>
      ## 安装依赖
      npm install 
      ## 运行
      npm run dev
      ```

#### 6.2 常用CompositionAPI

##### 6.2.1 setup

- Vue3 的一个新配置项，值为一个函数
- setup是所有CompositionAPI表演的舞台
- 组件中所用到的：数据、方法等，均要配置在setup中
- setup函数的两种返回值：
  - ==若返回一个对象，则对象中的属性、方法，在模板中均可直接使用==
  - 若返回一个渲染函数，则可以自定义渲染内容

##### 6.2.2 ref函数（对象和属性都可以用，但是不推荐修饰对象）

- 作用：定义一个响应式的数据

- 如果属性更改后，想实时的展示到页面上，需要响应式数据

- ```js
  import {ref} from 'vue';
  setup() {
      let name = ref("张三");
      let age = ref(18);
      let job = ref({
          type: "后端",
          salary: "30K",
      });
      
      function changeInfo() {
          name.value = "李四";
          age.value = 30;
          job.value.type = "UI";
          job.value.salary = "60K";
      }
  }
  
  // 模板中读取的时候，不需要写value

##### 6.2.3 reactive 函数（用在对象和数组上）

- 定义一个对象类型的响应式数据

- ```js
  setup() {
      ley job = reactive({
         type: "前端工程师",
         salsry: "30K",
      });
      function changeInfo() {
          job.type = "直接修改即可";
      }
  }
  ```

##### 6.2.4 Vue3的响应式

- 实现原理：

  - 通过Proxy代理：拦截对象中任意属性的变化，包括：属性的读写、属性的添加、属性的删除等

  - 通过Reflect反射：对源对象的属性进行操作

    ```js
    new Proxy(data, {
        // 拦截读取属性值
        get(target, prop) {
            return Reflect.get(target, prop);
        },
        // 拦截设置属性值  或  添加新属性
        set(target, prop, value) {
            return Reflect.deleteProperty(target, prop, value);
        },
        // 拦截删除属性
        deleteproperty(target, prop) {
            return Reflect.deleteProperty(target, prop);
        }
    })
    ```

##### 6.2.5 计算属性

- 和Vue2.X中computed配置功能一致

  ```js
  setup() {
      ...
      let fullName = computed(() => {
          return person.firstName + '-' + person.lastName;
      })
      // 完整写法
      person.fullName = computer({
          get() {
              
          },
          set(value) {
              const nameArr = value.split('-');
              person.firstName = nameArr[0];
              person.lastName = nameArr[1];
          }
      })
  }
  ```

##### 6.2.6 监视

- 和Vue2.X中watch配置功能一致

  ```js
  setup() {
      watch(sum, (newValue, oldValue) => {
          console.log(`变了`);
      }, {immediate: true});
      watch([sum, msg], (oldValue, newValue) => {
          console.log();
      });
      // 监视reactive定义的一个响应式数据，会无法正确获取到oldValue
      watch(person.value, (newValue, oldValue) => {
          console.log(`获取不到oldValue了`);
      })
  }
  ```

- 两个小坑：
  1. 监视reactive定义的响应式数据时，oldValue无法正确获取，强制开启了深度监视（deep配置失效）
  2. 监视reactive定义的响应式数据中某个属性时：deep属性有效

- watchEffect：回调函数中用了哪个属性，就监视哪些属性，并且是可以嵌套的

  ```js
  watchEffect(() => {
      sum.value;
      console.log(`回调`);
  })
  ```

  - **==这个属性就是可以实现前端一直向后端传递数据的行为，只要数据改变了就向后端发送请求，无敌啦！！！==**

##### 6.2.7 自定义hook函数

- 把setup函数中的数据、方法、方法体等进行了封装，封装到一个JS文件里面，需要的地方直接引用，可以得到自己想要的数据，减少了代码的冗余

  ```js
  import {reavtive} from 'vue';
  export default function() {
      let point = reactive({
         x: 0,
         y: 0,
      });
      
      // 下面可以写一些方法，操作这些数据的
      
      
      // 最后吧得到的处理后的数据返回，因为要用
      return point;
  }
  
  
  // 在使用的地方
  import usePoint from '../hook/usePoint'
  
  setup() {
      // 获取到处理逻辑后的数据
      let ponit = usePoint();
  }
  ```

##### 6.2.8 toRef（个人观点是没多大用）

- 作用：创建一个ref对象，其value值指向另一个对象中的某个属性
- 语法：`const name = toRef(person, 'name');`
- 应用：要将响应式对象中的某个数据单独提供给外部使用时
- 拓展：`toRefs`和`toRef`功能一直，但可以批量创建多个ref对象

#### 6.3 其他的CompositionApi（了解）

##### 6.3.1 shallowReactive

- 浅层次的reactive：只处理对象的第一层，第二层都不处理

  ```js
  let person = shallowReactive({
      name: '张三',
      age: 16,
      job: {
          j1: {
              salary: 30
          }
      }
  })
  ```

##### 6.3.2 shallowRef

- 浅层次的ref：如果传入的是基本数据类型，如shallowRef(0)，那么继续生效，如果传入的是shallowRef({y: 0})，那么不会生效了

##### 6.3.3 readonly 和 shallowReadonly

- person = readonly(person);    当前对象的所有属性都不允许改变

- person = shallowReadonly(person); 当前对象的第一层数据不允许更改，其它层的数据是允许更改的

















### 7. 性能优化

#### 7.1 函数的节流和防抖

- 节流：在规定的间隔时间范围内不会重复触发回调，只有大于这个时间间隔才会触发回调，把频繁触发变为少量触发。
- 防抖：前面的所有触发都被取消，最后一次执行在规定的时间之后才会触发，也就是说如果连续快速的触发，只会执行一次。









































