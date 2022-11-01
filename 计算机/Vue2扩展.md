
## vue-awesome-swiper

在Vue3中我们可以直接使用swiper

地址：https://swiperjs.com/vue

但在vue2中，我们直接使用 swiper的话，并不会应用，并且还有很多问题，那我们就使用 vue-awesome-swiper



推荐版本对应

```shell
npm i swiper@5.2.0
npm i vue-awesome-swiper@4.1.1
```

官方文档 ：https://github.com/surmon-china/vue-awesome-swiper/tree/v4.1.1

### 客户端渲染

单组件引入这样:

```js
import { Swiper, SwiperSlide, directive } from 'vue-awesome-swiper'
import 'swiper/css/swiper.css'

export default {
  components: {
    Swiper,
    SwiperSlide
  },
  directives: {
    swiper: directive
  }
}
```

全局引入
```js
import Vue from 'vue'
import VueAwesomeSwiper from 'vue-awesome-swiper'

// import style
import 'swiper/css/swiper.css'

Vue.use(VueAwesomeSwiper, /* { default options with global component } */)
```


示例代码
```html
<template>
  <swiper class="swiper" ref="mySwiper" :options="swiperOptions">
    <swiper-slide>Slide 1</swiper-slide>
    <swiper-slide>Slide 2</swiper-slide>
    <swiper-slide>Slide 3</swiper-slide>
    <swiper-slide>Slide 4</swiper-slide>
    <swiper-slide>Slide 5</swiper-slide>
    <div class="swiper-pagination" slot="pagination"></div>
  </swiper>
</template>

<script>
  export default {
    name: 'carrousel',
    data() {
      return {
        swiperOptions: {
          pagination: {
            el: '.swiper-pagination'
          },
          // Some Swiper option/callback...
        }
      }
    },
    computed: {
      swiper() {
        return this.$refs.mySwiper.$swiper
      }
    },
    mounted() {
      console.log('Current Swiper instance object', this.swiper)
      this.swiper.slideTo(3, 1000, false)
    }
  }
</script>
```

如果想要给模块加样式，可以点开源代码看开对应的类名然后写css

### nuxt

vue-awesome-swiper.js文件
```js
import Vue from 'vue'
import VueAwesomeSwiper from 'vue-awesome-swiper'

// import style
import 'swiper/css/swiper.css'

Vue.use(VueAwesomeSwiper, /* { default options with global component } */)
```


示例代码
```html
<template>
  <div v-swiper:mySwiper="swiperOption">
    <div class="swiper-wrapper">
      <div class="swiper-slide" :key="banner" v-for="banner in banners">
        <img :src="banner">
      </div>
    </div>
    <div class="swiper-pagination"></div>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        banners: [ '/1.jpg', '/2.jpg', '/3.jpg' ],
        swiperOption: {
          pagination: {
            el: '.swiper-pagination'
          },
          // ...
        }
      }
    },
    mounted() {
      console.log('Current Swiper instance object', this.mySwiper)
      this.mySwiper.slideTo(3, 1000, false)
    }
  }
</script>
```

这个插件需要用到dom api ，所以配置中应该使用client

# Nuxt

中文官网 https://www.nuxtjs.cn/

## 简介
基于 vue.js 的服务端渲染,是在服务端对 vue页面进行渲染生成 html 文件，再将 html 文件（html 字符串）传回给浏览器，不同于SPA 的单页面只有一个空的 Html 和 app.js，nuxt 生成的 html 是有内容的，所以更有利于搜索引擎的 seo 操作，并且加快了首屏加载时间。

## server和client端渲染问题

### 生命周期

-   红框内的是Nuxt 的生命周期(运行在服务端)，
-   黄框内同时运行在服务端&&客户端上，
-   绿框内则运行在客户端
![[Pasted image 20221030111824.png]]

### 流程

1.关于不能调用window的问题  
    **因为 红框、黄框内的周期在服务端运行，所以都不存在Window对象，所以不能直接使用window**
```html
    <script>
export default {
 asyncData() {
   console.log(window) // 服务端报错
 },
 fetch() {
   console.log(window) // 服务端报错
 },
 created () {
   console.log(window) // undefined
 },
 mounted () {
   console.log(window) // Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, frames: Window, …}
 }
}
</script>

```

2.整个页面流程：
当页面第一次加载或者重新reload页面时，**会从上向下一次执行，但是之后的路由跳转都是在client端中执行**，和server端就没什么关系了，**包括页面中的asyncData**，其实我一开始以为asyncData是一直在server端运行，其实并不是，除了第一次加载，其他的通过路由跳转的asyncData都是在client端运行

3.incoming request
 这个阶段是服务器收到请求，开始走流程

4.nuxtServerInit
-   服务器初始化
-   只能够在store/index.js中使用
-   用于在渲染页面之前存储数据到vuex中
-   接受两个参数：vuex上下文，和nuxt上下文

```js
actions: {
	nuxtServerInit ({ commit }, { req }) {
		if (req.session.user) {
 	 commit('user', req.session.user)
  	}
  }
}
//异步 nuxtServerInit 操作必须返回一个 Promise 或利用 async/await 来允许 nuxt 服务器等待它们。
actions: {
  async nuxtServerInit({ dispatch }) {
    await dispatch('core/load')
  }
}

```

5.middleware
- 这个阶段会执行一些预定义的中间件，自己定义的中间件也会在这个阶段执行
- 第一次进入页面或者刷新页面是在server端运行，之后的路由跳转是在client端运行
- 如果你写的是一个插件，那么如果你设置了该插件的属性为sse:false,表示只会在客户端运行该插件，如果你设置为了true,则表示既会在客户端运行，也会在服务器端运行
- 如在nuxt中使用axios，有两种方式：

>[!note] axios
>1.写两个req 的api作为两个插件: 一个在服务器请求名字为server-api.js，（服务端请求+asyncData中的请求）ssr设为true，一个在客户端请求 名为client-api.js,ssr设为false
>2. 只写一个re的qpi作为一个插件：将其ssr设为true即可

6.validate ：

-   可以让你在动态路由对应的页面组件中配置一个校验方法用于校验动态路由参数的有效性
-   [validate每次在导航到新路线之前都会调用](https://nuxtjs.org/docs/components-glossary/validate#the-validate-method)
```js
validate({ params, query, store }) {
  return true // if the params are valid
  return false // will stop Nuxt to render the route and display the error page
}

```

7.asyncData()和fecth()
- asyncData(): 你能够在渲染组件之前异步获取数据。好比你在vue组件中用created获取数据一样，不同的是asyncData是在服务端执行的
- 注意：asyncData只是在首屏的时候调用一次（即页面渲染之前，所以事件触发不了它）
- fectch(): 它会在组件每次加载前被调用（在服务端或切换至目标路由之前）,同时为了让获取过程可以异步，你需要返回一个 Promise，Nuxt.js 会等这个 promise 完成后再渲染组件
- 两者使用可看具体用法：[asyncData()和fecth()](https://blog.csdn.net/weixin_46331416/article/details/123477475)

8.render

-   这个阶段开始准备客户端渲染，如果过程中有通过nuxt-link跳转，会退回至middleware阶段重新执行

9.beforeCreat和created阶段

-   这个和vue中的钩子函数功能基本类似，有一个小的差别:
-   vue的这两个钩子只在客户端执行;
-   nuxt的这两个钩子在客户端和服务端都会执行一遍

10.其他阶段：之后的阶段都是在客户端中执行，比如beforeMount和mounted阶段等等

## axios问题

axios的请求在哪里发？

想要在ssr种进行服务端渲染，必须要在 asyncData或者 beforeCreate 、create中发，但后两个因为服务端和客户端都会执行，所以对于需要服务端渲染的页面，我们在asyncData发送axios请求

有的界面就是只有首页在进行服务端渲染（为了SEO），其他界面并没有

**nuxt中可以同时引入nuxt/axios和普通的axios，按需使用**


# axios

axios在vue2推荐版本 0.18.1，最新的版本使用有问题，并没有测试

## 客户端渲染

自定义一个axios封装

```js
import axios from 'axios'  
import { MessageBox, Message } from 'element-ui'  
import store from '@/store'  
import { getToken } from '@/utils/auth'  
  
// create an axios instance  
const service = axios.create({  
  baseURL: process.env.VUE_APP_BASE_API, // url = base url + request url  
  // withCredentials: true, 
  // send cookies when cross-domain requests  
  timeout: 5000 // request timeout  
})  
  
// request interceptor  
service.interceptors.request.use(  
  config => {  
    // do something before request is sent  
  
    if (store.getters.token) {  
      // let each request carry token  
      // ['X-Token'] is a custom headers key      
      // please modify it according to the actual situation      
      config.headers['X-Token'] = getToken()  
    }  
    return config  
  },  
  error => {  
    // do something with request error  
    console.log(error) // for debug  
    return Promise.reject(error)  
  }  
)  
  
// response interceptor  
service.interceptors.response.use(  
  /**  
   * If you want to get http information such as headers or status   * Please return  response => response  */  
  /**   * Determine the request status by custom code   * Here is just an example   * You can also judge the status by HTTP Status Code   */  
  response => {  
    const res = response.data  
  
    // if the custom code is not 20000, it is judged as an error.  
    if (res.code !== 20000) {  
      Message({  
        message: res.message || 'Error',  
        type: 'error',  
        duration: 5 * 1000  
      })  
  
      // 50008: Illegal token; 50012: Other clients logged in; 50014: Token expired;  
      if (res.code === 50008 || res.code === 50012 || res.code === 50014) {  
        // to re-login  
        MessageBox.confirm('You have been logged out, you can cancel to stay on this page, or log in again', 'Confirm logout', {  
          confirmButtonText: 'Re-Login',  
          cancelButtonText: 'Cancel',  
          type: 'warning'  
        }).then(() => {  
          store.dispatch('user/resetToken').then(() => {  
            location.reload()  
          })  
        })  
      }  
      return Promise.reject(new Error(res.message || 'Error'))  
    } else {  
      return res  
    }  
  },  
  error => {  
    console.log('err' + error) // for debug  
    Message({  
      message: error.message,  
      type: 'error',  
      duration: 5 * 1000  
    })  
    return Promise.reject(error)  
  }  
)  
  
export default service
```

然后就可以引用这个封装，并定义函数了

```js
import request from '@/utils/request'  
export default {  
  //根据课程id获取章节和小节数据列表  
  getAllChapterVideo(courseId){  
    return request({  
      url:`/eduService/chapter/getChapterVideo/${courseId}`,  
      method:'get'  
    })  
  }
}
```

然后再method中调用就行

## nuxt

nuxt2使用的是nuxt/axios，是专门对服务端渲染的封装，已经封装好了

nuxt3使用的是新的同构，$fetch API

官网：https://axios.nuxtjs.org/

在创建nuxt项目时，就可以引入nuxt/axios。

用法 

服务端渲染-asyncdata

写法和方法中有些不一样，服务端没有this

```js
async asyncData({ $axios }) {
  const ip = await $axios.$get('http://icanhazip.com')
  return { ip }
}
```

>[!note] .then和async
>这里说一下这两个的区别
>async会让await的程序进行等待，使其看起来同步的执行
>.then其实就是再开一个异步请求，其下方的代码会直接执行，但其内部执行需要等其依靠的异步请求得到结果
>所以.then中不要写return,其实函数已经结束了

method中
``` js
methods: {
  async fetchSomething() {
    const ip = await this.$axios.$get('http://icanhazip.com')
    this.ip = ip
  }
}
```


**nuxt接口统一管理**

nuxt因为和axios不一样，所以进行接口统一管理也不一样

在项目根目录下新建一个`api`文件夹，存放各类接口模块，例如`article.js`：

```js
export default $axios => ({
  getArticleList () {
    return $axios.$get('/api/posts')
  },
  // 其它接口
})

```

其它接口模块亦如此。

其次在`plugins`目录下新建 axios.js

这里其实用的就是创建一个axios示例的代码
```js
import articleModule from '../api/article'
export default function ({ $axios }, inject) {
  const apiModules = {}
  $axios.onRequest((config) => {
  	// 相关配置
  })
  apiModules.article = articleModule($axios)
  // Inject to context as $api
  inject('api', apiModules)
}

```

最后不要忘记在`nuxt.config.js`中引入该插件：

```js
export default {
  // Plugins to run before rendering page: https://go.nuxtjs.dev/config-plugins
  plugins: [
    '~/plugins/axios'
  ],
}
```

`asyncData`中：

```js
  async asyncData ({ $api }) {
    const { data: articleList } = await $api.article.getArticleList()
    return { articleList }
  },
```

`methods`中：

```js
    getArticleList () {
      this.$api.article.getArticleList().then((res) => {
        console.log(res)
      })
    }
```