

[TOC]

1、vue 是核心技术版本，vue 脚手架是标准化开发工具

2、$mount,$watch,$set(Set),$children,$refs,$destory,$nextTick，$emit,$on,$once,$store，$route,$routers

​      Vue.filter,Vue.directive,Vue.mixin，Vue.compopnent

3、库：nanoid，pubsub-js，day.js，loader-less（注意考虑脚手架的版本）,axios，

​      插件：vue-resource，vuex（注意考虑脚手架的版本），vue-router（注意考虑脚手架的版本）

4、解析顺序：

从入口文件开始解析，脚手架会把 import 语句进行整理，把所有的 import 语句执行完毕，在执行其他语句。即使在中间穿插了其他的语句也没有用。

vue 文件的解析：先从 script 开始解析，在解析 HTML 结构

函数体执行完毕后（修改 data 数据），才会重新解析页面。如果在函数中，要基于更新后的 dom 进行操作，需要用 nextTick 函数。例如：获取焦点等

5、vue是数据驱动页面

（1）结构相同，数据不同。结构在子组件，数据在父组件传递给子组件

（2）数据相同，结构不同。数据在子组件，结构在父组件。需要用到作用域插槽，将组件的数据传递给插槽的使用者

（3）数据不同，结构不同。数据和结构都在父组件中，通过插槽在子组件的指定位置插入结构

6、数据在哪，修改数据的方法就在那

# import 导入样式和 link 导入样式的区别

import 导入的样式，vue 会进行严格的检查，如果样式中用到的内容，项目中没有，就会报错

而 link 导入的 css，不会进行检查，使用即使文件的内容无法在工程中找到也不会报错

# 组件间通信的方式

## 1、prop

   （1）父=>子

   （2）子=>父：父组件先给子组件传递函数，子组件通过prop接收函数。子组件把需要传递的数据作为参数调用函数，完成组件间的通信

## 2、自定义事件:子=>父

给组件绑定的事件默认为自定义事件，如果想使用原生事件，需要用native事件修饰符

2.1 组件绑定自定义事件的两种方式

   （1）通过@给组件绑定自定义事件

   （2）通过给组件的实例对象（$ref.名）调用$on（”事件名“，回调）方法绑定事件。

​      注意：回调必须是箭头函数，或者是当前组件的方法（this.方法名），只有这样，this才会指向当前的组件实例对象，才能对this上的数据进行操作。

2.2 触发事件：$emit("事件名"，实参)

2.3 解绑事件：$off("事件名")。如果不指定事件名，将会解绑所有的事件。解绑多个事件时，实参为数组

核心：通过给函数传递参数实现组件间的通信

注意：事件绑定在组件上，当组件销毁时，自定义事件也随之销毁，所以也可以不解绑事件

## 3、全局事件总线

1. 一种组件间通信的方式，适用于<span style="color:red">任意组件间通信</span>。

   ![image-20220311214402340](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20220311214402340.png)

2. 安装全局事件总线：

   （1）要让所有的组件可以看到这个傀儡，组件实例对象（vc）/vm 可以访问到 Vue 原型上的属性、方法。Vue.prototype.$bus

   （2）同时要让傀儡满足第二个条件（有$on,$emit,$off 方法，这些方法都在 vue 的原型上，所以这个傀儡的类型必须是 vm,vc(都可以访问 vue 的原型对象)

   $bus 就是当前应用的 vc

   ```javascript
   const vc = Vue.extend({}); //这是一个vuecomponent类型的构造函数
   const d = new vc(); //实例化对象
   Vue.prototype.$bus = d;
   ```

   $bus 就是当前应用的 vm

   ```js
   new Vue({
   	......
   	beforeCreate() {
   		Vue.prototype.$bus = this //安装全局事件总线，$bus就是当前应用的vm
   	},
       ......
   })
   ```

3. 使用事件总线：

   1. 接收数据：A 组件想接收数据，则在 A 组件中给$bus 绑定自定义事件，事件的<span style="color:red">回调留在 A 组件自身。</span>

      ```js
      methods(){
        demo(data){......}
      }
      ......
      mounted() {
        this.$bus.$on('xxxx',this.demo)
      }
      ```

   2. 提供数据：`this.$bus.$emit('xxxx',数据)`

4. 最好在 beforeDestroy 钩子中，用$off 去解绑<span style="color:red">当前组件所用到的</span>事件，这个事件会在傀儡中消失。如果不解绑，当组件销毁后，这个事件还会在傀儡中占有位置，浪费资源（组件都消失了，这个事件失去作用了）

5. 核心：通过给函数传递参数实现组件间的通信

## 4、消息的订阅与发布

一种组件间通信的方式，适用于<span style="color:red">任意组件间通信</span>。

1. 使用步骤：

       1.  安装 pubsub：`npm i pubsub-js`
       // pubsub是一个对象
       2.  引入: `import pubsub from 'pubsub-js'`
       
       3.  接收数据：A 组件想接收数据，则在 A 组件中订阅消息，订阅的<span style="color:red">回调留在 A 组件自身。</span>
       
           ```js
           methods(){
             demo(data){......}
           }
           ......
           mounted() {
             this.pid = pubsub.subscribe('xxx',this.demo) //订阅消息，返回消息的id，为了取消订阅，所以把id添加到对象的属性中，否则由于作用域，id将无法传输出去
             this.pubId = pubsub.subscribe('hello',(msgName,data)=>{
                 //回调函数中：第一个参数指的是消息名，第二个参数指的是消息
           			console.log(this)
           			// console.log('有人发布了hello消息，hello消息的回调执行了',msgName,data)
           			})
           }
           ```
       
       4.  提供数据：`pubsub.publish('xxx',数据)`
       
           第一个参数指的是消息名，第二个参数指的是消息
       
       5.  最好在 beforeDestroy 钩子中，用`PubSub.unsubscribe(pid)`去<span style="color:red">取消订阅。</span>

   注意：消息的订阅与发布不能在 vue 开发者工具中进行观察，因为 pubsub 库不是属于 vue 的。vue 开发者工具可以观察全局事件总线以及自定义事件

## 5、插槽

以上四种都是组件间**数据**的通信方式，而插槽是组件间**结构**的通信方式

让父组件可以向子组件指定位置插入 html 结构，**非 HTML 元素不传递**。也是一种组件间通信的方式，适用于 <strong style="color:red">父组件 ===> 子组件</strong> 。

1. 如果样式写在父组件中（解析结构的同时带着样式给组件），如果没写，只把结构给组件，样式被组件控制

2. 分类：默认插槽、具名插槽、作用域插槽

3. 使用方式：

   1. 默认插槽：

      ```vue
      父组件中：
      <Category>
                 <div>html结构1</div>
              </Category>
      子组件中：
      <template>
        <div>
          <!-- 定义插槽 -->
          <slot>插槽默认内容...</slot>
        </div>
      </template>
      ```

   2. 具名插槽：

      ```vue
      父组件中：
      <Category>
                  <template slot="center">
                    <div>html结构1</div>
                  </template>
      
                  <template v-slot:footer>
                     <div>html结构2</div>
                  </template>
              </Category>
      子组件中：
      <template>
        <div>
          <!-- 定义插槽 -->
          <slot name="center">插槽默认内容...</slot>
          <slot name="footer">插槽默认内容...</slot>
        </div>
      </template>
      ```

   3. 作用域插槽（也可以有命中）：

      1. 理解：<span style="color:red">数据在组件的自身，但根据数据生成的结构需要组件的使用者来决定。</span>（games 数据在 Category 组件中，但使用数据所遍历出来的结构由 App 组件决定）

      2. 具体编码：

         ```vue
         父组件中：
         <Category>
         			<template scope="scopeData">
         				<!-- 生成的是ul列表 -->
         				<ul>
         					<li v-for="g in scopeData.games" :key="g">{{g}}</li>
         				</ul>
         			</template>
         		</Category>
         
         <Category>
         			<template slot-scope="scopeData">
         				<!-- 生成的是h4标题 -->
         				<h4 v-for="g in scopeData.games" :key="g">{{g}}</h4>
         			</template>
         		</Category>
         子组件中：
         <template>
           <div>
             <slot :games="games"></slot>
           </div>
         </template>
         
         <script>
         export default {
           name: "Category",
           props: ["title"],
           //数据在子组件自身
           data() {
             return {
               games: ["红色警戒", "穿越火线", "劲舞团", "超级玛丽"],
             };
           },
         };
         </script>
         ```


## 6、vuex

在 Vue 中实现集中式状态（数据）管理的一个 Vue 插件，对 vue 应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于**任意组件间通信**。

# 发送 Ajax 请求的方式

## 1、XmlHttpRespond

js原生可以直接用

## 2、jquery 

大部分内容都是直接操作真实的 dom，违背了 vue 和 react 不直接操作 dom 元素的原则，所以很少使用这种方式发送请求

## 3、axios 

库 ，常用的发送 Ajax 请求的方式，promise 风格，可以请求拦截和响应拦截

(1)下载库

```
npm i axios
```

(2)在使用库的组件上导入库

```
import axios from 'axios'
```

(3)使用库

## 4、fetch 

和 axios 类似，但并不常用

基于promise的----兼容性问题（ie） ----js原生

## 5、vue-resource

 插件，vue1.x 常用的方式，官方已不维护，现已交给别家维护，vue 官方推荐使用 axios。使用这个插件，会在 vc,vm 上添加$http 属性，发送请求的方法、得到数据以及对数据的处理与 axios 相同，

（1）下载插件

```
npm i vue-resource
```

（2）在 vm 上导入插件

```
//引入插件
import vueResource from 'vue-resource'
```

（3）在 vm 上使用插件

```
Vue.use(vueResource)
```

# 服务器跨域的解决方法

## 1、后端添加 cors 请求头

## 2、jsonp

## 3、配置代理服务器

作为请求的中转站，这个服务器与本地服务器符合同源策略，不会产生跨域的问题，具体方法见下“vue 脚手架配置代理”

# 一、脚手架文件结构

    ├── node_modules
    ├── public
    │   ├── favicon.ico: 页签图标
    │   └── index.html: 主页面
    ├── src
    │   ├── assets: 存放静态资源
    │   │   └── logo.png
    │   │── component: 存放组件
    │   │   └── HelloWorld.vue
    │   │── App.vue: 汇总所有组件
    │   │── main.js: 入口文件
    ├── .gitignore: git版本管制忽略的配置
    ├── babel.config.js: babel的配置文件
    ├── package.json: 应用包配置文件
    ├── README.md: 应用描述文件
    ├── package-lock.json：包版本控制文件

## 1.1 关于不同版本的 Vue

1. vue.js 与 vue.runtime.xxx.js 的区别：
   1. vue.js 是完整版的 Vue，包含：核心功能 + 模板解析器。
   2. vue.runtime.xxx.js 是运行版的 Vue，只包含：核心功能；没有模板解析器。
2. 因为 vue.runtime.xxx.js 没有模板解析器，所以不能使用 template 这个配置项，需要使用 render 函数接收到的 createElement 函数去指定具体内容。

3. 组件是使用的 template 标签，vue 有专门的库解决（在 package.json 文件的"devDependencies"配置项的"vue-template-compiler"属性）

4. 一直使用 vue 完整版的问题：经 webpack 打包(它能将.vue 文件翻译为.js 文件，且把可以解析的都解析了，浏览器可以认识)，这时候打包的完整版 vue 的模板解析器就已经没有作用了，而且还占空间，打包后的文件大

   ### 精简版与完整版的使用

   实际上 vue 文件下有多个版本的 vue，但是默认导入精简版的 vue

   使用精简的 vue 时：由于没有模板解析器，需要在 vue 实例化时，添加 render 函数（参数是 createElement 可以简写为任意字母）

   要想使用完整的 vue 有两个办法:

   （1）在导入时写出完整版的详细路径，所有版本的 vue 都在 vue 文件下的 dist 文件(vue.js 是完整版 vue)

   （2）crtl+点击 vue 跳转 vue 文件；找到 package.json 文件，在第六行修改默认的 vue 版本（不推荐，这将修改官方库）

## 1.2 vue.config.js 配置文件

1. 使用 vue inspect > output.js 可以查看到 Vue 脚手架的默认配置。修改无效
2. 使用 vue.config.js 可以对脚手架进行个性化定制，详情见：https://cli.vuejs.org/zh

### 1.2.1 语法检查

将代码不合理的地方检查出来，例如定义了变量，函数却没有使用

webpack 的语法检查：eslint，jslint，jshint
每个公司的语法检查不一样
如何关语法检查：
在 vue.config.js 文件中设置 lintOnSave 配置项（平级），具体看 vue 官网->生态系统->vue cli->配置参考

### 1.2.2 程序入口文件

### 1.2.3 配置代理服务器

```
module.exports = {
  pages: {
    index: {
      //程序入口
      entry: 'src/main.js',
    },
  },
	lintOnSave:false, //关闭语法检查
	//开启代理服务器（方式一）
  // 缺点：（1）只能配置一个代理服务器（2）不能灵活控制走不走代理。
  // get请求会先在自身服务器寻找资源（public中的数据都是），没有在发送请求
	/* devServer: {
    proxy: 'http://localhost:5000'
  }, */
	//开启代理服务器（方式二）
	devServer: {
    proxy: {
      '/atguigu': {
        target: 'http://localhost:5000',
				pathRewrite:{'^/atguigu':''},
        // ws: true, //用于支持websocket
        // changeOrigin: true //用于控制请求头中的host值
      },
      '/demo': {
        target: 'http://localhost:5001',
				pathRewrite:{'^/demo':''},
        // ws: true, //用于支持websocket
        // changeOrigin: true //用于控制请求头中的host值
      }
    }
  }
}
```



# 二、ref 属性

1. 被用来给元素或子组件注册引用信息（id 的替代者）
2. 应用在 html 标签上获取的是真实 DOM 元素，效果与 document.getByElement 相同，应用在组件标签上获取的是组件实例对象（vc）。通过document.getByElement 获取组件，得到组件真实的dom结构
3. 使用方式：
   1. 打标识：`<h1 ref="xxx">.....</h1>` 或 `<School ref="xxx"></School>`
   2. 获取：`this.$refs.xxx`

# 三、props 配置项

1.  功能：让组件接收外部传过来的数据。组件结构不变，数据由组件的使用者决定。父=>子

2.  传递数据：`<Demo name="xxx"/>`

    如果传入的数据有 number 类型，并且想对他进行操作，应有用数据动态变化的指令。如果不这样，vc 接收的就是字符串，在 vue 模板操作数据时，页面会显示结果，但是控制台也会报错 v-bind

3.  接收数据：

    1. 第一种方式（只接收）：`props:['name'] `

    2. 第二种方式（限制类型）：`props:{name:String}`

    3. 第三种方式（限制类型、限制必要性、指定默认值）：

             ```js
             props:{
             	name:{
             	type:String, //类型
             	required:true, //必要性
             	default:'老王' //默认值
             	}
             }
             ```

       如果 data 中的数据和 prop 由重复的，prop 的优先级更高，data 的数据将会被覆盖

       > 备注：props 是只读的，Vue 底层会监测你对 props 的修改，如果进行了修改，就会发出警告，若业务需求确实需要修改，那么请复制 props 的内容到 data 中一份，然后去修改 data 中的数据。如果改变的是对象的内部属性，vue 不会报错，只有试图修改对象（地址）时，才会报错
    
3.  子=>父。父组件先给子组件传递函数，子组件通过prop接收函数。子组件把需要传递的数据作为参数调用函数，完成组件间的通信

    

# 三、mixin(混入)

1. 功能：可以把多个组件共用的配置想提取成一个混入对象

1. 混合对象如果与组件的 data、methods 配置项有相同的属性，那么将会使用组件本身的属性（覆盖混入对象的属性）；如果混入对象与组件都有生命周期函数（mounted，其他还没实验），那么都将使用，结果是 double，先使用混入的钩子，后自身的钩子

1. 使用方式：

   第一步定义混合(新建 xxx.js 文件，注意使用 export 将文件暴露)：

   ```javascript
   //分别暴露，引用通过 export {xxx,xxx} from 文件路径
   export const hunhe = {
   	methods: {
   		showName(){
   			alert(this.name)
   		}
   	},
   	mounted() {
   		console.log('你好啊！')
   	},
   }
   export const hunhe2 = {
   	data() {
   		return {
   			x:100,
   			y:200
   		}
   	},
   }
   {
       data(){....},
       methods:{....}
       ....
   }
   ```

   第二步使用混入：

   全局混入（在 main.js 文件中导入混合文件）：`Vue.mixin(xxx)`

   ```JavaScript
   import {hunhe,hunhe2} from './mixin'
   Vue.mixin(hunhe)
   Vue.mixin(hunhe2)
   //全局混入会挂载所有的组件以及vue本身
   ```

   局部混入：`mixins:[xxx，xxx]	`（配置项）

   ```JavaScript
   import {hunhe,hunhe2} from '../mixin'
   	export default {
   		name:'Student',
   		data() {
   			return {
   				name:'张三',
   				sex:'男'
   			}
   		},
   		mixins:[hunhe,hunhe2]
   	}
   ```

# 四、插件

1. 功能：用于增强 Vue

2. 本质：包含 install 方法的一个对象，install 的第一个参数是 Vue，第二个以后的参数是插件使用者传递的数据。

3. 定义插件：

   ```js
   export default {
   	install(Vue,x,y,z){
       // 1. 添加全局过滤器
       Vue.filter(....)

       // 2. 添加全局指令
       Vue.directive(....)

       // 3. 配置全局混入(合)
       Vue.mixin(....)

       // 4. 添加实例方法
       Vue.prototype.$myMethod = function () {...}
       Vue.prototype.$myProperty = xxxx
   }
   ```

4. 使用插件：以下操作都在 main.js 文件中，导入插件 export 插件 from 路径；Vue.use(插件明，实参，xxx)

5. 插件中的所有操作可以在组件中使用（原因是在 main.js 文件中全局引入使用）

# 五、scoped 样式

1. 作用：让样式在局部生效，防止冲突。本质上是为标签添加一个随机生成的属性
2. 写法：`<style scoped lang="css(less)">`lang 写 css 或者 less 预编译语言，不写 lang 属性，默认使用 css.如果没下 less，就在 vs 的终端 npm i less-loader@[1-8],不@就安装默认版本。可以通过 npm view less-loader(webpack) version 查看安装的版本，如果两者版本不适合，会报错

# 六、总结 TodoList 案例

1. 组件化编码流程：

   (1).拆分静态组件：组件要按照功能点拆分，命名不要与 html 元素冲突。

   (2).实现动态组件：考虑好数据的存放位置，数据是一个组件在用，还是一些组件在用：

   1).一个组件在用：放在组件自身即可。

   2). 一些组件在用：放在他们共同的父组件上（<span style="color:red">状态提升</span>）。

   (3).实现交互：从绑定事件开始。

2. props 适用于：

   (1).父组件 ==> 子组件 通信

   (2).子组件 ==> 父组件 通信（要求父先给子一个函数）

3. 使用 v-model 传入布尔值+表单属性为 checked 时要切记：v-model 绑定的值不能是 props 传过来的值，因为 props 是不可以修改的！

4. props 传过来的若是对象类型的值，修改对象中的属性时 Vue 不会报错，但不推荐这样做。

5. 注意全选、全不选的技巧；使用 v-model 传入布尔值+表单属性为 checked，这个时候可以用 vc 的计算属性，通过计算属性的 get 方法得到已完成与总数的关系，从而判断是否全选；通过 set 方法可以判断是否全选，从而修改 item 的 checked 属性

6. 注意当没有数据的时候，footer 不显示的技巧 ；

7. 注意计算已完成数量的技巧

# 七、webStorage

1.  存储内容大小一般支持 5MB 左右（不同浏览器可能还不一样）

2.  浏览器端通过 Window.sessionStorage 和 Window.localStorage 属性来实现本地存储机制。

3.  相关 API：

    1.  `xxxxxStorage.setItem('key', 'value');`
        该方法接受一个键和值作为参数，会把键值对添加到存储中，如果键名存在，则更新其对应的值。

            键名和键值类型都是字符串，如果值的类型不是字符串，就会自动调用toString方法转换。如果值是对象，就需要自己通过JSON.stringify(p)转化为字符串，否则本地存储的内容将是object的字符串数组

    2.  `xxxxxStorage.getItem('person');`

        该方法接受一个键名作为参数，返回键名对应的值。

        如果要将字符串转化为对象：JSON.parse(localStorage.getItem('person'))
        如果读取一个不存在的键名，会返回 null

    3.  `xxxxxStorage.removeItem('key');`

        该方法接受一个键名作为参数，并把该键名从存储中删除。

    4.  ` xxxxxStorage.clear()`

        该方法会清空存储中的所有数据。

4.  备注：

    1. SessionStorage 存储的内容会随着浏览器窗口关闭而消失。
    2. LocalStorage 存储的内容，不会随着浏览器窗口的关闭而消失，需要手动清除才会消失。（1）调用 api 清除（2）清除缓存
    3. `xxxxxStorage.getItem(xxx)`如果 xxx 对应的 value 获取不到，那么 getItem 的返回值是 null。
    4. `JSON.parse(null)`的结果依然是 null。

# 八、组件的自定义事件

$on,$emit,$off 都是 vue 显示原型属性上的对象，vc 可以访问 vue 上的所有属性及方法

1. 一种组件间通信的方式，适用于：<strong style="color:red">子组件 ===> 父组件</strong>

2. 使用场景：A 是父组件，B 是子组件，B 想给 A 传数据，那么就要在 A 中给 B 绑定自定义事件（<span style="color:red">事件的回调在 A 中</span>，只有这样才能得到子组件的数据，才能子=>夫）。

   ```JavaScript
    //这将多个参数封装在一个数组中
       getStudentName(name, ...params) {
         console.log("App收到了学生名：", name, params);
         this.studentName = name;
       }
   ```

3. 绑定自定义事件：

   1. 第一种方式，在父组件中：`<Demo @atguigu="test"/>` 或 `<Demo v-on:atguigu="test"/>`

   2. 第二种方式，在父组件中：
      在 mounted 钩子函数中开启定时器，绑定自定义事件，消息的订阅与发布
      `js
      <Demo ref="demo"/>
      ......
      mounted(){
      this.$refs.xxx.$on('atguigu',this.test)
      }
      `

   3. 若想让自定义事件只能触发一次，可以使用`once`修饰符，或`$once`方法。

4. 触发自定义事件：`this.$emit('atguigu',数据)`

5. 解绑自定义事件`this.$off('atguigu')`
   在 beforeDestory 钩子函数中，关闭定时器，解绑自定义事件，取消消息订阅
   （1）解绑多个事件：字符串数组

   ```JavaScript
    this.$off(['atguigu','demo'])
   ```

   （2）解绑所有事件

   ```JavaScript
    this.$off()
   ```

   **注意：自定义事件不需要解绑，因为事件是给组件绑定的，当组件被销毁时，其事件必定也不再存在。但是全局事件总线中，需要解绑事件，因为事件是给$bus总线绑定的，组件的销毁，不会导致$bus 上事件的销毁**

6. 组件上也可以绑定原生 DOM 事件，需要使用`native`修饰符。否则组件将把事件作为自定义事件处理

   ```JavaScript
    <Student ref="student" @click.native="show" />
   ```

7. 注意：通过`this.$refs.xxx.$on('atguigu',回调)`绑定自定义事件时，回调<span style="color:red">要么配置在 methods 中</span>，<span style="color:red">要么用箭头函数</span>，否则 this 指向会出问题！（this 必须指向父组件，因为数据从子=>夫）

   ## 数据从子组件=>父组件的两种方法

   1、通过父组件给子组件传递函数的 props 实现：子给父传递数据

   ```JavaScript
   //父
   <MyHeader :addTodo="addTodo"/>
   addTodo(todoObj){
   				this.todos.unshift(todoObj)
   			},
   //子
   <input type="text" placeholder="请输入你的任务名称，按回车键确认" v-model="title" @keyup.enter="add"/>
   props:['addTodo']
   methods: {
   			add(){
   				//校验数据
   				if(!this.title.trim()) return alert('输入不能为空')
   				//将用户的输入包装成一个todo对象
   				const todoObj = {id:nanoid(),title:this.title,done:false}
   				//通知App组件去添加一个todo对象
   				this.addTodo(todoObj)
   				//清空输入
   				this.title = ''
   			}
   ```

   （2）组件的自定义事件

   总结：（1）为达到子组件 ===> 父组件，函数必须定义在父组件中，且 this 指向父组件。（2）在子组件触发事件的响应函数中调用父组件的函数，从而达到数据传输的目的

# 九、全局事件总线（GlobalEventBus）

1. 一种组件间通信的方式，适用于<span style="color:red">任意组件间通信</span>。

   ![image-20220311214402340](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20220311214402340.png)

2. 安装全局事件总线：

   （1）要让所有的组件可以看到这个傀儡，组件实例对象（vc）/vm 可以访问到 Vue 原型上的属性、方法。Vue.prototype.$bus

   （2）同时要让傀儡满足第二个条件（有$on,$emit,$off 方法，这些方法都在 vue 的原型上，所以这个傀儡的类型必须是 vm,vc(都可以访问 vue 的原型对象)

   $bus 就是当前应用的 vc

   ```javascript
   const vc = Vue.extend({}); //这是一个vuecomponent类型的构造函数
   const d = new vc(); //实例化对象
   Vue.prototype.$bus = d;
   ```

   $bus 就是当前应用的 vm

   ```js
   new Vue({
   	......
   	beforeCreate() {
   		Vue.prototype.$bus = this //安装全局事件总线，$bus就是当前应用的vm
   	},
       ......
   })
   ```

3. 使用事件总线：

   1. 接收数据：A 组件想接收数据，则在 A 组件中给$bus 绑定自定义事件，事件的<span style="color:red">回调留在 A 组件自身。</span>

      ```js
      methods(){
        demo(data){......}
      }
      ......
      mounted() {
        this.$bus.$on('xxxx',this.demo)
      }
      ```

   2. 提供数据：`this.$bus.$emit('xxxx',数据)`

4. 最好在 beforeDestroy 钩子中，用$off 去解绑<span style="color:red">当前组件所用到的</span>事件，这个事件会在傀儡中消失。如果不解绑，当组件销毁后，这个事件还会在傀儡中占有位置，浪费资源（组件都消失了，这个事件失去作用了）

# 十、消息订阅与发布（pubsub）

1.  一种组件间通信的方式，适用于<span style="color:red">任意组件间通信</span>。

2.  使用步骤：

        1.  安装 pubsub：`npm i pubsub-js`
        // pubsub是一个对象
        2.  引入: `import pubsub from 'pubsub-js'`
        
        3.  接收数据：A 组件想接收数据，则在 A 组件中订阅消息，订阅的<span style="color:red">回调留在 A 组件自身。</span>
        
            ```js
            methods(){
              demo(data){......}
            }
            ......
            mounted() {
              this.pid = pubsub.subscribe('xxx',this.demo) //订阅消息，返回消息的id，为了取消订阅，所以把id添加到对象的属性中，否则由于作用域，id将无法传输出去
              this.pubId = pubsub.subscribe('hello',(msgName,data)=>{
                  //回调函数中：第一个参数指的是消息名，第二个参数指的是消息
            			console.log(this)
            			// console.log('有人发布了hello消息，hello消息的回调执行了',msgName,data)
            			})
            }
            ```
        
        4.  提供数据：`pubsub.publish('xxx',数据)`
        
            第一个参数指的是消息名，第二个参数指的是消息
        
        5.  最好在 beforeDestroy 钩子中，用`PubSub.unsubscribe(pid)`去<span style="color:red">取消订阅。</span>

    注意：消息的订阅与发布不能在 vue 开发者工具中进行观察，因为 pubsub 库不是属于 vue 的。vue 开发者工具可以观察全局事件总线以及自定义事件

# 十一、nextTick

前提：函数体执行完毕后，才会重新解析页面。

1. 语法：`this.$nextTick(回调函数)`

2. 作用：在下一次 DOM 更新结束后执行其指定的回调。

3. 什么时候用：当改变数据后，要基于更新后的新 DOM 进行某些操作时，要在 nextTick 所指定的回调函数中执行。

   ```
   this.$nextTick(function(){
   					this.$refs.inputTitle.focus()
   				})
   ```

# 十二、Vue 封装的过度与动画

1. 作用：在插入、更新或移除 DOM 元素时，在合适的时候给**元素添加样式类名**。给元素上级添加 transition 元素就可以实现，不会破坏结构

3. 写法：

   准备好样式：

   - 元素进入的样式：
     1. v-enter：进入的起点
     2. v-enter-active：进入过程中
     3. v-enter-to：进入的终点
   - 元素离开的样式：
     1. v-leave：离开的起点
     2. v-leave-active：离开过程中
     3. v-leave-to：离开的终点

3. 如果使用动画，样式选择 v-enter-active 和 v-leave-active 就可以。使用过渡，则使用 v-enter 和 v-enter-to，它们和离开的样式有相同的地方，可以复用写在一起 

3.  使用`<transition>`包裹要过度的元素，并配置 name 属性（可选），如果有 name，样式第一个单词也要发生相应的改变：

   ```
   <transition name="hello">
   	<h1 v-show="isShow">你好啊！</h1>
   </transition>
   ```
   
3. 备注：若有多个元素需要过度，则需要使用：`<transition-group>`，且每个元素都要指定`key`值。

   ```
   <transition-group 
   			appear
   			name="animate__animated animate__bounce" 
   			enter-active-class="animate__swing"
   			leave-active-class="animate__backOutUp"
   		>
   			<h1 v-show="!isShow" key="1">你好啊！</h1>
   			<h1 v-show="isShow" key="2">尚硅谷！</h1>
   		</transition-group>
   ```
   
6. 使用动画库

# 十三、vue 脚手架配置代理

## 方法一

在 vue.config.js 中添加如下配置：

```js
devServer: {
  proxy: "http://localhost:5000";
}
```

说明：

1. 优点：配置简单，请求资源时直接发给前端（8080）即可。
2. 缺点：不能配置多个代理，不能灵活的控制请求是否走代理。
3. 工作方式：若按照上述配置代理，当请求了前端不存在的资源时，那么该请求才会转发给服务器 （优先匹配前端资源）

## 方法二

编写 vue.config.js 配置具体代理规则：

```js
module.exports = {
  devServer: {
    proxy: {
      "/api1": {
        // 匹配所有以 '/api1'开头的请求路径
        target: "http://localhost:5000", // 代理目标的基础路径
        changeOrigin: true,
          //重写请求的路径，否则将会向后端请求不存在的路径
        pathRewrite: { "^/api1": "" },
      },
      "/api2": {
        // 匹配所有以 '/api2'开头的请求路径
        target: "http://localhost:5001", // 代理目标的基础路径
        changeOrigin: true,
        pathRewrite: { "^/api2": "" },
      },
    },
  },
};
/*是否对服务器说谎与服务器主机名和端口名都相同
   changeOrigin设置为true时，服务器收到的请求头中的host为：localhost:5000
   changeOrigin设置为false时，服务器收到的请求头中的host为：localhost:8080
   changeOrigin默认值为true
*/
```

说明：

1. 优点：可以配置多个代理，且可以灵活的控制请求是否走代理。
2. 缺点：配置略微繁琐，请求资源时必须加前缀。

# 十四、插槽

1. 作用：让父组件可以向子组件指定位置插入 html 结构，非 HTML 元素不传递。也是一种组件间通信的方式，适用于 <strong style="color:red">父组件 ===> 子组件</strong> 。
2. 如果样式写在父组件中（解析结构的同时带着样式给组件），如果没写，只把结构给组件，样式被组件控制
3. 分类：默认插槽、具名插槽、作用域插槽

4. 使用方式：

   1. 默认插槽：

      ```vue
      父组件中：
      <Category>
                 <div>html结构1</div>
              </Category>
      子组件中：
      <template>
        <div>
          <!-- 定义插槽 -->
          <slot>插槽默认内容...</slot>
        </div>
      </template>
      ```

   2. 具名插槽：

      ```vue
      父组件中：
      <Category>
                  <template slot="center">
                    <div>html结构1</div>
                  </template>
      
                  <template v-slot:footer>
                     <div>html结构2</div>
                  </template>
              </Category>
      子组件中：
      <template>
        <div>
          <!-- 定义插槽 -->
          <slot name="center">插槽默认内容...</slot>
          <slot name="footer">插槽默认内容...</slot>
        </div>
      </template>
      ```

   3. 作用域插槽（也可以有命中）：

      1. 理解：<span style="color:red">数据在组件的自身，但根据数据生成的结构需要组件的使用者来决定。</span>（games 数据在 Category 组件中，但使用数据所遍历出来的结构由 App 组件决定）

      2. 具体编码：

         ```vue
         父组件中：
         <Category>
         			<template scope="scopeData">
         				<!-- 生成的是ul列表 -->
         				<ul>
         					<li v-for="g in scopeData.games" :key="g">{{g}}</li>
         				</ul>
         			</template>
         		</Category>
         
         <Category>
         			<template slot-scope="scopeData">
         				<!-- 生成的是h4标题 -->
         				<h4 v-for="g in scopeData.games" :key="g">{{g}}</h4>
         			</template>
         		</Category>
         子组件中：
         <template>
           <div>
             <slot :games="games"></slot>
           </div>
         </template>
         
         <script>
         export default {
           name: "Category",
           props: ["title"],
           //数据在子组件自身
           data() {
             return {
               games: ["红色警戒", "穿越火线", "劲舞团", "超级玛丽"],
             };
           },
         };
         </script>
         ```


# 十五、Vuex

## 1.概念

在 Vue 中实现集中式状态（数据）管理的一个 Vue 插件，对 vue 应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信。

![vuex1](E:\前端\study_vue\资料（含课件）\02_原理图\vuex1.png)

![img](file:///D:\QQdata\2337939131\Image\C2C~MNY{WFU`ODS_ZL4M%3R0{E.png)

![image-20220316154240570](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20220316154240570.png)

在终端下载时，默认下载最新版，所以在 vue2 中需要指定下载 vuex3 版本。npm i vuex@3

![image-20220316154300792](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20220316154300792.png)

Vue.use（Vuex）使 vue认识 store 配置项（vm,vc多了$store属性），配置项的内容需要我们自己创建 js 文件添加内容。

## 2.何时使用？

多个组件需要共享数据时

## 3.搭建 vuex 环境

vuex 的核心配置项：actions，mutations，state，

从入口文件开始解析，脚手架会把 import 语句进行整理，把所有的 import 语句执行完毕，在执行其他语句。即使在中间穿插了其他的语句也没有用。所以在store文件中使用Vue.use(Vuex)，否则在导入store文件时，还没有应用Vuex插件，却创建store（new Vuex.Store）对象，将会报错

1. 创建文件：`src/store/index.js`

   ```js
   //引入Vue核心库
   import Vue from "vue";
   //引入Vuex
   import Vuex from "vuex";
   //应用Vuex插件
   Vue.use(Vuex);

   //准备actions对象——响应组件中用户的动作
   const actions = {};
   //准备mutations对象——修改state中的数据
   const mutations = {};
   //准备state对象——保存具体的数据
   const state = {};

   //创建并暴露store
   export default new Vuex.Store({
     actions,
     mutations,
     state,
   });
   ```

2. 在`main.js`中创建 vm 时传入`store`配置项的内容

   ```js
   ......
   //引入store
   import store from './store'
   ......
   
   //创建vm
   new Vue({
   	el:'#app',
   	render: h => h(App),
   	store
   })
   ```

store 配置项将出现在 vm,vc 中。可以通过它使用 commit 方法（对应 mutations 中的方法）以及 dispatch 方法（对应 actions 中的方法）

## 4.基本使用

1. 初始化数据、配置`actions`、配置`mutations`，操作文件`store.js`

   ```js
   //引入Vue核心库
   import Vue from "vue";
   //引入Vuex
   import Vuex from "vuex";
   //引用Vuex
   Vue.use(Vuex);

   const actions = {
     //响应组件中加的动作
     //第一个参数：微型store，它包含dispatch，commit方法以及state数据。可以在这里直接操作state中的数据，页面也有响应改变，但是该操作不会被vuex开发工具监视。如果业务逻辑非常复杂，可以把业务分拆多个，完成一个就调用dispatch继续下一个业务逻辑，直到所有的业务逻辑都完成，就调用commit触发mutations中的对应的方法
     //第二个参数：操作值
     jia(context, value) {
       // console.log('actions中的jia被调用了',miniStore,value)
       context.commit("JIA", value);
     },
   };

   const mutations = {
     //执行加
     //第一个参数state
     //第二个参数：操作树
     JIA(state, value) {
       // console.log('mutations中的JIA被调用了',state,value)
       state.sum += value;
     },
   };

   //初始化数据
   const state = {
     sum: 0,
   };

   //创建并暴露store
   export default new Vuex.Store({
     actions,
     mutations,
     state,
   });
   ```

2. 组件中读取 vuex 中的数据：`$store.state.sum`

3. 组件中修改 vuex 中的数据：`$store.dispatch('action中的方法名',数据)` 或 `$store.commit('mutations中的方法名',数据)`

   > 备注：若没有网络请求（操作数从网络请求获得）或其他业务逻辑，组件中也可以越过 actions，即不写`dispatch`，直接编写`commit`

## 5.getters 的使用

1. 概念：当 state 中的数据需要经过加工后再使用时，可以使用 getters 加工。多组件或要复用时使用

2. 在`store.js`中追加`getters`配置

   ```js
   ......

   const getters = {
   	bigSum(state){
   		return state.sum * 10
   	}
   }

   //创建并暴露store
   export default new Vuex.Store({
   	......
   	getters
   })
   ```

3. 组件中读取数据：`$store.getters.bigSum`

## 6.四个 map 方法的使用

这四个方法返回的都是对象，所以必须使用扩展运算符

1. <strong>mapState 方法：</strong>用于帮助我们映射`state`中的数据为计算属性

   ```js
   computed: {
       //借助mapState生成计算属性：sum、school、subject（对象写法）
       //key:计算属性名
       //value：state中的名字，必须为字符串，否则表示为变量，将报错
        ...mapState({sum:'sum',school:'school',subject:'subject'}),

       //借助mapState生成计算属性：sum、school、subject（数组写法）
       //数组写法要求计算属性名与state、getter中的属性名相同，一名多用
       ...mapState(['sum','school','subject']),
   },
   ```

2. <strong>mapGetters 方法：</strong>用于帮助我们映射`getters`中的数据为计算属性

   ```js
   computed: {
       //借助mapGetters生成计算属性：bigSum（对象写法）
       ...mapGetters({bigSum:'bigSum'}),

       //借助mapGetters生成计算属性：bigSum（数组写法）
       //数组写法要求计算属性名与state、getter属性名相同，一名多用
       ...mapGetters(['bigSum'])
   },
   ```

3. <strong>mapActions 方法：</strong>用于帮助我们生成与`actions`对话的方法，即：包含`$store.dispatch(xxx)`的函数

   ```js
   methods:{
       //靠mapActions生成：incrementOdd、incrementWait（对象形式）
       ...mapActions({incrementOdd:'jiaOdd',incrementWait:'jiaWait'})

       //靠mapActions生成：incrementOdd、incrementWait（数组形式）
        //数组写法要求计算属性名与state、getter属性名相同，一名多用
       ...mapActions(['jiaOdd','jiaWait'])
   }
   ```

4. <strong>mapMutations 方法：</strong>用于帮助我们生成与`mutations`对话的方法，即：包含`$store.commit(xxx)`的函数

   ```js
   methods:{
       //靠mapActions生成：increment、decrement（对象形式）
       ...mapMutations({increment:'JIA',decrement:'JIAN'}),
   
       //靠mapMutations生成：JIA、JIAN（数组形式）
            //数组写法要求计算属性名与state、getter属性名相同，一名多用
       ...mapMutations(['JIA','JIAN']),
   }
   ```

> mapActions内部执行程序：
>
> increment（value）{
>
> this.$store.dispatch("jiaOdd",value)
>
> }
>
> **mapActions 与 mapMutations 使用时，若需要传递参数需要：在模板中绑定事件时传递好参数，否则参数是事件对象。**

## 7.模块化+命名空间

1. 目的：让多种数据分类更加明确，让代码更好维护，。

2. 修改`store.js`

   ```javascript
   const countAbout = {
     namespaced:true,//开启命名空间，// 使分类名被map...方法认识
     state:{x:1},
     mutations: { ... },
     actions: { ... },
     getters: {
       bigSum(state){
          return state.sum * 10
       }
     }
   }

   const personAbout = {
     namespaced:true,//开启命名空间
     state:{ ... },
     mutations: { ... },
     actions: { ... }
   }

   const store = new Vuex.Store({
     modules: {
       countAbout,
       personAbout
     }
   })
   ```

3. 开启命名空间后，组件中读取 state 数据：

   ```js
   //方式一：自己直接读取
   this.$store.state.personAbout.list
   //方式二：借助mapState读取countAbout管理的数据：
   ...mapState('countAbout',['sum','school','subject']),
   ```

4. 开启命名空间后，组件中读取 getters 数据：

   ```js
   //方式一：自己直接读取
   this.$store.getters['personAbout/firstPersonName']
   //方式二：借助mapGetters读取countAbout管理的数据：
   ...mapGetters('countAbout',['bigSum'])
   ```

5. 开启命名空间后，组件中调用 dispatch

   如果 action 中有业务逻辑，就必须自己写 dispatch

   ```js
   //方式一：自己直接dispatch
   this.$store.dispatch('personAbout/addPersonWang',person)
   //方式二：借助mapActions读取countAbout中的方法：
   ...mapActions('countAbout',{incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
   ```

6. 开启命名空间后，组件中调用 commit

   ```js
   //方式一：自己直接commit
   this.$store.commit('personAbout/ADD_PERSON',person)
   //方式二：借助mapMutations读取countAbout中的方法：
   ...mapMutations('countAbout',{increment:'JIA',decrement:'JIAN'}),
   ```

# 十六、路由

vue 的一个插件库，专门用来实现 SPA 应用

1. 理解： 一个路由（route）就是一组映射关系（key - value），多个路由需要路由器（router）进行管理。
2. 前端路由：key 是路径，value 是组件或函数（后端路由）。
3. vue2----vue-router3，vue3------vue-router4
4. router-link 在按钮/链接的当前组件中

## 1. 对 SPA 应用的理解

1. 单页 Web 应用（single page web application，SPA）。
2. 整个应用只有一个完整的页面。
3. 点击页面中的导航链接不会刷新页面，只会做页面的局部更新。
4. 数据需要通过 ajax 请求获取

## 2.基本使用

1. 安装 vue-router，命令：`npm i vue-router`

1. 导入插件：import VueRouter from "vue-router"

2. 应用插件：`Vue.use(VueRouter)`

3. 编写 router 配置项:

   ```js
   //引入VueRouter
   import VueRouter from "vue-router";
   //引入Luyou 组件
   import About from "../components/About";
   import Home from "../components/Home";

   //创建router实例对象，去管理一组一组的路由规则
   const router = new VueRouter({
     routes: [
       {
         path: "/about",
         component: About,
       },
       {
         path: "/home",
         component: Home,
       },
     ],
   });

   //暴露router
   export default router;
   ```

5. router-link实现切换路径（active-class 可配置高亮样式），路径的切换都会被路由器监测，根据路由规则匹配组件

   ```vue
   //最终会转化为a标签
   <router-link active-class="active" to="/about">About</router-link>
   ```

5. 指定组件的展示位置

   ```vue
   <router-view></router-view>
   ```

## 3.几个注意点

1. 路由组件通常存放在`pages`文件夹，一般组件通常存放在`components`文件夹。
2. 通过切换，“隐藏”了的路由组件，默认是被销毁掉的，需要的时候再去挂载。
3. 参与了路由规则的组件，都有自己的`$route`属性，里面存储着自己的路由信息（query，pramas）。
4. 整个应用只有一个 router，可以通过组件的`$router`属性获取到(实现编程式路由导航)。

## 4.多级路由（多级路由）

1. 配置路由规则，使用 children 配置项：

   ```js
   routes: [
     {
       path: "/about",
       component: About,
     },
     {
       path: "/home",
       component: Home,
       children: [
         //通过children配置子级路由
         //遍历children时，会给路径加“/“ 
         {
           path: "news", //此处一定不要写：/news
           component: News,
         },
         {
           path: "message", //此处一定不要写：/message
           component: Message,
         },
       ],
     },
   ];
   ```

2. 跳转（**要写完整路径**）：

   ```vue
   <router-link to="/home/news">News</router-link>
   ```

## 5.路由的 query 参数

1. 传递参数【父组件---》路由组件】

   ```vue
   <!-- 跳转并携带query参数，to的字符串写法，参入固定 -->
   <router-link :to="/home/message/detail?id=666&title=你好">跳转</router-link>
   <!-- 跳转并携带query参数，to的字符串写法，动态传参 -->
   <router-link :to="`/home/message/detail?id=${a}&title=${b}`">跳转</router-link>
   <!-- 跳转并携带query参数，to的对象写法 -->
   <router-link
   	:to="{
   		path:'/home/message/detail',
   		query:{
   		   id:666,
               title:'你好'
   		}
   	}"
   >跳转</router-link>
   ```

2. 路由组件接收参数：

   ```
   $route.query.id;
   $route.query.title;
   ```
   
3. 动态传参时的数据，应该在父组件的 data 配置项中定义

## 6.命名路由

1. 作用：可以简化路由跳转的路径。

2. 如何使用

   1. 给路由命名：

      ```js
      {
      	path:'/demo',
      	component:Demo,
      	children:[
      		{
      			path:'test',
      			component:Test,
      			children:[
      				{
                            name:'hello' //给路由命名
      					path:'welcome',
      					component:Hello,
      				}
      			]
      		}
      	]
      }
      ```

   2. 简化跳转：

      ```vue
      <!--简化前，需要写完整的路径 -->
      <router-link to="/demo/test/welcome">跳转</router-link>
      
      <!--简化后，直接通过名字跳转 -->
      <router-link :to="{ name: 'hello' }">跳转</router-link>
      
      <!--简化写法配合传递参数 必须搭配to的对象写法-->
      <router-link
        :to="{
          name: 'hello',
          query: {
            id: 666,
            title: '你好',
          },
        }"
      >跳转</router-link>
      ```

**注意：使用 name 代替 path 进行路由跳转，必须必须搭配 to 的对象写法**

## 7.路由的 params 参数

1. 配置路由，声明接收 params 参数

   ```js
   {
   	path:'/home',
   	component:Home,
   	children:[
   		{
   			path:'news',
   			component:News
   		},
   		{
   			component:Message,
   			children:[
   				{
   					name:'xiangqing',
   					path:'detail/:id/:title', //使用占位符声明接收params参数
   					component:Detail
   				}
   			]
   		}
   	]
   }
   ```

2. 传递参数（父---》路由组件）

   ```vue
   <!-- 跳转并携带params参数，to的字符串写法 -->
   <!-- 跳转并携带params参数，to的字符串写法。下面这种参数固定 -->
   <router-link :to="/home/message/detail/666/你好">跳转</router-link>
   <!-- 跳转并携带params参数，to的字符串写法。下面这种动态传参 -->
   <router-link :to="/home/message/${detail}">跳转</router-link>
   <!-- 跳转并携带params参数，to的对象写法 -->
   <router-link
     :to="{
       name: 'xiangqing',
       params: {
         id: 666,
         title: '你好',
       },
     }"
   >跳转</router-link>
   ```

   > 特别注意：路由携带 params 参数时，若使用 to 的对象写法，则不能使用 path 配置项，必须使用 name 配置！

3. 动态传参时的数据，应该在父组件的 data 配置项中定义

4. 路由组件接收参数：

   ```js
   $route.params.id;
   $route.params.title;
   ```

## 8.路由的 props 配置

作用：让路由组件更方便的使用参数

```js
{
	name:'xiangqing',
	path:'detail/:id',
	component:Detail,

	//第一种写法：props值为对象，该对象中所有的key-value的组合最终都会通过props传给Detail组件，参数固定
	// props:{a:900}

	//第二种写法：props值为布尔值，布尔值为true，则把路由收到的所有params参数通过props传给Detail组件
	// props:true

	//第三种写法：props值为函数，该函数返回的对象中每一组key-value都会通过props传给Detail组件
	props($route){
		return {
			id:$route.query.id,
			title:$route.query.title
		}
	}
}
```

```JavaScript
<li>消息编号：{{id}}</li>
<li>消息标题：{{title}}</li>
props:['id','title'],
```

## 9.`<router-link>`的 replace 和 push 属性

1. 作用：路由跳转时，操作浏览器历史记录的模式
2. 浏览器的历史记录是存储在栈中，后退与前进 是指针指向的历史纪录变化引起的
3. 浏览器的历史记录有两种写入方式：分别为`push`和`replace`，`push`是追加历史记录（不会影响其他历史记录），`replace`是替换当前记录。路由跳转时候默认为`push`。
4. 如何开启`replace`模式：`<router-link replace .......>News</router-link>`
5. replace 模式下，后退的页面不再是前一个页面，而是此时指针指向的页面

## 10.编程式路由导航

1. 作用：不借助`<router-link> `实现路由跳转，而是通过路由$router的push和replace方法实现路由的跳转，让路由跳转更加灵活

2. 具体编码：

   ```js
   //$router的两个API
   this.$router.push({
     name: "xiangqing",
     params: {
       id: xxx,
       title: xxx,
     },
   });
   
   this.$router.replace({
     name: "xiangqing",
     params: {
       id: xxx,
       title: xxx,
     },
   });
   //基于浏览器的历史记录
   this.$router.forward(); //前进
   this.$router.back(); //后退
   this.$router.go(); //可前进也可后退
   this.$router.push(字符串路径或对象); //push模式路由跳转
   this.$router.replace(字符串路径或对象); //replace模式路由跳转
   ```

## 11.缓存路由组件

1. 作用：让不展示的路由组件保持挂载，不被销毁。

1. 按钮、链接在哪(使用跳转 push,replace,router-link)，

1. 在哪里展示组件，keep-alive 就在那

1. include 指向要缓存的组件名，如果不写，将缓存所有的组件

1. 具体编码：

   ```vue
   //缓存一个
   <keep-alive include="News"> 
       <router-view></router-view>
   </keep-alive>
   <!-- 缓存多个路由组件 -->
   <!-- <keep-alive :include="['News','Message']"> -->
   ```

## 12.两个新的生命周期钩子

1. 作用：路由组件所独有的两个钩子，用于捕获路由组件的激活状态。
2. 具体名字：

   1. `activated`路由组件被激活时触发。
   2. `deactivated`路由组件失活时触发。

   3.作用：路由组件激活时触发特有的行为，切换到另一个路由组件时停止

## 13.路由守卫

1. 作用：对路由进行权限控制

2. 分类：全局守卫、独享守卫、组件内守卫

3. 需要给路由添加守卫，所以不能采用默认暴露

4. 全局守卫:

   ```js
   //全局前置守卫：初始化时执行、每次路由切换前执行
   //to目标路由,from当前路由，包含路由的信息（query，params,meta,path,name）
   //next函数，表示是否可以路由跳转，如果没有执行这个函数，那么路由跳转将失败
   //在meta写自己想添加的数据。例如：哪些路由跳转需要进行验证，就添加isAuth，没有添加isAuth的路由，在读取属性时会返回undefined，转化为布尔值为false，不必验证
   router.beforeEach((to, from, next) => {
     console.log("beforeEach", to, from);
     if (to.meta.isAuth) {
       //判断当前路由是否需要进行权限控制
       if (localStorage.getItem("school") === "atguigu") {
         //权限控制的具体规则
         next(); //放行
       } else {
         alert("暂无权限查看");
         // next({name:'guanyu'})
       }
     } else {
       next(); //放行
     }
   });
   //在这里写路由组件切换成功后的引起的行为变化的逻辑
   //全局后置守卫：初始化时执行、每次路由切换后执行
   router.afterEach((to, from) => {
     console.log("afterEach", to, from);
     if (to.meta.title) {
       document.title = to.meta.title; //修改网页的title
     } else {
       document.title = "vue_test";
     }
   });
   ```

5. 独享守卫:

   ```js
   {
   					name:'xinwen',
   					path:'news',
   					component:News,
   					meta:{isAuth:true,title:'新闻'},
   					beforeEnter: (to, from, next) => {
   						console.log('独享路由守卫',to,from)
   						if(to.meta.isAuth){ //判断是否需要鉴权
   							if(localStorage.getItem('school')==='atguigu'){
   								next()
   							}else{
   								alert('学校名不对，无权限查看！')
   							}
   						}else{
   							next()
   						}
   					}
   				},
   ```

6. 组件内守卫：

   ```js
   //通过路由规则，进入该组件时被调用
   beforeRouteEnter (to, from, next) {
   },
   //：通过路由规则，离开该组件时被调用
   beforeRouteLeave (to, from, next) {
   }
   ```

## 14.路由器的两种工作模式

1. 对于一个 url 来说，什么是 hash 值？—— #及其后面的内容就是 hash 值。
2. 发起HTTP请求时，hash值不会给服务器。hash 值不会包含在 HTTP 请求中，即：hash 值不会带给服务器。
3. hash 模式：
   1. 地址中永远带着#号，不美观 。
   2. 若以后将地址通过第三方手机 app 分享，若 app 校验严格，则地址会被标记为不合法。
   3. 兼容性较好。
4. history 模式：
   1. 地址干净，美观 。
   2. 兼容性和 hash 模式相比略差。ie8及其下浏览器无法运行vue项目
   3. 应用部署上线时需要后端人员支持，解决刷新页面服务端 404 的问题。（使用connect-history-api-fallback服务端中间件解决）

![image-20230331134012900](C:\Users\ydy\AppData\Roaming\Typora\typora-user-images\image-20230331134012900.png)

### 14.1 使用node.js和express建立服务器，部署网页

新建文件夹
（1）文件夹变成合理的包->npm init
（2）安装express->npm i express
（3）写入服务器代码
   解决路由的history工作模式带来的问题：（1）搜索npm->connect-history-api-fallback查看详细使用（2）服务端安装connect-history-api-fallback（3）中间件使用app.use(history())
将网页部署到服务器
 （1）npm run build生成dist文件
 （2）将dist文件的内容复制到服务器新建的static文件夹（与server.js同级）中
 （3）写入代码：app.use(express.static(__dirname+'/static'))

```
const express = require('express')
const history = require('connect-history-api-fallback');

const app = express()
app.use(history())
app.use(express.static(__dirname+'/static'))

app.get('/person',(req,res)=>{
	res.send({
		name:'tom',
		age:18
	})
})

app.listen(5005,(err)=>{
	if(!err) console.log('服务器启动成功了！')
})
```

### 
