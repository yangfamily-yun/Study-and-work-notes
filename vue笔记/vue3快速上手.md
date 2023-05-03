# Vue3

数据代理：通过一个对象操作另一个对象的属性

响应式/数据劫持：用object.defineProperty（数据改变会调用get,set,deleteProperty）捕获/拦截数据的改变，重新解析页面。

如果get,set,deleteProperty只有对另一个对象属性的操作，它就只实现了数据代理。如果还包含了其他的的解析页面的操作，才是数据劫持（响应式）

- 2020年9月18日，Vue.js发布3.0版本，代号：One Piece（海贼王）
- 耗时2年多、[2600+次提交](https://github.com/vuejs/vue-next/graphs/commit-activity)、[30+个RFC](https://github.com/vuejs/rfcs/tree/master/active-rfcs)、[600+次PR](https://github.com/vuejs/vue-next/pulls?q=is%3Apr+is%3Amerged+-author%3Aapp%2Fdependabot-preview+)、[99位贡献者](https://github.com/vuejs/vue-next/graphs/contributors) 
- github上的tags地址：https://github.com/vuejs/vue-next/releases/tag/v3.0.0

## 1、基于setup配置项和语法糖setup的区别

vue2和vue3不变的使用：模板中依然用$attrs访问组件中为声明的数据（注意透传attribute）

- 如果是setup配置项

  （1）依然需要用component，props,emits配置项(vue2中不需要声明自定义事件，直接通过$emit触发事件)注册组件，声明组件接收的数据和自定义事件

  （2）必须要把数据、方法return，模板才可以使用

  （3）setup的参数

  - props：值为代理对象，包含：组件外部传递过来，且组件内部声明接收了的属性。如果有属性没接收，控制台将发出警告，属性将出现在context.attrs上

    ```
    props:['msg','school'],
    ```

  - context：普通上下文对象

    - attrs: 值为代理对象，包含：组件外部传递过来，但没有在props配置中声明的属性, 相当于 ```this.$attrs```。

    - slots: 收到的插槽内容（代理对象）, 相当于 ```this.$slots```（如果是匿名插槽就是this.$slots.default。如果是具名插槽，就是this.$slots.插槽名）

      注意：因为兼容的问题，通过【v-slot：插槽名】使用插槽

    - emit: 触发自定义事件的函数, 相当于 ```this.$emit```。

      注意：自定义事件必须在组件内部通过emits配置项声明接收

- 如果是语法糖<script setup>，

  （1）导入的组件直接使用，不需要注册。

  （2）通过defineProps，defineEmits，defineExpose 等编译宏命令（不需要导入，可以直接使用）声明接收的数据，自定义事件等（3）定义在内的数据、方法在模板中可以直接使用，不用return

  （4）使用useAttrs()代替setup方法中的context.attrs访问一个组件的所有透传 attribute

  （5）使用 `<script setup>` 的单文件组件会自动根据文件名生成对应的 `name` 选项，无需再手动声明。

## 2.Vue3带来了什么

### 1.性能的提升

- 打包大小减少41%

- 初次渲染快55%, 更新渲染快133%

- 内存减少54%

  ......

### 2.源码的升级（面试）

- 使用Proxy代替defineProperty实现响应式

- 重写虚拟DOM的实现和Tree-Shaking（去除不需要的代码，webpack可以实现）

  ......

### 3.拥抱TypeScript

- Vue3可以更好的支持TypeScript
- vue2也可以

### 4.新的特性(面试)

1. Composition API（组合API）

   - setup配置
   - ref与reactive
   - watch与watchEffect
   - provide与inject
   - ......
2. 新的内置组件
   - Fragment 
   - Teleport
   - Suspense
3. 其他改变

   - 新的生命周期钩子
   - data 选项应始终被声明为一个函数
   - 移除keyCode支持作为 v-on 的修饰符
   - ......

## 3.组合式函数（hooks）

### 3.1 和无渲染组件（插槽）对比

在组件插槽一章中，我们讨论过了基于作用域插槽的[无渲染组件](https://cn.vuejs.org/guide/components/slots.html#renderless-components)。我们甚至用它实现了一样的鼠标追踪器示例。

组合式函数相对于无渲染组件的主要优势是：组合式函数不会产生额外的组件实例开销。当在整个应用中使用时，由无渲染组件产生的额外组件实例会带来无法忽视的性能开销。

我们推荐在纯逻辑复用时使用组合式函数，在需要同时复用逻辑和视图布局时使用无渲染组件。

### 3.2 和mixin对比

mixins 有三个主要的短板：

1. **不清晰的数据来源**：当使用了多个 mixin 时，实例上的数据属性来自哪个 mixin 变得不清晰，这使追溯实现和理解组件行为变得困难。这也是我们推荐在组合式函数中使用 ref + 解构模式的理由：让属性的来源在消费组件时一目了然。
2. **命名空间冲突**：多个来自不同作者的 mixin 可能会注册相同的属性名，造成命名冲突。若使用组合式函数，你可以通过在解构变量时对变量进行重命名来避免相同的键名。
3. **隐式的跨 mixin 交流**：多个 mixin 需要依赖共享的属性名来进行相互作用，这使得它们隐性地耦合在一起。而一个组合式函数的返回值可以作为另一个组合式函数的参数被传入，像普通函数那样。

基于上述理由，我们不再推荐在 Vue 3 中继续使用 mixin。保留该功能只是为了项目迁移的需求和照顾熟悉它的用户。

## 4.vue2和vue3的自定义指令变化

（1）vue2

1.  指令名是abc-def方式。
2. 一个指令定义对象可以提供如下几个钩子函数 (均为可选)：
   - `bind`：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
   - `inserted`：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。
   - `update`：所在组件的 VNode 更新时调用，**但是可能发生在其子 VNode 更新之前**。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 (详细的钩子函数参数见下)。
3. 全局注册的回调必须是普通函数，确保this指向组件的实例对象

（2）vue3

1.  定义指令由一个包含类似组件生命周期钩子的对象来定义

    一个指令的定义对象可以提供几种钩子函数 (都是可选的)

   ```
   const myDirective = {
     // 在绑定元素的 attribute 前
     // 或事件监听器应用前调用
     created(el, binding, vnode, prevVnode) {
       // 下面会介绍各个参数的细节
     },
     // 在元素被插入到 DOM 前调用
     beforeMount(el, binding, vnode, prevVnode) {},
     // 在绑定元素的父组件
     // 及他自己的所有子节点都挂载完成后调用
     mounted(el, binding, vnode, prevVnode) {},
     // 绑定元素的父组件更新前调用
     beforeUpdate(el, binding, vnode, prevVnode) {},
     // 在绑定元素的父组件
     // 及他自己的所有子节点都更新后调用
     updated(el, binding, vnode, prevVnode) {},
     // 绑定元素的父组件卸载前调用
     beforeUnmount(el, binding, vnode, prevVnode) {},
     // 绑定元素的父组件卸载后调用
     unmounted(el, binding, vnode, prevVnode) {}
   }
   ```

   指令的钩子会传递以下几种参数：

   - `el`：指令绑定到的元素。这可以用于直接操作 DOM。
   - `binding`：一个对象，包含以下属性。
     - `value`：传递给指令的值。例如在 `v-my-directive="1 + 1"` 中，值是 `2`。
     - `oldValue`：之前的值，仅在 `beforeUpdate` 和 `updated` 中可用。无论值是否更改，它都可用。
     - `arg`：传递给指令的参数 (如果有的话)。例如在 `v-my-directive:foo` 中，参数是 `"foo"`。
     - `modifiers`：一个包含修饰符的对象 (如果有的话)。例如在 `v-my-directive.foo.bar` 中，修饰符对象是 `{ foo: true, bar: true }`。
     - `instance`：使用该指令的组件实例。
     - `dir`：指令的定义对象。
   - `vnode`：代表绑定元素的底层 VNode。
   - `prevNode`：之前的渲染中代表指令所绑定元素的 VNode。仅在 `beforeUpdate` 和 `updated` 钩子中可用。

2. 在 `<script setup>` 中，任何以 `v` 开头的驼峰式命名的变量都可以被用作一个自定义指令

   <script setup>
   // 在模板中启用 v-focus
   const vFocus = {
     mounted: (el) => el.focus()
   }
   </script>

   <template>
     <input v-focus />
   </template>

3. 在没有使用 `<script setup>` 的情况下，自定义指令需要通过 `directives` 选项注册：

   ```
   export default {
     setup() {
       /*...*/
     },
     directives: {
       // 在模板中启用 v-focus
       focus: {
         /* ... */
       }
     }
   }
   ```

4. 将一个自定义指令全局注册到应用层级也是一种常见的做法.

   因为setup没有this，所以回调可以是箭头函数

   ```
   const app = createApp({})
   
   // 使 v-focus 在所有组件中都可用
   app.directive('focus', {
     /* ... */
   })
   一个很常见的情况是仅仅需要在 mounted 和 updated 上实现相同的行为，除此之外并不需要其他钩子。这种情况下我们可以直接用一个函数来定义指令，
   app.directive('color', (el, binding) => {
     // 这会在 `mounted` 和 `updated` 时都调用
     el.style.color = binding.value
   })
   
   ```

   

   

# 一、创建Vue3.0工程

## 1.使用 vue-cli 创建

官方文档：https://cli.vuejs.org/zh/guide/creating-a-project.html#vue-create

```bash
## 查看@vue/cli版本，确保@vue/cli版本在4.5.0以上
vue --version  //Vue -V
## 安装或者升级你的@vue/cli
npm install -g @vue/cli
## 创建
vue create vue_test
## 启动
cd vue_test
npm run serve
```

## 2.使用 vite 创建

官方文档：https://v3.cn.vuejs.org/guide/installation.html#vite

vite官网：https://vitejs.cn

- 什么是vite？—— 新一代前端构建工具。
- 优势如下：
  - 开发环境中，无需打包操作，可快速的冷启动。
  - 轻量快速的热重载（HMR）。
  - 真正的按需编译，不再等待整个应用编译完成。
- 传统构建 与 vite构建对比图

<img src="https://cn.vitejs.dev/assets/bundler.37740380.png" style="width:500px;height:280px;float:left" /><img src="https://cn.vitejs.dev/assets/esm.3070012d.png" style="width:480px;height:280px" />

```bash
## 创建工程
npm init vite-app <project-name>
## 进入工程目录
cd <project-name>
## 安装依赖
npm install
## 运行
npm run dev
```

# 二、常用 Composition API

官方文档: https://v3.cn.vuejs.org/guide/composition-api-introduction.html

## 1.拉开序幕的setup

1. 理解：Vue3.0中一个新的配置项，值为一个函数。
2. setup是所有<strong style="color:#DD5145">Composition API（组合API）</strong><i style="color:gray;font-weight:bold">“ 表演的舞台 ”</i>。
4. 组件中所用到的：数据、方法等等，均要配置在setup中。
5. setup函数的两种返回值：
   1. 若返回一个对象，则对象中的属性、方法, 在模板中均可以直接使用。（重点关注！）
   2. <span style="color:#aad">若返回一个渲染函数：则可以自定义渲染内容。（了解）</span>
6. 注意点：
   1. 尽量不要与Vue2.x配置混用
      - Vue2.x配置（data、methos、computed...）中<strong style="color:#DD5145">可以访问到</strong>setup中的属性、方法。
      - 但在setup中<strong style="color:#DD5145">不能访问到</strong>Vue2.x配置（data、methods、computed...）。
      - 如果有重名, setup优先。
   2. setup不能是一个async函数，因为返回值不再是return的对象, 而是promise, 模板看不到return对象中的属性。（后期也可以返回一个Promise实例，但需要父组件使用Suspense和异步引入defineAysnProperty的配合）

##  2.ref函数

- 作用: 定义一个响应式的数据
- 语法: ```const xxx = ref(initValue)``` 
  - 创建一个包含响应式数据的<strong style="color:#DD5145">引用对象（reference对象，简称ref对象）</strong>。
  - JS中操作数据： ```xxx.value```
  - 模板中读取数据: 不需要.value，直接：```<div>{{xxx}}</div>```
- 备注：
  - 接收的数据可以是：基本类型、也可以是对象类型。
  - 基本类型的数据：响应式依然是靠``Object.defineProperty()``的```get```与```set```完成数据劫持（依旧用object.defineProperty捕获数据的改变，重新解析页面）的。
  - 对象类型的数据：内部 <i style="color:gray;font-weight:bold">“ 求助 ”</i> 了Vue3.0中的一个新函数—— ```reactive```函数。

## 3.reactive函数

- 作用: 定义一个<strong style="color:#DD5145">对象类型</strong>的响应式数据（基本类型不要用它，要用```ref```函数）
- 语法：```const 代理对象= reactive(源对象)```接收一个对象（或数组），返回一个<strong style="color:#DD5145">代理对象（Proxy的实例对象，简称proxy对象）</strong>
- reactive定义的响应式数据是“深层次的”。
- 内部基于 ES6 的 Proxy 实现，通过代理对象对源对象内部数据进行操作。

## 4.Vue3.0中的响应式原理

### vue2.x的响应式

- 实现原理：
  - 对象类型：通过```Object.defineProperty()```对属性的读取、修改进行拦截（数据劫持）。
  
  - 数组类型：通过重写更新数组的一系列方法来实现拦截。（对数组的变更方法进行了包裹）。
  
    ```js
    Object.defineProperty(data, 'count', {
        get () {}, 
        set () {}
    })
    ```

- 存在问题：
  - 新增属性、删除属性, 界面不会更新。
  - 直接通过下标修改数组, 界面不会自动更新。

### Vue3.0的响应式

- 实现原理: 
  - 通过Proxy（代理）:  拦截对象中任意属性的变化, 包括：属性值的读写、属性的添加、属性的删除等。
  - 通过Reflect（反射）:  对源对象的属性进行操作。
  - MDN文档中描述的Proxy与Reflect：
    - Proxy：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy
    
    - Reflect：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect
    
      ```js
      new Proxy(data, {
      	// 拦截读取属性值
          get (target, prop) {
          	return Reflect.get(target, prop)
          },
          // 拦截设置属性值或添加新属性
          set (target, prop, value) {
          	return Reflect.set(target, prop, value)
          },
          // 拦截删除属性
          deleteProperty (target, prop) {
          	return Reflect.deleteProperty(target, prop)
          }
      })
      
      proxy.name = 'tom'   
      ```

## 5.reactive对比ref

-  从定义数据角度对比：
   -  ref用来定义：<strong style="color:#DD5145">基本类型数据</strong>。
   -  reactive用来定义：<strong style="color:#DD5145">对象（或数组）类型数据</strong>。
   -  备注：ref也可以用来定义<strong style="color:#DD5145">对象（或数组）类型数据</strong>, 它内部会自动通过```reactive```转为<strong style="color:#DD5145">代理对象</strong>。
-  从原理角度对比：
   -  ref通过``Object.defineProperty()``的```get```与```set```来实现响应式（数据劫持）。
   -  reactive通过使用<strong style="color:#DD5145">Proxy</strong>来实现响应式（数据劫持）, 并通过<strong style="color:#DD5145">Reflect</strong>操作<strong style="color:orange">源对象</strong>内部的数据。
-  从使用角度对比：
   -  ref定义的数据：操作数据<strong style="color:#DD5145">需要</strong>```.value```，读取数据时模板中直接读取<strong style="color:#DD5145">不需要</strong>```.value```。
   -  reactive定义的数据：操作数据与读取数据：<strong style="color:#DD5145">均不需要</strong>```.value```。

## 6.setup的两个注意点!

- setup执行的时机
  - 在beforeCreate之前执行一次，this是undefined。
  
- setup的参数
  - props：值为代理对象，包含：组件外部传递过来，且组件内部声明接收了的属性。如果有属性没接收，控制台将发出警告，属性将出现在context.attrs上
  
    ```
    props:['msg','school'],
    ```
  
  - context：普通上下文对象
    - attrs: 值为代理对象，包含：组件外部传递过来，但没有在props配置中声明的属性, 相当于 ```this.$attrs```。
    
    - slots: 收到的插槽内容（代理对象）, 相当于 ```this.$slots```（如果是匿名插槽就是this.$slots.default。如果是具名插槽，就是this.$slots.插槽名）
    
      注意：因为兼容的问题，通过【v-slot：插槽名】使用插槽
    
    - emit: 触发自定义事件的函数, 相当于 ```this.$emit```。
    
      注意：自定义事件必须在组件内部通过emits配置项声明接收
    
      ```
      emits:['hello']
      ```


## 7.计算属性与监视

### 1.computed函数

- 与Vue2.x中computed配置功能一致

- 写法

  ```js
  import {computed} from 'vue'
  
  setup(){
      ...
  	//计算属性——简写
      let fullName = computed(()=>{
          return person.firstName + '-' + person.lastName
      })
      //计算属性——完整
      let fullName = computed({
          get(){
              return person.firstName + '-' + person.lastName
          },
          set(value){
              const nameArr = value.split('-')
              person.firstName = nameArr[0]
              person.lastName = nameArr[1]
          }
      })
  }
  ```

### 2.watch函数

- 与Vue2.x中watch配置功能一致

- 两个小“坑”：

  - 监视reactive定义的所有的响应式数据时：oldValue无法正确获取、强制开启了深度监视（deep配置失效）。
  
  - 监视reactive定义的响应式数据中某个属性时：deep配置有效，oldValue无法正确获取。
  
  - 如果ref接收是基本数据类型，则不用person.value，否则监测的将是字面量。如果监视的ref对象中的reactive定义的数据，有两种办法解决。
  
    ```
       // watch(person.value, (newValue, oldValue) => {
        //   console.log("person的值变化了", newValue, oldValue);
        // });
        watch(
          person,
          (newValue, oldValue) => {
            console.log("person的值变化了", newValue, oldValue);
          },
          { deep: true }
        );
    ```
  
    
  
  ```js
   //情况一：监视ref所定义的一个响应式数据
      /* watch(sum,(newValue,oldValue)=>{
  				console.log('sum变了',newValue,oldValue)
  			},{immediate:true}) */
  
      //情况二：监视ref所定义的多个响应式数据
      /* watch([sum,msg],(newValue,oldValue)=>{
  				console.log('sum或msg变了',newValue,oldValue)
          // newValue,oldValue都是数组，对应着数组中被监视的属性newValue,oldValue
  			},{immediate:true}) */
  
      /*
  				情况三：监视reactive所定义的一个响应式数据的全部属性
  						1.注意：此处无法正确的获取oldValue，值和newValue相同
  						2.注意：强制开启了深度监视（deep配置无效）
  			*/
      /* watch(person,(newValue,oldValue)=>{
  				console.log('person变化了',newValue,oldValue)
  			},{deep:false}) //此处的deep配置无效 */
  
      //情况四：监视reactive所定义的一个响应式数据中的某个属性，可以得到正确的oldValue
      /* watch(()=>person.name,(newValue,oldValue)=>{
  				console.log('person的name变化了',newValue,oldValue)
  			})  */
  
      //情况五：监视reactive所定义的一个响应式数据中的某些属性
      // 这种方式可以获取oldvalue
      /* watch([()=>person.name,()=>person.age],(newValue,oldValue)=>{
  				console.log('person的name或age变化了',newValue,oldValue)
  			})  */
  
      //特殊情况
      // 同样无法正确的获取oldValue
      /* watch(()=>person.job,(newValue,oldValue)=>{
  				console.log('person的job变化了',newValue,oldValue)
  			},{deep:true}) //此处由于监视的是reactive素定义的对象中的某个属性（值为引用数据类型），所以deep配置有效 */
  ```



### 3.watchEffect函数!

- watch的套路是：既要指明监视的属性，也要指明监视的回调。

- watchEffect的套路是：不用指明监视哪个属性，监视的回调中用到哪个属性，那就监视哪个属性。

- watchEffect创建监听器时，立即执行回调。watch是懒执行，仅当依赖的响应式状态变化时，才会执行回调

- watchEffect有点像computed：

  - 但computed注重的计算出来的值（回调函数的返回值），所以必须要写返回值。
  - 而watchEffect更注重的是过程（回调函数的函数体），所以不用写返回值。

  ```js
  //watchEffect所指定的回调中用到的数据只要发生变化，则直接重新执行回调。
  watchEffect(()=>{
      const x1 = sum.value
      const x2 = person.age
      console.log('watchEffect配置的回调执行了')
  })
  ```

## 8.生命周期

<div style="border:1px solid black;width:380px;float:left;margin-right:20px;"><strong>vue2.x的生命周期</strong><img src="https://cn.vuejs.org/images/lifecycle.png" alt="lifecycle_2" style="zoom:33%;width:1200px" /></div><div style="border:1px solid black;width:510px;height:985px;float:left"><strong>vue3.0的生命周期</strong><img src="https://v3.cn.vuejs.org/images/lifecycle.svg" alt="lifecycle_2" style="zoom:33%;width:2500px" /></div>



















































- Vue3.0中可以继续使用Vue2.x中的生命周期钩子，但有有两个被更名：
  - ```beforeDestroy```改名为 ```beforeUnmount```
  - ```destroyed```改名为 ```unmounted```
  - 无法使用beforeDestroy,destroyed
- Vue3.0也提供了 Composition API 形式的生命周期钩子，与Vue2.x中钩子对应关系如下：
  - `beforeCreate`===>`setup()`
  - `created`=======>`setup()`
  - `beforeMount` ===>`onBeforeMount`
  - `mounted`=======>`onMounted`
  - `beforeUpdate`===>`onBeforeUpdate`
  - `updated` =======>`onUpdated`
  - `beforeUnmount` ==>`onBeforeUnmount`
  - `unmounted` =====>`onUnmounted`
- 如果两种方式都用，组合式api的执行时机会更早

## 9.自定义hook函数

- 什么是hook？—— 本质是一个函数，把setup函数中使用的Composition API进行了封装。
- 类似于vue2.x中的mixin。
- 自定义hook的优势: 复用代码, 让setup中的逻辑更清楚易懂。
- 主要对某个属性的操作

## 10.toRef

- 作用：创建一个 ref 对象，其value值指向另一个对象中的某个属性。
- 语法：```const name = toRef(person,'name')```
- 应用:   要将响应式对象中的某个属性单独提供给外部使用时。


- 扩展：```toRefs``` 与```toRef```功能一致，但可以批量创建多个 ref 对象，语法：```toRefs(person)```


# 三、其它 Composition API

## 1.shallowReactive 与 shallowRef

- shallowReactive：只处理对象最外层属性的响应式（浅响应式）。
- shallowRef：只处理基本数据类型的响应式, 不进行对象的响应式处理。

- 什么时候使用?
  -  如果有一个对象数据，结构比较深, 但变化时只是外层属性变化 ===> shallowReactive。
  -  如果有一个对象数据，后续功能不会修改该对象中的属性，而是生新的对象来替换 ===> shallowRef。

## 2.readonly 与 shallowReadonly

- readonly: 让一个响应式数据变为只读的（深只读）。
- shallowReadonly：让一个响应式数据变为只读的（浅只读）。
- 应用场景: 不希望数据被修改时。

## 3.toRaw 与 markRaw

- toRaw：
  - 作用：将一个由```reactive```生成的<strong style="color:orange">响应式对象</strong>转为<strong style="color:orange">普通对象</strong>。
  - 使用场景：用于读取响应式对象对应的普通对象，对这个普通对象的所有操作，不会引起页面更新。
- markRaw：
  - 作用：标记一个对象，使其永远不会再成为响应式对象。
  - 应用场景:
    1. 有些值不应被设置为响应式的，例如复杂的第三方类库等。
    2. 当渲染具有不可变数据源的大列表时，跳过响应式转换可以提高性能。

## 4.customRef

- 作用：创建一个自定义的 ref，并对其依赖项跟踪和更新触发进行显式控制。

- 实现防抖效果：

  ```vue
  <template>
  	<input type="text" v-model="keyword">
  	<h3>{{keyword}}</h3>
  </template>
  
  <script>
  	import {ref,customRef} from 'vue'
  	export default {
  		name:'Demo',
  		setup(){
  			// let keyword = ref('hello') //使用Vue准备好的内置ref
  			//自定义一个myRef
  			function myRef(value,delay){
  				let timer
  				//通过customRef去实现自定义
  				return customRef((track,trigger)=>{
  					return{
  						get(){
  							track() //告诉Vue这个value值是需要被“追踪”的
  							return value
  						},
  						set(newValue){
  							clearTimeout(timer)
  							timer = setTimeout(()=>{
  								value = newValue
  								trigger() //告诉Vue去更新界面
  							},delay)
  						}
  					}
  				})
  			}
  			let keyword = myRef('hello',500) //使用程序员自定义的ref
  			return {
  				keyword
  			}
  		}
  	}
  </script>
  ```

  

## 5.provide 与 inject

<img src="https://v3.cn.vuejs.org/images/components_provide.png" style="width:300px" />

- 作用：实现<strong style="color:#DD5145">祖与后代组件间</strong>通信

- 套路：父组件有一个 `provide` 选项来提供数据，后代组件有一个 `inject` 选项来开始使用这些数据

- 具体写法：

  1. 祖组件中：

     ```js
     setup(){
     	......
         let car = reactive({name:'奔驰',price:'40万'})
         provide('car',car)
         ......
     }
     ```

  2. 后代组件中：

     ```js
     setup(props,context){
     	......
         const car = inject('car')
         return {car}
     	......
     }
     ```

## 6.响应式数据的判断

- isRef: 检查一个值是否为一个 ref 对象
- isReactive: 检查一个对象是否是由 `reactive` 创建的响应式代理
- isReadonly: 检查一个对象是否是由 `readonly` 创建的只读代理
- isProxy: 检查一个对象是否是由 `reactive` 或者 `readonly` 方法创建的代理

# 四、Composition API 的优势

## 1.Options API 存在的问题

使用传统OptionsAPI中，新增或者修改一个需求，就需要分别在data，methods，computed里修改 。

<div style="width:600px;height:370px;overflow:hidden;float:left">
    <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f84e4e2c02424d9a99862ade0a2e4114~tplv-k3u1fbpfcp-watermark.image" style="width:600px;float:left" />
</div>
<div style="width:300px;height:370px;overflow:hidden;float:left">
    <img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e5ac7e20d1784887a826f6360768a368~tplv-k3u1fbpfcp-watermark.image" style="zoom:50%;width:560px;left" /> 
</div>


















## 2.Composition API 的优势

我们可以更加优雅的组织我们的代码，函数。让相关功能的代码更加有序的组织在一起。

<div style="width:500px;height:340px;overflow:hidden;float:left">
    <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bc0be8211fc54b6c941c036791ba4efe~tplv-k3u1fbpfcp-watermark.image"style="height:360px"/>
</div>
<div style="width:430px;height:340px;overflow:hidden;float:left">
    <img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6cc55165c0e34069a75fe36f8712eb80~tplv-k3u1fbpfcp-watermark.image"style="height:360px"/>
</div>













# 五、新的组件

## 1.Fragment

- 在Vue2中: 组件必须有一个根标签
- 在Vue3中: 组件可以没有根标签, 内部会将多个标签包含在一个Fragment虚拟元素中
- 好处: 减少标签层级, 减小内存占用

## 2.Teleport

- 什么是Teleport？—— `Teleport` 是一种能够将我们的<strong style="color:#DD5145">组件html结构</strong>移动到指定位置的技术。

  ```vue
  <teleport to="移动位置">
  	<div v-if="isShow" class="mask">
  		<div class="dialog">
  			<h3>我是一个弹窗</h3>
  			<button @click="isShow = false">关闭弹窗</button>
  		</div>
  	</div>
  </teleport>
  ```

## 3.Suspense

- 等待异步组件时渲染一些额外内容，让应用有更好的用户体验

- 使用步骤：父组件

  - 异步引入组件

    ```js
    import {defineAsyncComponent} from 'vue'
    const Child = defineAsyncComponent(()=>import('./components/Child.vue'))
    ```

  - 使用```Suspense```包裹组件，并配置好```default``` 与 ```fallback```

    ```vue
    <template>
    	<div class="app">
    		<h3>我是App组件</h3>
    		<Suspense>
    			<template v-slot:default>
    				<Child/>
    			</template>
    			<template v-slot:fallback>
    				<h3>加载中.....</h3>
    			</template>
    		</Suspense>
    	</div>
    </template>
    ```

子组件的setup可以用Async

# 六、其他

## 1.全局API的转移

- Vue 2.x 有许多全局 API 和配置。
  - 例如：注册全局组件、注册全局指令等。

    ```js
    //注册全局组件
    Vue.component('MyButton', {
      data: () => ({
        count: 0
      }),
      template: '<button @click="count++">Clicked {{ count }} times.</button>'
    })
    
    //注册全局指令
    Vue.directive('focus', {
      inserted: el => el.focus()
    }
    ```

- Vue3.0中对这些API做出了调整：

  - 将全局的API，即：```Vue.xxx```调整到应用实例（```app```）上

    | 2.x 全局 API（```Vue```） | 3.x 实例 API (`app`)                        |
    | ------------------------- | ------------------------------------------- |
    | Vue.config.xxxx           | app.config.xxxx                             |
    | Vue.config.productionTip  | <strong style="color:#DD5145">移除</strong> |
    | Vue.component             | app.component                               |
    | Vue.directive             | app.directive                               |
    | Vue.mixin                 | app.mixin                                   |
    | Vue.use                   | app.use                                     |
    | Vue.prototype             | app.config.globalProperties                 |
  

## 2.其他改变

- data选项应始终被声明为一个函数，非函数将报错。在vue2中，data为对象，虽然可能对象存在数据引用的关系，也不会报错，

- 过度类名的更改：

  - Vue2.x写法

    ```css
    .v-enter,
    .v-leave-to {
      opacity: 0;
    }
    .v-leave,
    .v-enter-to {
      opacity: 1;
    }
    ```

  - Vue3.x写法

    ```css
    .v-enter-from,
    .v-leave-to {
      opacity: 0;
    }
    
    .v-leave-from,
    .v-enter-to {
      opacity: 1;
    }
    ```

- <strong style="color:#DD5145">移除</strong>keyCode作为 v-on 的修饰符，同时也不再支持```config.keyCodes```

- <strong style="color:#DD5145">移除</strong>```v-on.native```修饰符

  - 父组件中绑定事件

    ```vue
    <my-component
      v-on:close="handleComponentEvent"
      v-on:click="handleNativeClickEvent"
    />
    ```

  - 子组件中声明自定义事件，没有声明的事件为原生事件

  - 在vue2中，默认给组件绑定的事件都是自定义事件，不用声明自定义事件，直接使用$emit触发

    ```vue
    <script>
      export default {
        emits: ['close']
      }
    </script>
    ```

- <strong style="color:#DD5145">移除</strong>过滤器（filter）

  > 过滤器虽然这看起来很方便，但它需要一个自定义语法，打破大括号内表达式是 “只是 JavaScript” 的假设，这不仅有学习成本，而且有实现成本！建议用方法调用或计算属性去替换过滤器。

- ......