#### Vue router 路由
  Vue Router是vue.js官方的路由管理器，构建单页面应用，功能有：嵌套的路由/视图表;模块化的，基于组件的路由配置;路由参数，查询，通配符；基于Vue.js过渡系统的视图过度效果；细粒度的导航控制；带有自动激活的css class的链接；HTML5历史模式或hash模式，在IE9中自动降级；自定义的滚动行为
- Vue-router 核心
   路由跳转的方式
   ```
    <template>
       <div>
          <!-- //使用router-link 组件来导航
            通过传入'to'属性指定链接
            <router-link> 默认会被渲染成一个<a>标签 -->
          <router-link to="/helloworld/123">Go to HelloWorld</router-link>
          <router-link to="/view1">view1</router-link>
          <router-link :to="{name:'view1'}">view1</router-link>
          <div @click="goto">Go to HelloWorld</div>
       </div>
    </template>
    <script>
       export default{
           methods:{
               goto(){
                   //编程式的导航
                   this.$router.push('/helloworld/123')
               }
           }
       }
    </script>

    const router = new Router({
        //定义路由
        //每个路由应该映射一个组件，其中component可以是
        //通过 Vue.extend() 创建的组件构造器
        // 或者，只是一个组件配置对象
        routes:[
            {
                //动态路径参数，以冒号开头
                path:'/helloworld/:id',
                //可以不通过this.$route获取id
                //直接当做组件的props使用
                props:true,
                // 通过name进行跳转
                name:'HelloWorld',
                component:HelloWorld
            }
        ]
    })
   ```
   - 动态路由匹配：动态路由路径参数，以冒号开头，如上
     - 获取动态路由的参数
       ```
        mounted(){
            console.log(this.$route.param.id)
        }
       ```
       ```
       直接当做props中的属性使用
        export default{
            name:'Helloworld',
            props:['id'],
            mounted(){
                console.log(this.id)
            }
        }
       ```
    - 响应路由的参数的变化
      - 当路由参数发生变化时，比如helloworld/123导航到 helloworld/456，原来的组件实例会被复用。因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效，不过这意味着组件的生命周期钩子不会再被调用
      - 复用组件时，想对路由参数的变化作出响应的话，可以用以下两种方式
        ```
         //方式一 导航守卫
         beforeRouteUpdate(to,from,next){
             // 具体逻辑代码
             console.log('update')
             next()
         },
         //方式二 watch（监测变化）
         watch:{
             '$route'(to,from){
                 console.log('路由参数变化')
             }
         }
        ```
   - 嵌套路由
     ```
      routes:[
          {
            path:'/',
            name:'routeDemo',
            children:[
                {
                    path:'home',
                    name:'home',
                    component:home
                },
                {
                 path:'view1',
                 name:'view1',
                 component:view1
                }
            ]
          }
      ]
      
      routerDemo.vue文件
      <router-link to="/home">home</router-link>
      <router-link :to="{name:'view1'}">view1</router-link>
      <router-view></router-view>// home或者view1渲染在这里
     ```
   - 编程式的导航
     ```
       除了使用创建a标签来定义导航链接，我们还可以借助router的实例方法，通过编写代码来实现：this.$router.push({path:''})
       文档示例
       router.push('home');//字符串
       router.push({path:'home'});//对象
       router.push({name:'user',params:{userId:123}})//命名路由
       router.push({path:'register',query:{plan:'private'}}) //带查询参数，变成 /register?plan=private
       ,获取this.$route.query.plan
       注意：如果提供了path,params会被忽略，上述例子中的query并不属于这种情况，取而代之的是下面例子的做法，你需要提供路由的name
       或手写完整的带有参数的path
      ------
       const userId=123
       router.push({name:'user',params:{userId}}); //-> /user/123
       router.push({path:`/user/${userId}`}) //->/user/123
       //这里的params不生效（注意这个例子）
       *** router.push({path:'/user',params:{userId}}) //-> /user
       //同样的规则也适用于router-link 组件的to属性
       <router-link to="/helloworld/123">Go to helloWorld123</router-link>
       <!-- 注意前面 to 前要加：  -->
       <router-link :to="{name:'helloWorld',params:{id:456}}">Go to helloWorld123</router-link>

     ``` 
   - 命名路由
     ```
      routes:[
          {
              path:'/helloworld/:id',
              //通过name进行跳转，命名路由
              name:'HelloWorld',
              component:HelloWorld
          }
      ]
      <router-link to="/helloworld/456">Go to HelloWorld456</router-link>
      <router-link :to="{name:'helloWorld',params:{id:456}}">Go to HelloWorld456</router-link>
      //这跟代码调用router.push()是一样的
      this.$router.push({name:'helloworld',params:{id:123}})
     ```
   - 命名视图
     - 有时候想同时(同级)展示多个视图，而不是嵌套展示，例如创建一个布局，有sidebar(侧导航)和main(主内容)两个视图，
       这个时候命名视图就派上用场了，你可以在界面中拥有多个单独命名的视图，而不是只有一个单独的出口，如果router-view没有
       设置名字，那么默认为default
         ```
           const router=new VueRouter({
           routes:[
               {
                  path:'/',
                  components:{
                      default:Foo,
                      a:Bar,
                      b:Baz
                  }
               }
             ]
           })
         ```
     - 嵌套命名视图
       ```js routerDemo.vue文件
       <router-view name="a"></router-view>
       <!-- 不写name为默认值 -->
       <router-view></router-view> 
       {
           path:'/',
           name:'routerdemo',
           component:routerDemo,
           children:[
               {
                   path:'',
                   components:{
                       default:view1,
                       a:home
                   }
               }
           ]
       }
       ```
     - 重定向和别名
       - 重定向 通过routes配置来完成
         ```
          //三种重定向的写法：
          {path:'/hello',redirect:'/helloworld/123'},
          {path:'/hello',redirect:{name:'helloWorld',params:{id:343}}},
          {path:'/hello',redirect:to=>{
            //方法接收目标路由作为参数
            // return 重定向的 字符串路径/路径对象
            return '/helloworld/999'
          }}
         ...
       - 别名(文档示例)
        - /a的别名是/b,意味着，当用户访问/b时，URL会保持为/b，但是路由匹配则为/a，就像用户访问/a一样
          ```
           const router=new VueRouter({
             routes:[
                 {path:'/a',component:A,alias:'/b'}
             ]
           })
          ```
       - 导航守卫
        - '导航表示路由正在发生改变'，记住参数查询的改变并不会触发进入/离开的导航守卫，
          可以通过观察$route对象来应对这些变化，或使用beforeRouteUpdate的组件内守卫
          - 全局守卫（在定义router的地方定义）
             ```js
              Vue.use(Router)
              const router= new Router({
               routes:[
                  {
                      path:'/helloworld/:id',
                      name:'HelloWorld',
                      component:HelloWorld
                  },
                  {
                      path:'/',
                      name:'routerdemo',
                      component:()=>import('@/components/routerDemo')
                  }
                ]
              })
              <!-- 全局钩子 -->
              <!-- 使用 router.beforeEach 注册一个全局 前置守卫 -->
              router.beforeEach((to,from,next)=>{
                 console.log('before');
                  if(to.fullPath==='/login'){
                    next('/login')
                  }else{
                    next()
                    }
              })
             <!-- router.beforeResolve 注册一个全局解析守卫 -->
              router.beforeResolve((to,from,next)=>{
                 console.log('beforeResolve');
                next()
              })

              <!-- router.afterEach 注册全局后置钩子 -->
              router.afterEach((to,from)=>{
                console.log('afterEach')
              })
             export default router
            ```
          - 路由独享的守卫(在路由内定义)
            ```js
             routes:[
                 {
                     path:'/helloworld/:id',
                     name:'HelloWorld',
                     component:HelloWorld,
                     beforeEnter:(to,from,next)=>{
                         console.log('路由内的守卫导航')
                     }
                 }
             ]
            ```
          - 组建内的守卫（在组件内定义）
            ```js
             export default{
                 //不能使用this,因为没有实例化
                 beforeRouteEnter(to,from,next){
                  //在渲染该组件的对应路由被confirm前调用
                  //不能获取组件实例this，因为当前守卫执行前，组件实例还没被创建
                  console.log('enter');
                  //不能使用this 但是能使用vm
                  //vm实例化后的this
                  next(vm=>{vm.id=100})
                  next()
                 },
                 beforeRouteUpdate(to,from,next){
                    console.log('update');
                    //在当前路由改变，但是该组件被复用时调用
                    //举例来说，对于一个带有动态参数的路径/foo/:id,在/foo/1和/foo/2之间跳转的时候
                    //由于会渲染同样的Foo组件，因此组件实例会被复用，而这个钩子就会在这个情况下被调用
                    // 就跟watch一样
                    //可以访问组件实例‘this’
                    next()
                 },
                  beforeRouteLeave(to,from,next){
                    //导航离开该组件的对应路由时调用
                    // 可以访问组件实例‘this’
                    console.log('Leave',this.id)
                    if(global.confirm('are you sure')){
                        next()
                    }
                 }
             }
            ```
       - router.go(n) 
        - 这个方法的参数是一个整数，意思是在history记录中向前或者后退多少步，类似window.history.go(n)
          - router.go(1) //在浏览器记录中前进一步，等同于history.forward()
          - router.go(-1) // 后退一步记录，等同于history.back()
          - router.go(3) // 前进3步记录