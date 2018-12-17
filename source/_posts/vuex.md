---
title: vuex
date: 2018-12-17 22:48:18
tags: Vuex
categories: Vuex
copyright: 乾學長
top:
---

## Vuex

### 安装、引入、使用

- `npm install vuex -S`

- 引入并使用

  ```javascript
  import Vuex from 'vuex'
  
  const store = new Vuex.Store({
      state: {
          count: 0
      },
      mutations: {
          updateCount (state, num) {
              state.count = num
          }
      }
  })
  
  export default () => {
      return store
  }
  ```

  在 main.js 中引入

  ```javascript
  import Vuex from 'vuex'
  import createStore from './store/store'
  
  Vue.use(Vuex)
  
  const store = createStore()
  ```

- 调用 store 对象

  ```javascript
  mounted () {
      console.log(this.$store)
      let i = 1
      this.$store.commit('updateCount', i++)
  },
  computed: {
      count () {
          return this.$store.state.count
      }
  }
  
  ```

- 开发使用

  - store 文件目录结构

    > store
    >
    > > store.js	
    >
    > > > state 
    > > >
    > > > > state.js
    > >
    > > > mutations
    > > >
    > > > > mutations.js
    > >
    > > > getters
    > > >
    > > > > getters.js
    > > >
    > > > actions
    > > >
    > > > > actions.js

  - store.js

    ```javascript
    import Vuex from 'vuex'
    
    import defaultState from './state/state'
    import mutations from './mutations/mutations'
    import getters from './getters/getters'
    import actions from './actions/actions'
    
    const isDev = process.env.NODE_ENV === 'development'
    
    export default () => {
        return new Vuex.Store({
            strict： isDev, // 开发环境如果在外部修改 state 的值，会报出警告
            state: defaultState,
            mutations,
            getters,
            actions
        })
    }
    ```

  - state.js `data`

    ```javascript
    export default {
        count: 0,
        firstName: 'Jobs',
        fullName: 'Steve'
    }
    ```

  - mutations.js `methods`  同步操作

    ```javascript
    export default {
        updateCount (state, num) {
            state.count = num
        }
    }
    ```

  - getters.js `computed`

    ```javascript
    export default {
        fullName (state) {
            return `${state.firstName} ${state.fullName}`
        }
    }
    ```

  - actions.js 异步操作

    ```
    export default {
        updateCountAsync (store, data) {
            setTimeout(() => {
                store.commit('updateCount', {
                    num: data.num
                })
            }, data.time)
        }
    }
    ```

  - 组件内调用

    ```javascript
    import {mapState, mapGetters, mapMutations, mapActions} from 'vuex'// Vuex辅助方法
    
    computed: {
        //state
        ...mapState(['count']) // 同名
        
        ...mapState({ // 不同名
    		counter: 'count'
        })
        
        ...mapState({ // 不同名, 可计算
            counter （state）=> state.count
        })
        
        // getters 调用方法同上
       
        // mutations
        ...mapMutations(['updateCount'])
         this.updateCount({
            num1: 5,
            num2: 2
        })
    
        // 简写为： 
        ...mapActions(['updateCountAsync'])
        this.updateCountAsync({
            num: 5,
            time: 2000
        })       
    }
    ```

    - 如果不支持 `...` 的语法，则需要安装`babel-preset-stage-1`

      ```shell
      npm i babel-preset-stage-1 -D
      ```

      **  `babelrc` 文件配置 **

      ```javascript
      "presets": [
          "env",
          "stage-1"
      ]
      ```

    - `Vue` 官方推荐 `state` 中数据的修改推荐放在 `mutations` 中

    - `mutations` 和 `actions `的参数传递只能是两个，第一个是 `state`，第二个是一个对象，即

      ```javascript
      export default {
          updateCount (state, {num1, num2}) {
              state.count = num1 + num2
          }
      }
      ```

  - `module` 模块，可嵌套

    ```javascript
    // Vuex 中定义
    modules: {
        a: {
            namespaced: true, //添加命名空间
            state: {
                text: 1
            },
            mutations: {
                updateText (state, text) {
                    state.text = text
                }
            }，
            getters: {
                textPlus (state, getters, rootState) { // rootState 为全局的 state
                    return state.text + rootState.b.text
                }
            },
            actions: {
                add ({state, commit, rootState }) {
                    commit('updateText', {num: 1}, {root: true})
                }   
            }
        },
        b: {
            state: {
                text: 2
            }
        }
    }
    
    //组件中调用
    methods: {
       ...mapMutations(['a/updateText'])
       ...mapActions(['a/add']) 
    },
    computed: {
        ...mapState({
            textA: state => state.a.text
        })   
        
        ...mapGetters({
            textPlus: 'a/textPlus'
        })    
    },
    mounted () {
      this['a/updateText']('123') //调用 mutatitns
        
      this['a/add']() // 调用 actions
    }
    ```

    - 动态注册和解绑模块

      ```javascript
      // 注册
      store.regigterModule('c', {
          state: {
              text: 3
          }
      })
      
      // 解绑
      store.unregigterModule('c')
      
      computed: {
          ...mapState({
              textC：state => state.c.text
          })
      }
      ```

### 热更新

```javascript
export default () => {
    const store = new Vuex.Store({
        state: defaultState,
        mutations,
        getters,
        actions
    })
    
    if (module.hot) {
    	module.hot.accept([
            './state/state',
            './mutations/mutations',
            './getters/getters',
            './actions/actions'
    	], () => {
            const newState = require('./state/state').default
            const newMutations = require('./mutations/mutations').default
            const newGetters = require('./getters/getters').default
            const newActions = require('./actions/actions').default
            
            store.hotUpdate({
                state: newState,
                mutations: newMutations,
                getters: newGetters,
                actions: newActions
            })
    	})
    }
    return store
}
```

### 常用API

- `store.watch`

  ```JavaScript
  store.watch((state) => {
  	state.count + 1 //监听想要得到的返回值
  }, () => {
      console.log('new count watched', newCount) // 作为第一个方法的回调
  })
  ```

- `store.subscribe`

  ```JavaScript
  store.subscribe((mutation, state) => {
      console.log(mutation.type)
      console.log(mutation.payload)
  })
  
  store.subscribe((action, state) => {
      console.log(action.type)
      console.log(action.payload)
  })
  ```

- 定义 `Vuex` 插件

  ```JavaScript
  export default () => {
      const store = new Vuex.Store({
          state: defaultState,
          mutations,
          getters,
          actions，
          plugins: [{
          	(store) => {
          		console.log('my plugin invoked')
      		}
      	}]
      })
  }
  ```


