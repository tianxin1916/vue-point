#### Vuex 仓库
  ##### 专为vue.js应用程序开发的状态管理模式，采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化
  ##### 核心概念
   - state：用来存放组件之间共享的数据，跟组件的data类似，只不过data选项是用来存放组件的私有数据
     ```js
      //main.js
      import Vue from 'vue';
      import App from './App.vue'
      import Vuex from 'vuex'
      Vue.use(Vuex);
       const store = new Vuex.Store({//要设置的全局访问的state对象
        state:{
          showFooter: true,
          changableNum:0,
          a:1,
          b:23
         //要设置的初始属性值
         } 
        });
       new Vue({
          el:'#app',
          store,
          render:h=>h(App)
      }) 
      //此时 a.vue 和 b.vue也需要做相应的更改
       a.vue
       export default{
           data(){
               return {
                   a:this.$store.state.a //获取store中state的数据 值为1
               }
           }
       }
       b.vue
       export default{
           data(){
               return {
                   b:this.$store.state.b //获取store中state的数据 值为23
               }
           }
       }
     ```
   - Getter:getters属性理解为所有组件的computed属性，也就是计算属性，vuex的官方文档也是说可以
     将getter理解为store的计算属性，getters的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生改变才会被重新计算
     ```js
       const store = new Vuex.Store({//要设置的全局访问的state对象
         state:{
            showFooter: true,
            changableNum:0,
            a:1,
            b:23
          //要设置的初始属性值
          },
         getters:{  //添加getters
           getFun:(state)=>{
              return{
                  a:state*2 // 乘以2
              }
           }
         } 
        });
        //此时更改a 
       a.vue
       export default{
           data(){
               return {
                   a:this.$store.getters.a //获取store中state的数据  值为2
               }
           }
       }
     ```
   - Mutation:mutations可以理解为store中的methods，mutations对象中保存着更改数据的回调函数，
      第一个参数是state，第二个参数是自定义的参数
      ```js
       const store = new Vuex.Store({//要设置的全局访问的state对象
         state:{
            showFooter: true,
            changableNum:0,
            a:1,
            b:23
          //要设置的初始属性值
          },
         getters:{  //添加getters
           getFun:(state)=>{
              return{
                  a:state*2 //值为2
              }
           }
         },
         mutations:{ //添加mutations
             getA(state,num){
               state.a*=num 
             }

         } 
        });
        //在a.vue中 可以添加一个方法，在该方法中commit mutations中的getA这个回调函数
        export default{
           data(){
               return {
                   a:this.$store.getters.a //获取store中state的数据  值为2
               }
           },
           methods:{
               count(){
                   this.$store.commit('getA',8)
               }
           }
       }
      ```
   - Actions
     actions类似于mutations，不同在于
     - actions提交的是mutations而不是直接变更状态
     - actions中可以包含异步操作，mutations中绝对不允许出现异步
     - actions中的回调函数的第一个参数是context，是一个与store实例具有相同属性和方法的对象
     ```js
         const store = new Vuex.Store({//要设置的全局访问的state对象
         state:{
            showFooter: true,
            changableNum:0,
            a:1,
            b:23
          //要设置的初始属性值
          },
         getters:{  //添加getters
           getFun:(state)=>{
              return{
                  a:state*2 //值为2
              }
           }
         },
         mutations:{ //添加mutations
             getCount(state,num){
               state.a*=num 
             }

         } 
        });
        actions:{ //添加actions
         getCountAsync(context,num){
             setTimeout(()=>{
                 context.commit('getCount',num)//context提交
             },2000)
         }
        }
      //a.vue
       export default{
           data(){
               return {
                   a:this.$store.getters.a //获取store中state的数据  值为2
               }
           },
           methods:{
               count(){
                   this.$store.commit('getCount',8)
               }
               countAsync(){
                   this.$store.dispatch('getCountAsync',5) //分发actions中的getCountAsync这个异步函数
               }
           }
       }
     ```
   - Modules: 
     ##### 由于使用单一状态树，应用的所有状态会集中到一个比较大的对象，当应用变得
      非常复杂时，store对象就有可能变得相当臃肿，为了解决以上问题，Vuex允许我们将store分割成模块(module)
      每个模块拥有自己的state，mutation，action，getter,甚至是嵌套子模块--从上至下进行同样方式的分割
      ```js
        const moduleA = {
            state:{},
            mutations:{...},
            actions:{...},
            getters:{...}
        }
        const moduleB = {
            state:{},
            mutations:{...},
            actions:{...},
            getters:{...}
        }
        const store=new Vuex.Store({
            modules:{
                a:moduleA,
                b:moduleB
            }
        })
      ```
   - 辅助函数
    - mapState 工具函数会将store中的state映射到局部计算属性中
      - 底层代码
         ```js
          export function mapState (states) {
           const res = {}
            normalizeMap(states).forEach(({ key, val }) => {
              res[key] = function mappedState () {
                return typeof val === 'function'
               ? val.call(this, this.$store.state, this.$store.getters)
              : this.$store.state[val]
              }
             })
              return res
            }
            function normalizeMap (map) {
              return Array.isArray(map)
              ? map.map(key => ({ key, val: key }))
             : Object.keys(map).map(key => ({ key, val: map[key] }))
            }
         ```
         ```js
           <!-- vuex提供了独立的构建工具函数 Vuex.mapState -->
            import {mapState} from 'vuex' 
            export default {
              computed: mapState({
               <!--箭头函数可以代码非常简单 -->
                 count:state=>state.count,
                <!-- 传入字符串‘count’等同于`state=>state.count` -->
                 countAlias:'count',
                <!-- 想访问局部状态，就必须借助于一个普通函数，函数中使用‘this’获取局部状态 -->
                 countPlusLocalState(state){
                   return state.count+this.localCount
                 }
              })
            }
            //经过底层代码解析可以得到
            import { mapState } from 'vuex'
            export default{
              computed:{
                  count(){
                     return this.$store.state.count
                  },
                  countAlias(){
                     return this.$store.state['count']
                  },
                 countPlusLocalState(){
                     return this.$store.state.count + this.localCount
                 }
              }
           }
         ```
      -  当计算属性名称和状态子树名称对应相同时，我们可以向mapState工具函数传入一个字符串数组
        ```js
         computed:mapState([
            'count' //映射 this.count 到 this.$store.state.count
         ])
         
         //经过底层代码解析
         computed:{
             count(){
                 return this.$store.state['count']
             }
         }
        ```
    - mapGetters
      - mapGetters 工具函数会将store中的getter映射到局部计算属性中，它的功能和mapState非常类似，可以看底层代码
        ```js
         export function mapGetters (getters) {
           const res = {}
           normalizeMap(getters).forEach(({ key, val }) => {
             res[key] = function mappedGetter () {
           if (!(val in this.$store.getters)) {
           console.error(`[vuex] unknown getter: ${val}`)
           }
           return this.$store.getters[val]
          }
          })
          return res
         }
        ```
      - mapGetters的实现也和mapState很类似，不同的是它的val不是函数，只能是一个字符串
      - 而且会检查val in this.$store.getters的值，如果为false会输出一条错误日志
       ```
        import { mapGetters } from 'vuex'
        export default {
            computed:{
                ...mapGetters([
                    'doneTodosCount',
                    'anotherGetter'
                ])
            }
        }
        经过mapGetters函数调用后的结果，如下所示
        import { mapGetters } from 'vuex'
        export default {
            computed:{
                doneTodosCount(){
                    return this.$store.getters['doneTodosCount']
                },
                anotherGetter(){
                    return this.$store.getters['anotherGetter']
                }
            }
        }
       ```
      - 再看一个参数mapGetters 参数是对象的例子：
         ```js
          computed:{
            doneCount(){
                return this.$store.getters['doneTodosCount']
             }
           }
         ```
      - mapActions
        - mapActions工具函数会将store中的dispatch方法映射到组件的methods中，和mapState,mapGetters也类似，
          只不过它映射的地方不是计算属性，而是组件的methods对象上,底层代码
          ```js
           export function mapActions (actions) {
            const res = {}
            normalizeMap(actions).forEach(({ key, val }) => {
              res[key] = function mappedAction (...args) {
               return this.$store.dispatch.apply(this.$store, [val].concat(args))
             }
           })
             return res
           }
         ```
        - 可以看到，函数的实现套路和mapState，mapGetters差不多，甚至更简单一些，实际上就是做了一层函数包装
           ```js
              import { mapActions } from 'vuex'
              export default {
                 methods:{
                     ...mapActions(['increment']), //映射this.increment() 到 this.$store.dispatch('increment')
                     ...mapActions([add:'increment'])  //映射this.add() 到 this.$store.dispatch('increment')
                 }
             }
             -经过mapActions函数调用后的结果
               import { mapActions } from 'vuex'
               export default{
                   methods:{
                    increment(...args){
                     return this.$store.dispatch.apply(this.$store,['increment'].concat(args))
                    }
                    add(...args){
                       return this.$store.dispatch.apply(this.$store,['increment'].concat(args))
                    }
                }
            }
          ```
      - mapMutations
        - mapMutations工具函数会将store中的commit方法映射到组件的methods中，和mapActions的功能几乎一样，底层代码
          ```js
            export function mapMutations (mutations) {
              const res = {}
             normalizeMap(mutations).forEach(({ key, val }) => {
               res[key] = function mappedMutation (...args) {
                return this.$store.commit.apply(this.$store, [val].concat(args))
              }
             })
                return res
            }
          ```
        - 函数的实现几乎也和mapActions一样，唯一差别就是映射的是store的commit方法
          ```js
           import { mapMutations } from 'vuex'
            export default {
               // ...
             methods: {
             ...mapMutations([
               'increment' // 映射 this.increment() 到 this.$store.commit('increment')
               ]),
              ...mapMutations({
                 add: 'increment' // 映射 this.add() 到 this.$store.commit('increment')
                })
              }
            }
            //经过mapMutations函数调用后结果
            import { mapActions } from 'vuex'
            export default {
               // ...
            methods: {
              increment(...args) {
                  return this.$store.commit.apply(this.$store, ['increment'].concat(args))
                 }
              add(...args) {
                 return this.$store.commit.apply(this.$store, ['increment'].concat(args))
               }
              }
             }
          ```
