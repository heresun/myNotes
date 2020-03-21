# vue模板语法

+ `v-bind:标签属性`：将vue实例的数据和标签的某个属性的值绑定，该属性值跟随vue实例的数据的改变而改变

  v-bind 可以缩写为 ：

+ `{{message}}`,message是vue实例的变量名，这个语法会将message的内容解析为纯文本，如果想把message的内容解析为html，则需要 `v-html="message"`这样
+ {{}}中也可以写js的表达式，仅仅是表达式，语句不可以
+ v-on:click=""可以缩写为@

# 条件渲染

v-if=""

v-else-if=""

v-else



# 路由

```javascript
import Vue from 'vue'
import VueRouter from "vue-router";
import Index from "../pages/Index";
import Login from "../pages/Login";
import SubPage1 from "../pages/SubPage1";
import SubPage2 from "../pages/SubPage2";
import SubPage3 from "../pages/SubPage3";
import InfoList from "../pages/InfoList";

Vue.use(VueRouter);

export default new VueRouter({
  mode:'history',
  routes: [
    {
      path: "/index",
      component: Index
    },
    {
      path: "/login",
      component: Login,
      children: [
        {
          path: "sub1",
          component: SubPage1
        },
        {
          path: "sub2",
          component: SubPage2
        },
        {
          path: "",
          redirect: "sub1"
        }
      ]
    },
    {
      path:"/info",
      component:InfoList,
      children:[
        {
          path:"sub3/:id",
          component: SubPage3

        }
      ]
    },
    {
      path: "/",
      redirect: "/index"
    },

  ]

});

```



# vuex

store.js文件是vuex的核心配置文件，它对组件的数据状态进行统一管理,其内容如下

```javascript
//引入模块
import Vue from "vue"
import Vuex from "vuex"

//将模块安装到Vue
Vue.use(Vuex);

//定义Vuex的核心对象
const state = {//用来保存数据状态
  num: 0
}

const actions = {//管理对数据的动作，它起到一个中转站的作用，这里的函数都是异步的
  increment({commit}){//每一个动作函数需要传入一个{commit}对象
    commit("inc");//inc是位于mutations中的函数名称
  },
  decrement({commit}) {
    commit("dec")
  }
}

const mutations = {//管理数据的变化，这里的函数都是同步的
  inc(state){//每一个函数都有一个默认的state参数，该参数就是上面定义的state对象
    state.num++;
  },
  dec(state){
    state.num--;
  }
}

const getters={//获取state的数据
  evenNumber(state) {
    return state.num%2==0?"偶数":"奇数";
  }
}

//定义Store对象
export default new Vuex.Store({
  actions,
  mutations,
  state,
  getters
});

```

然后将它在main.js中注册

```javascript
import Vue from "vue"
import App from "./App"
import router from './router'
import store from "./store";


new Vue({
  el:"#app",
  components:{
    App
  },
  template:"<App/>",
  router,
  store//注册后，每一vue实例，即vue文件都有个一$store的属性，可以通过这个属性对store存储的数据进行			访问
});

```

VuexTest.vue文件

```vue
<template>
  <div>
    <!--调用state和getters中的数据-->
    <p>{{$store.state.num}}是{{$store.getters.evenNumber}}</p>
    <button @click="increment">+</button>
    <button @click="decrement">-</button>
  </div>
</template>

<script>
  export default {
    name: "VuexTest",

    methods: {
      increment(){
        this.$store.dispatch("increment");//通过对象的$store调用actions中的increment方法
      },
      decrement(){
        this.$store.dispatch("decrement");
      }

    }
  }
</script>

<style scoped></style>
```

# webpack

webpack4废弃了`CommonsChunkPlugin`插件，其功能已经内置