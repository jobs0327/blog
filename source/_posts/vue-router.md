---
title: vue-router
copyright: 乾學長
date: 2018-12-17 22:49:53
tags: vue-router
categories: vue-router
top:
---

## Vue-Router

### 基本参数

```javascript
import routes from './routes'
export default () => {
    return new Router({
        routes，
        mode: 'history', // 去掉路由中的 #/
        base: '/base/', // 所有通过配置的路由在进入的时候都会加上 /base 的前缀，但是 base 不是强制性的，在没有/base 的时候，路由还是能够正常的使用
        linkActiveClass: 'activeLink', // 配置全局样式，修改链接被激活样式, 部分匹配
        linkExactActiveClass: 'exactActiveLink', // 完全匹配的当前路由
        scrollBehavior: (to, from, savedPosition) { // 页面路径跳转的时候，页面滚动位置
        	if (avedPosition) {
        		return savedPosition
    		} else {
                return {x: 0, y: 0}      
             }
    	},
        parseQuery (query) {},
        stringifyQuery (object) {},
        fallback: true // 不是所有的浏览器都支持 history 的方式，Vue会自动变为hash的形式，一般设置成 true 就行了，如果为 false，单页应用就不存在了 
    })
}

// 默认路由 / 自动重定向到 /app
{
    path: '/'
    redirect: '/app',
}

// routes 基本路由
{
    path: '/app',
    name: 'app',
    components: {
        default: Todo,
        a: Login
    },
    // 一般当做参数传递使用，防止vue自动忽略
    meta: {
    	title: '',
        descriptiton: ''
    }，
    children: [
        {
            path: 'children',
            component: Children
        }
    ]
}
    
// 动态query, query 不一定要在使用的时候拼上去，可以使用 props 的方式传递，当 props：true 的时候，只需要在组件中定义 props: ['id'] 即可，从而可以不用 $route 对象使用，不再需要 router 读取
{
    path: '/app/:id',
    props: true,
    name: 'app',
}
    
// props 也可以是一个方法进行自定义
{
	path: '/app/:id',
    props: (route) => {
        id: route.query.xx
    }
    name: 'app',
}

```

在 main.js 中引入

```
import createRouter from './router/routes'

const router = createRouter()

```

- `webpack` 配置

  ```javascript
  // webpack.config.client.js
  
  const devServer = {
      port: 8080,
      host: '0.0.0.0',
      overlay: {
      	errors: true
      },
      //如果 Router 对象中使用了mode: 'history'， 这里必须配置才能在手动进入项目的时候不会出现404，这里 index 的路径必须与 `webpack.config.base.js`中的 `output.publicPath` 相对应 `/public/`
      histortApiFallback: {
          index: '/index.html',
          //index: '/public/index.html'
      },
      hot: true
  }
  ```

### 常用API

- `transition` 组件包裹 `router-view` 路由切换时加上过渡动画

  ```vue
  //global.css
  
  .fade-enter-active, .fade-leave-active {
      transition: opcity .5s
  }
  
  .fade-enter, .fade-leave-to {
      opcity: 0
  }
  
  //.vue
  <transition name="fade">
  	<router-view />
  </transition>
  ```

- `this.$route` 该对象包含路由的所有参数

  - `params` 动态参数
  - `query` hash参数

### 路由守卫

- 全局守卫

  ```javascript
  router.beforeEach((to, from, next) => {
      console.log('before each invoked')
      if (to.fullPath === '/app') {
          next('/login')
      } else {
      	next()
      }
  })
  
  router.beforeResolve((to, from, next) => {
      console.log('before resolve invoked')
      next()
  })
  
  router.afterEach((to, from) => {
      console.log('after each invoked')
  })
  ```

- 路由配置守卫

  ```JavaScript
  {
      path: '/',
      name: 'app',
      components: {
          default: Todo,
      },
      meta: {
      	title: '',
          descriptiton: ''
      },
      //执行时间实在全局的 beforeEach 和 beforeResolve 之间
      beforeEnter(to, from, next) {
          console.log('app route before enter')
          next()
      }
  }
  ```

- 组件内守卫

  ```
  export default {
      beforeRouteEnter (to, from, next) {
          console.log('todo enter enter')
          next()
      },
      // 同样的路由形式在不通的参数改变的时候触发，优化了 watch 去监听路由参数的处理方式
      beforeRouteUpdate (to, from, next) {
          console.log('todo update enter')
          next()
      },
      beforeRouteLeave (to, from, next) {
          console.log('todo leave enter')
          next()
      },
      data () {
          return {}
      }
  }
  ```

  - 在组件内路由中是没法获取组件实例 this 的，可以通过下面的方式获取组件实例

    ```
    beforeRouteEnter (to, from, next) {
        next(vm => {
        	console.log('todo enter enter', vm)
        })
    },
    
    ```

### 异步路由

- 用法

  ```
  // 组件内配置
  component: () => import('../login/login.vue'),
  
  // 修改 router
  {
      path: '/login',
      component: import('../login/login.vue')
  }
  
  // 安装插件
  npm babel-plugin-syntax-dynamic-import -D
  
  // babelrc 文件
  "plugins": [
      "syntax-dynamic-import"
  ]
  
  //重启服务
  
  
  ```

- 作用：加速首屏渲染的速度
