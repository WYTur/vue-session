# Vue2学习总结2

### 基础部分

#### Vue2的常用配置

##### 1.运行

​	npm run serve

##### 2.安装bootstrap

​	cnpm install bootstrap -- save

##### 3.安装axios

​	cnpm install axios

##### 4.Eslint推荐的添加配置

​	'space-before-function-paren': ['warn', 'never']

​	"vue/multi-word-component-names":"off",

##### 5.安装echarts

​	后面有单独一篇讲vue+echarts

#### data

在组件中 data的格式为

```js
	//data数据源
    //注意：组件里的data不能像之前一样指向对象
    //注意：组件中的data必须是函数
    //应该向下一样
    data() {
        //这个return出去的{}中，可以定义数据
        return {
            username: 'zs'
        }
    }
```

#### 组件的其他函数

```js
methods: {
        changeName() {
            this.username = 'hhh'
        }
    },
    //当前组件的侦听器
    watch: {},
    //当前组件中的计算属性
    computed: {},
    //当前组件的过滤器
    filters: {}
```

#### 组件的注册

##### 1.导入需要的vue组件

```js
// 导入需要使用的vue组件
import Left from '@/components/Left.vue'
```

##### 2.注册组件

```js
components: {
    //2注册组件
    // 'Left': Left,
    // 简写
    Left,
    Right,
    Test
  }
```

注册后可以直接使用标签

```vue
<Left></Left>
```



#### props的使用

在传输组件上需要：

```vue
<MyCount :init="9"></MyCount>
```

在接收组件可以直接获取：

```js
//props可以提高组件的复用性
    //props 是自定义属性，为使用者通过自定义属性，为当前组件指定初始值
    //props中的数据，可以直接在模板结构中被使用
    //props 的数据是只读的，所以不能直接把传过来的数据进行修改,下方count可读可写
    // props: ['init'],
    props: {
      //自定义属性A：{配置选项}  
      //自定义属性B：{配置选项}
      init: {
        //如果外界使用Count组件的时候，没有传递init属性，则默认值生效
        default: 0,
        //init的值的类型是数字类型
        type: Number,
        //必填项init，否则报错
        required: true
      }  
```



### 进阶部分

#### 不同组件间传输的方式

- ##### 父向子传值

用props进行传值

父组件：

```vue
  <!-- 父向子传数据 -->
  <Left :message="message" :userInfo="userInfo"></Left>
```

子组件：

```js
props:['message','userInfo']
```

- 子向父传值

定义事件传值

子组件：

```js
add() {
      this.count += 1
      //自定义事件来触发父组件的事件
      this.$emit('numchange',this.count)
    }
```

父组件：

```vue
<Right @numchange="getCount"></Right>
```

- 兄弟之间传值

用bus(eventBus.js)

先定义bus，创建eventBus.js

```js
import Vue from "vue";

export default new Vue()
```

每个兄弟组件都要先导入bus

```js
import bus from './eventBus.js'
```

Left兄弟组件：

```vue
<button @click="send">{{ str }}发送到Right</button>
```

```js
send() {
      bus.$emit('share',this.str)
    }
```

Right兄弟组件:

```js
bus.$on('share',val =>{
      console.log("触发事件")
      this.msgFromLeft = val
    })
```



#### Vue操作DOM

用ref(这里是input)

```vue
<input type="text" v-if="inputVisible" @blur="showButton" ref="input" >
```

在js中(如次可操作Dom)

```js
this.$refs.myh1.style.color = 'red'
```



#### Vue的生命周期



![lifecycle](C:\Users\wty16\Desktop\lifecycle.png)



#### 切换组件

点击切换不同的组件

```vue
<button @click="comName = 'Left'">展示Left</button>
<button @click="comName = 'right'">展示Right</button>

<div class="box">
      <!-- 渲染 Left 组件和 Right 组件 -->
      <!-- <components>切换会创建和销毁 -->
      <!-- is选谁就显示谁 -->
      <!-- 可以动态绑定组件 -->
      <!-- keep-alive可以让内部的组件缓存，而不是销毁组件 -->
      <!-- include 选择缓存具体组件 
            exclude 表示排除 
            两个二选一 -->
      <keep-alive include="Left,Right" >
        <!-- 里面的内容不会被销毁  -->
        <components :is="comName"></components>
      </keep-alive>
      <!-- <Left></Left>
      <Right></Right> -->
    </div>
```

原理：components中让is单向绑定给comName实现切换

这里的keep-alive缓存作用

keep-alive的生命周期

```js
 // 以下为keep-alive的生命周期函数
   activated() {
     console.log("组件被激活了")
   },
   deactivated() {
     console.log("组件被缓存了")
   }
```



#### 自定义指令

- 私有自定义

```js
// 私有自定义指令
  directives: {
    //定义名为color的自定义指令，指向一个配置对象
   color: {
      //指令第一次绑定才会调用bind
      //el是Dom元素
     bind(el,binding) {
       el.style.color = binding.value
     },
      //update更新的时候就会触发update函数，但是第一次没有生效
     update(el,binding) {
       el.style.color = binding.value
     }
  	}

```

动态绑定参数值 binding 是第二个参数

简写模式

```js
    //简写形式
    color (el,binding) {
      el.style.color = binding.value
    }
  }
```

定义后可以使用

js:

```js
data() {
  return {
    color: 'red'
  }
}
```

vue:

```vue
<h1 v-color="color" >App 根组件</h1>
```

- 全局自定义

main.js

```js
//全局自定义指令
 Vue.directive('color',{
   bind(el,binding){
     el.style.color = binding.value
   },
   update(el,binding){
     el.style.color = binding.value
   }
 })
```

简写

```js
//全局自定义指令简写
Vue.directive('color',function(el,binding) {
  el.style.color = binding.value
})
```



#### 插槽

在组件标签中写要插入组件的部分

- 不接受数据

组件代码用slot标签接收:

```vue
<slot name="foot" :user="userinfo"></slot>
```

发送端:

```vue
<div class="box">
	<template #foot>
          <p>这是想在Left组件中foot渲染的p标签和传过来的值</p>
    </template> 
</div>
```



- 接收数据

插槽端:

```vue
<slot name="content" msg="value" :user="userinfo">
      <h6>这是default插槽的默认内容</h6>
</slot>
<slot name="foot" :user="userinfo"></slot>
```

```js
userinfo: {
        name: 'zs',
        age: '20' 
}
```

发送端接收数据:

```vue
<div class="box">
      <!-- 渲染 Left 组件和 Right 组件 -->
      <Left>
        <!-- 默认情况下，在使用组件时，提供的内容都会填充到default的插槽之中 -->
        <!-- 如果要插槽到指定区域 v-slot:只能用在template标签或者components标签
              v-sort: 后面要跟上插槽的名字 
              v-sort: 的简写是#-->
              <!-- 解构赋值 -->
        <template #content="{ msg , user }">
          <p>这是想在Left组件中content渲染的p标签和传过来的值---{{ msg }}</p>
          <p>{{ user.name }}</p>
        </template>
          
         <template #foot="scope">
          <p>这是想在Left组件中foot渲染的p标签和传过来的值---{{ scope.user }}</p>
        </template> 
      </Left>
</div>
```

#### 路由

##### 基本配置和基本使用

路由点击转换

```vue
<router-link to="/about/tab2">tab2</router-link>
```

路由占位符

```vue
<!-- 路由占位符 -->
<router-view></router-view>
```

router的index.js

```js
import Vue from 'vue'
import VueRouter from 'vue-router'

//组件
import Login from '@/components/MyLogin.vue'

Vue.use(VueRouter)

// 创造路由的实例
const router = new VueRouter({
  // 定义hash地址和"组件"之间的对应关系
  routes: [
    // 路由规则
    // redirect当访问时'/',跳转到/home
    {
      path: '/',
      redirect: '/home'
    },
    { path: '/home', component: Home },
    // { path: '/movie', component: Movie },
    // 动态路由
    // 这里：通用
    // 需求：在Movie组件中，根据id找到对应的页面
    // 开启props后可以直接传参
    { path: '/movie/:mid', component: Movie, props: true },
    {
      path: '/about',
      component: About,
      //可以默认重定向来设置默认子路由
      redirect: '/about/tab1',
      children: [
        //可以把path设置为空，那么就是默认子路由
        { path: 'tab1', component: Tab1 },
        { path: 'tab2', component: Tab2 }
      ]
    },
    { path: '/login', component: Login },
    { path: '/main', component: Main }
  ]
})

export default router
```

##### 传值事例

```vue
<!-- 当安装和配置了vue-router，就可以使用router-link来替代 -->
    <!-- #不用写了 -->
    <router-link to="/home">首页</router-link>
    <!-- 在hash地址中/后面的参数叫做“路径参数” 要用this.$route.param来获取-->
    <!-- 在hash地址中？后面的参数叫做“查询参数”  要用this.$route.query来获取-->
    <!-- 在hash地址中 this.$route.fullPath是完整的地址 path是路径部分
         "/movie/1"路径部分
         "/movie/1?name=zs"-->
    <router-link to="/movie/1">洛基</router-link>
    <router-link to="/movie/2">雷神</router-link>
    <router-link to="/movie/3">复联</router-link>
    <router-link to="/about">关于</router-link>
    <hr />

    <!-- router-view占位符 
          在配置后自动进行占位-->
    <router-view></router-view>
```

##### this.$router的用法

在hash地址中/后面的参数叫做“路径参数” 要用this.$route.param来获取

在hash地址中？后面的参数叫做“查询参数”  要用this.$route.query来获取

在hash地址中 this.$route.fullPath是完整的地址 path是路径部分

```js
 gotoLk() {
      // 通过导航跳转进行页面跳转
      // push会有浏览器的历史记录
      this.$router.push('/movie/1')
    },
    gotoLk2() {
      // replace直接替换当前的历史记录，不会有历史记录
      this.$router.replace('/movie/1')
    },
    goBack() {
      // 这里是返回1次历史记录，可以设置多次n
      this.$router.go(-1)
      // 他的简化 后退1个历史记录
      this.$router.back()
      // 前进1个历史记录
      this.$router.forward()
    }
```



##### 路由守卫

```js
//为router实例对象，声明全局前置导航守卫
//只要发生了路由的跳转，必然会触发beforEach的回调函数
router.beforeEach(function(to, from, next) {
  // to可以得到将要访问的路由的信息对象
  // form是离开的路由信息对象
  // next() 用来放行
  // next()
  // 1.拿到用户将要访问的hash地址
  // 2.判断hash地址是否等于/main
  // 2.1(如果等于)，需要登录才能登录
  // 2.2 不等于，则不需要登录，直接放行,next()
  // 3. 如果访问的地址是/main，则需要localStorage中的token值
  if (to.path === '/main') {
    // 访问后台主页，需要判断是否有token
    const token = localStorage.getItem('token')
    if (token) {
      next()
    } else {
      next('/login')
    }
  } else {
    next()
  }
})
```

