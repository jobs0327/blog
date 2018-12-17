---
title: vue基础
date: 2018-12-17 22:45:37
tags: vue
categories: vue
copyright: 乾學長
top:
---

## Vue 基础学习

### 实例的属性和方法

- `app.$mount('#app') === #el: '#app'`

- `app.text `(text为data中变量)直接访问

- `app.$data `访问 data 属性

- `app.$props` 访问 props 属性

- `app.$el === '#app'` 根节点

- `app.$options` 访问app实例的所有属性

- app实例中 data 属性在改变的时候 `render` 方法会执行

  ```javascript
  app.$options.render = （h）=> {
      return h('div', {}, 'new render function')
  }
  ```

- `app.$root === app` 

- `app,$children` 子组件属性

- `app.$slots` 插槽

- `app.$scopedSlots` 插槽

- `app.$refs` 模板的引用，快速定位组件或者元素，如果作用在组件上，返回的是当前组件的实例，如果作用在元素上，返回的就是当前的元素，

- `app.$isServer` 判断是否为服务端渲染

- `app.$watch == watch属性`

- `app.$on('text', (a, b) => {console.log('test emit' + a + b)})`

- `app.$emit('text', 1, 2)` 事件绑定和监听的必须是同一个app，不会冒泡

- `app.$forceUpdate()` 强制组件更新，如果在 data 中没有直接申明某个属性， 改变这个属性，并不能更新视图，最好的方法不是使用强制刷新，而是在 data 中定义这个属性并设置默认值

- `app.$set(app.obj, 'a', 1)` 给 data 添加新属性

- `app.$delete(app.obj.a)` 删除 data 中的 obj 属性

- `vm.$nextTick([callback])` 组件Dom更新使用

### 生命周期

- beforeCreate 实例创建

- Created

- beforeMount 数据挂载

- mounted

- beforeUpdate 数据更新

- updated

- activated 动态组件 `keep-alive`

- deactivated

- beforeDestroy 实例销毁

- destroyed

  - 可以只用 `app.$ + 生命周期函数名` 调用

  - 服务端渲染的情况下只有 beforeCreate, created 执行

  - `render` 方法

    ```javascript
    render (h) {
        console.log('render 方法会在 beforeMounte 和 mounted 之间执行')
    }
    ```

  - `renderError()` 收集错误，仅限当前组件， 不会冒泡

  - `errorCaptured()` 收集错误，可冒泡，收集本身和所有子组件的错误，除非子组件停止了事件冒泡 

### 数据绑定

- `v-html` 数据绑定时将`html`字符串解析成`html`结构
- `v-bind` 单项数据绑定简写为  `:`
- `v-on `事件绑定，简写为 `@`
- `v-model` 表单元素数据双向绑定
- `{{ data }}`非表单元素双向绑定
- 动态绑定类名
  - `:class="{ active: isActive }"` 动态绑定类名
  - `:class="[isActive ? active : '']"` 数组内写表达式
  - `:class="[{ active: isActive }]"` 合并上面两种方式，推荐
  - 计算属性绑定
- 样式绑定（ vue 绑定样式的时候，会根据浏览器的不同自动添加浏览器前缀）
  - `:style="styles"` 数据绑定方式,  [styles: Object]
  - `:style="[style1, style2]"` 绑定多个样式

### 计算属性和侦听器

- `computed` 只有当前有依赖的 data 属性值发生改变时，才会重新计算执行
- `computed` 拥有 get 和 set 方法，但是不到万不得已不要使用 set 方法
- `watch` 所监听的 对象必须是 data 中的某个属性
- `watch` 中包含 `handler`  `immediate: true`  `deep: false`三个属性
  - `handler`  是当所监听的对象发生改变的时候执行的回调方法
  - `immediate: true` watch 监听对象的方法是不会马上执行的，只有有改变的时候才会执行 handler， 当immediate 配置项为 true 的时候，watch的 handler 方法才会立即执行一次
  - `deep: false`默认为 false ，表示 watch 只会监听被监听对象的引用，不会监听内部的属性，只有当该对象被重新赋值的时候才会被监听到，当 `deep: true` 的时候才能监听到内部的属性值的改变
  - 可以将内部属性以 `'obj.a'` 的方式去监听，就可以监听到内部属性的改变，推荐
- 不要试图在 `computed` 和 `watch` 中试图修改被监听的对象，不然可能会出现死循环的情况，尤其在 `computed `中

### 指令

- `v-bind` 属性的绑定

- `v-text` 标签内显示的内容 当出现`<div v-text="text">{{ text }}</div>`不会显示双花括号中的内容

- `v-html` 将数据当做 html 字符串的方式进行解析

- `v-show` 是否显示当前节点，原理：`display: none`

- `v-if` 是否存在当前节点，(动态增删节点，引起 dom 重绘) 

- `v-else` `v-else-if` 配合 `v-if` 进行条件渲染

- `v-for` 遍历数组或者对象

  ```
  <div v-for="(item, index) in arr" :key="item">{{ item }}</div>
  <div v-for="(value, key, index) in obj" :key="key">{{ value }}</div>
  ```

  - 指定 key 就会缓存 dom 节点，在数据重新渲染的时候提高性能，一般在遍历数组的时候使用 item 作为key，如果使用 index 作为 key 的话，没有任何意义

- `v-on` 事件绑定

- `v-model` 一般作用于表单元素实现双向绑定

  - 修饰符
    - `v-model.number` 转数字格式
    - `v-model.trim` 去掉首位空格
    - `v-model.lazy` 默认文本框绑定的是 input 事件, 当时该修饰符的时候绑定的事件就是 change 事件

- `v-pre` 不解析 data 数据，直接输出

- `v-cloak` 在vue代码还没有加载完成之前，可以给元素加上这个指令不显示组件，使用 `webpack` 打包之后这个指令一般用不到

- `v-once` 数据绑定到节点中不会被修改，节省性能开销，`vue` 不再根据虚拟 `Dom` 检查该节点

### 组件

- 定义组件名最好用大写的方式，大驼峰方式，使用组件的时候使用短横线的形式

- ```javascript
  const component = {
      template: '<div></div>',
      data () {
          return {
              text: 123
          }
      }
  }
  
  new Vue({
      el: '#app'
      components: {
      	comp: component
  	},
      template: `
  		<comp></comp>
  	`
  })
  ```

- 定义组件的时候 ` data` 必须是一个方法， 且 `return` 的必须是一个新建的对象，不能是已有的全局对象

- `props` 作用

  - 定义组件被外部使用的时候所需要的配置参数

  - ```javascript
    //类型检查
    props: {
        active: Boolean，
        onChange: Function
    }
    
    //直接传值,不够严谨
    props: ['active', 'onChange']
    
    //严谨方式
    props: {
        active： {
            type：Boolean,
            required: true,
            default: true,
            validator (value) {
             	return typeof value === Boolean   
            }
        },
        obj: {
            type：Object,
            //required: true,
            default () {
                return newObj
            }
        }
        onChange: Function
    }
    // 如果所传的值是一个对象，default 需要是一个方法返回一个默认对象
    // 如果有了 default ，required 则不会生效
    // validator可以自定义校验规则
    ```

  - 命名和使用规范： 定义小驼峰命名方式，使用短横杠的方式

  - `props` 中的参数变量最好不要做主动修改，`props` 是单项数据流

- `extend` 生成 `Vue` 的一个子类

  - ```javascript
    const CompVue = Vue.extend(comp)
    new CompVue({
        el: '#app'
    })
    ```

  - 生成的子类，`data` 属性和生命周期等都会进行合并，父类先执行，子类后执行

  - 作用：组件的扩展和重构

  - 子类通过 `this.$parent` 访问父类， 最好不要进行修改

- `model` 属性，避免变量冲突，自定义 `props` 中的变量名

  - 实现 `v-model` 的双向绑定

    ```JavaScript
    const component = {
        model: {
            prop: 'myValue',
        	event: 'change'
        },
        props: ['myValue'],
        template: `
        	<div>
        		<input type="text" @input="handleInput" :value="myValue">
        	</div>
        `,
        methods: {
            handleInput (e) {
                this.$emit('change', e.target.value)
            }
        }
    }
    
    new Vue({
        el: '#app',
        components: {
            comp: component
        },
        data () {
            return {
                value: '123'
            }
        },
        template: `
        	<div>
        		<comp-one v-model="value"></comp-one>
        	</div>
        `
    })
    ```

- `provide` 和 `inject` 属性：组件内使用越级的实例

  ```javascript
  const component = {
      inject：['value', 'data'],
      template: `
      	<div>child component: {{ data.value }}</div>
      `
  }
  
  new Vue({
      el: '#app',
      components: {
          comp: component
      },
      provide () {
          const data = {}
          Object.defineProperty(data, 'value', {
              get: () => this.value,
              enumerable: true // 可被读取
          })
          return {
              value: this.value,
              data
          }
      },
      data () {
          return {
              value: '123'
          }
      },
  })
  ```

- `render` 方法

  - 组件的 `template` 属性是通过 `render` 方法编译成为虚拟 Dom

    ```javascript
    // 这里 createElement 就是 Vue 虚拟 dom 的概念 即 VNode 类
    render (createElement) {
        return createElement('comp1', { // 根节点
            ref: 'comp1'，
            props: {
            	props1: this.value
        	},
            on: {
              	click: this.handleClick               
            },
            nativeOn: { // 直接创建在根节点上
              	click: this.handleClick               
            },
            domProps: { //原生 dom 操作
                innerHtml: '<span>123123</span>'             
            },
            slots: {
             	
            }
            attrs: {
    			id: 'test-id'
            }
        }, //子节点
    	[
         	createElement('comp2', {
            	ref: 'comp2'
        	}, this.value)
        ])
    }
    ```


### 插槽

- 在使用组件的中间添加内容

```javascript
const component = {
    template: `
    	<div>
    		<slot name="header"></slot>	
			<slot my-value="123"></slot> // 具名插槽
    	</div>
    `
}

new Vue({
    el: '#app',
    components: {
        comp: component
    },
    data () {
        return {
            value: '123'
        }
    },
    template: `
    	<div>
    		<comp-one>
                // 使用引用该组件的属性
                <span slot="header">{{ value }}</span> 
                // 作用域插槽，使用组件内部定义的属性					
                <span slot-scope="props">{{ props.myValue }}</span> 
			</comp-one>
    	</div>
    `
})
```

