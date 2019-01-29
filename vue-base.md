#### Vue基础
  - 双括号（插值表达式）
    - 语法：<any>{{表达式}}</any>
    - 作用：将表达式执行的结果输出当前调用元素的innerHTML中，还可以将数据绑定到视图
  - 指令-循环指令
    - 语法1：
      ```
       <any v-for="tmp in array"></any>
      ```
    - 语法2：
      ```
       <any v-for="(value,index) in array"></any>
      ```
  - 指令-选择指令
    - 语法
      ```
       <any v-if="表达式"></any>
       <any v-else-if="表达式"></any>
       <any v-else="表达式"></any>
      ```
  - 指令-显示和隐藏
    - 语法
      ```
       <any v-show="表达式"></any>
       // v-if 是动态地向DOM树内添加或者删除DOM元素；
        <any v-if="表达式"></any>
       // v-show 是通过设置DOM元素的display样式属性控制显隐；
      ```
  - 指令-事件绑定
    - 语法
      ```
       <any v-on:eventName="handleEvent"></any>
       简写
       <any @eventName="handleEvent"></any>
      ```
  - 指令-属性绑定
    - 语法
           
           ```
            <any v-bind:myProp="表达式"></any>
            简写
            <any :myProp="表达式"></any>
            <img :src="'img/'+myImg">
            <p :style="{backgroundColor:myBGColor}">动态样式绑定</p>
            <h1 :class="{myRed:false}">动态样式类的绑定</h1>
            <h1 :class="[{myRed:false},'current']">动态样式类的绑定</h1>
           ```
  - 指令-双向数据绑定
    - <表单元素 v-model="变量"></表单元素>
  - 组件化(组件就是可被反复使用，带有特定功能的视图)
    - 组件的创建
      全局组件
      ```
       Vue.component('my-com',{
           template:`<h2> it is a header</h2>`
       })
      ```
      ```
       new Vue({
           components:{
               'my-footer':{
                   template:''
               }
           }
       })
      ```
    - 组件的使用
      ```
      <my-com></my-com>
      ```
    - 注意事项
       1.组件的id和使用方式，遵循烤串式命名方式：a-b-c
       2.如果一个组件要渲染多个元素，将多个元素放在一个顶层标签中，比如div，form
       3.全局组件可以用在id为example的范围内的任何一个组件内部，直接调用可以：但是局部组件只能在父模板中直接调用
  - 自定义指令
    - 创建和使用
      ```
       Vue.directive('change',{
         bind(){
         //首次调用
         },
         update(){
         //只要是有数据变化，都会调用
          }，
         unbind(){
         //解绑
         }
        })
      <any v-change="count"></any>
      ```
  - 自定义过滤器
    - 过滤器的创建和使用
     ```
      Vue.filter('myFilter',(myInput)=>{
        //myInput是在调用过滤器时，管道前表达式执行的结果
        //针对myInput，按照业务需求做处理
        return "处理后的结果"
      })
      <any>{{expression | myFilter}}</any>
     ```
    - 如何在调用过滤器时，完成参数的发送和接受
     ```
      1.发送
      <any>{{expression | myFilter(参数1,参数2)}}</any>
      2.接受
      Vue.filter('myFilter',()=>{
        return '处理后的结果'
      })
     ```
  - 复合组件
    - 在一个组件中直接来调用另外一个组件
     ```
      <my-list>
         <my-item></my-item>
      </my-list>
     ```
  - 组建创建方式
    ```
      1.直接在template属性中指定模板内容
         1.全局组件
         Vue.component
         2.局部组件
         {
           components:{
             'my-footer':{tempalte:``}
           }
         }
      2,.vue结尾的文件
        <template></template>
        <script></script>
        <style></style>
      3.单独指定一个模板内容
        <script id='myContent' type='text/x-template'>     
        </script>
        Vue.component('',{
          template:'#myContent'
        })
    ```
  - 生命周期
    - 四个阶段
      - create 准备工作 (数据的初始化)
      - mount  挂载前后针对元素进行操作
      - update 数据发生变化
      - destory 清理工作(关闭定时器，集合清空)

       - beforeCreate/created
       - beforeMount/mounted
       - beforeUpdate/updated
       - beforeDestrory/destroyed
  - 常用属性
    1. watch
       ```
        1.表单元素的双向数据绑定
        v-model="myValue"
        2.监听
        watch:{
          myValue(){  
          }
        }
       ```
    2. computed
       计算属性是用于在模板中，搞定复杂的业务逻辑，因为有依赖缓存
       ```
         1.指定计算属性
           computed:{
             myHandle(){
              return 数据
            }
          }
         2.调用
          <any>{{myHandle}}</any>
       ```
  - 组件间通信
    -  父与子通信
       ```
       1.发送
         <son myName="zhangshan"></son>
       2.接受
        到son组件
        Vue.component('son',{
          props:['myName'],
          template:`<p>{{myName}}</p>`
         })
       ```
    -  子与父通信
        ```
         1.绑定
          methods:{
           handleEvent(){

           }
         }
         <son @customEvent="handleEvent"></son>
         2.触发
          子组件内部
          this.$emit('customEvent',100)
        ```
    -  ref (reference 引用/参考 外号)
       帮助在父组件中，得到子组件中的数据，方法
       ```
        1.指定ref属性
        <son ref="mySon"></son>
        2.根据ref得到子组件实列
        this.$refs.mySon
       ```
    -  $parent
       ```
        this.$parent得到父组件的实例
       ```
    -  兄弟组件通信
       ```
        1.var bus=new Vue();
        2.接收方
        bus.$on('eventName',()=>{})
        3.发送方
        bus.$emit('eventName',123)
       ```
      
      