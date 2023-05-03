### 一、创建组件的几种方法

使用Vue.extend(options)创建，其中options和new Vue(options)时传入的那个options几乎一样，但也有点区别；

- el不要写，为什么？ ——— 最终所有的组件都要经过一个vm的管理，由vm中的el决定服务哪个容器。
- data必须写成函数，为什么？ ———— 避免组件被复用时，数据存在引用关系。

1、

```
 const school = Vue.extend({
         template:
             `<div>
                 <h2>{{schoolName}}</h2>
                 <h2>{{address}}</h2>
             </div>
             `,
         data() {
             return {
                 schoolName: "成都工学院",
                 address: "成都"
             }
         }
     })
```

### 二、注册组件

#### 1、局部注册

```
 <school></school>
 components: {
             school,  student
         }
  components: {
             school:school
         }   
```

```
  components: {
'组件的名称':{ 配置对象成员 },'组件的名称':{配置对象成员 }...},
  // 注册私有组件
      components:{
        'com-page':{
          // template: 设定当前组件拥有的html标签
          template:`
            <ul>
              <li>1</li>
              <li>2</li>
              <li>3</li>
            </ul>
          `
        }
      },
```

#### 2、全局注册

```
Vue.component(名称,{配置对象成员})
 //全局组件需要在new Vue之前设置。
new Vue()
```

### 三、创建路由的几种方法

#### 1、createRouter

```
import { createRouter, createWebHistory } from "vue-router";
// 路由规则
const routes = [
  {
    path: "/",
    name: "主页",
    meta: ["istabbar"],
    component: () => import("../views/Home.vue"),
  },
];
//根据路由规则创建路由
const router = createRouter({
  history: createWebHistory(""),
  routes,
});
export default router;

```

在`main.js`中添加到`Vue`对象

```
import { createApp } from "vue";
import App from "./App.vue";
import router from "./router"; // 添加路由router引入
// 创建VUE对象
createApp(App)
  .use(router) // 使用.use(router)添加路由router
  .mount("#app");
```

#### 2、VueRouter实例对象

```
const router = new VueRouter({
  routes: [
    {
      path: '/user/:id', component: User,
      children: [
        // 当 /user/:id 匹配成功，
        // UserHome 会被渲染在 User 的 <router-view> 中
        { path: '', component: UserHome },

        // ...其他子路由
      ]
    }
  ]
})
```

### 四、Router获取及使用

```
<template>
  <div class="page">
    因为在setup内部没有this访问权限，不能直接使用this访问this.$router 或者
    this.$route。所有使用useRouter, useRoute来获取
  </div>
</template>
<script>
import { getCurrentInstance } from "vue";
import { useRouter } from "vue-router";
export default {
  setup() {
    // 第一种方法：获取路由对象 router 的方法1
    const vue = getCurrentInstance();
    const router1 = vue.ctx.$router;
    // 第二种方法：获取路由对象 router 的方法2
    const router2 = useRouter();
      
    // 下面提供了router对应的方法使用大全
    // ------------------------------------------------------
    // router1.addRoute(parentOrRoute, route)
    // router1.afterEach(回调函数)
    // router1.back() 等价于 go(-1)
    // router1.beforeEach(回调函数)
    // router1.beforeResolve(回调函数)
    // router1.currentRoute 获取当前路由：如： {_rawValue: {…}, _shallow: true, __v_isRef: true, _value: {…}}
    // router1.forward() 等价于 go(1)
    // router1.getRoutes: ƒ getRoutes()
    // router1.go(delta) 等价于 routerHistory.go(delta) 跳到指定历史记录
    // router1.hasRoute(name) 判断是否有对应的路由
    // router1.isReady() 判断路由是否准备跳转
    // router1.onError(回调函数) 当发生错误的时候执行的
    // router1.options 获取所有路由信息 {history: {…}, routes: Array(5)}
    // router1.push(to) 跳转到指定路由对应的页面，有历史记录
    // router1.removeRoute(name) 动态的删除某个路由
    // router1.replace(to) 直接跳转到指定路由页面，没有历史记录
    // router1.resolve(rawLocation, currentLocation)  可以自定义跳转参数的方法
    return {};
  },
  mounted() {
    // 第三种方法：获取路由对象 router 的方法3
    console.log(this.$router);
  },
};
</script>
```

注意：store同理