### 认识 vue-router

![image-20221222175116168](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C02_vue-router%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8%E5%92%8C%E9%85%8D%E7%BD%AE.assets%5Cimage-20221222175116168.png)

```js
npm install vue-router@4

// 路由的使用步骤
使用vue-router的步骤
- 第一步：创建路由组件的组件
- 第二步：配置路由映射：组件和路径映射关系的routes数组
- 第三步：通过createRouter创建路由对象，并且传入 routes 和 history 模式
- 第四步：使用路由：通过<router-link> 和 <router-view>

// 既然要一个路由路径对应一个组件，那么我们就需要先创建出来一个组件
```

### 使用vue-router

```vue
页面结构
- src
  - components
     - About.vue
	 - Home.vue
  - router
	 - index.js
  - App.vue
  - main.js
```

```js
//1.  首先创建两个组件分别叫做 Home 和 About 组件部分
```

```vue
<!-- 07_learn_vuerouter\src\components\About.vue -->
<template>
  <div>About</div>
  <button>About</button>
</template>

<script>
export default {
  name: "About",
  data() {
    return {};
  },
};
</script>

<style scoped lang="less"></style>
```

```vue
<!-- 07_learn_vuerouter\src\components\Home.vue -->
<template>
  <div>Home</div>
</template>

<script>
export default {
  name: "Home",
  data() {
    return {};
  },
};
</script>

<style scoped lang="less"></style>
```

```js
//2. 路由代码部分
```

```js
// 07_learn_vuerouter\src\router\index.js

import { createRouter, createWebHistory, createWebHashHistory } from 'vue-router'

//1. 引入组件
import Home from '../components/Home.vue'
import About from '../components/About.vue'

//2. 配置映射关系，但是现在还没有应用
const routes = [
	{ path: '/home', component: Home },
	{ path: '/about', component: About }
];

//3. 创建一个路由对象
const router = createRouter({
	// 传入一个对象
	// routes: routes
	routes,
	// 在vue-router 4 的情况下还可以传入另外一个参数
	// 指定一下当前是什么样的模式，第一种是hash，第二种是history
	// history:createWebHistory()/createWebHashHistory
	history: createWebHashHistory()
})

//4. 导出一个路由对象，需要在main.js中导入，并且使用实例对象app.use(router)
export default router
```

```js
// mian.js 部分 需要引入插件部分
```

```js
// 07_learn_vuerouter\src\main.js
import { createApp } from 'vue'
// 5.导入
import App from './App.vue'

import router from './router'

const app = createApp(App)

//6. 使用路由对象，用use使用插件，router其实就是一个插件
app.use(router)
// 需要注意的是，这里了是最后再进行挂载的操作，否则会失效
app.mount('#app')

// 但是还需要一个路由出口才能显示组件内容 提供了一个 <router-view></router-view>
```

```js
// 首页部分
```

```vue
<!-- 07_learn_vuerouter\src\App.vue -->
<template>
  <div id="app">
    <!-- 7. 这里可以使用 router-link来设置点击跳转的操作 -->
    <router-link to="/home">首页</router-link>
    <router-link to="/about">关于</router-link>
    <router-view />
  </div>
</template>

<script>
export default {
  name: "",
  data() {
    return {};
  },
};
</script>

<style scoped lang="less"></style>
```

![image-20221222221309418](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C02_vue-router%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8%E5%92%8C%E9%85%8D%E7%BD%AE.assets%5Cimage-20221222221309418.png)

### 路由的默认路径
```js
// 当我们第一次进入网页的话，就会被重定向到登录页面
// 比如当我们访问一个网页的默认路径的时候，那么就会被重定向到首页或者是登录页
```

![image-20221222221337802](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C02_vue-router%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8%E5%92%8C%E9%85%8D%E7%BD%AE.assets%5Cimage-20221222221337802.png)

```js
import { createRouter, createWebHistory, createWebHashHistory } from 'vue-router'

//1. 引入组件
import Home from '../components/Home.vue'
import About from '../components/About.vue'

//2. 配置映射关系，但是现在还没有应用
const routes = [
	// 8. 我们这里可以进行一个重定向，比如我们在访问 baidu.com的时候重定向到google.com上
	{ path: '/', redirect: "/about" },
	{ path: '/home', component: Home },
	{ path: '/about', component: About }
];

//3. 创建一个路由对象
const router = createRouter({
	// 传入一个对象
	// routes: routes
	routes,
	// 在vue-router 4 的情况下还可以传入另外一个参数
	// 指定一下当前是什么样的模式，第一种是hash，第二种是history
	// history:createWebHistory()/createWebHashHistory
	history: createWebHashHistory()
})

//4. 导出一个路由对象，需要在main.js中导入，并且使用实例对象app.use(router)
export default router
```

### router-link

![image-20221223095025984](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C02_vue-router%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8%E5%92%8C%E9%85%8D%E7%BD%AE.assets%5Cimage-20221223095025984.png)

```vue
<!-- 07_learn_vuerouter\src\App.vue -->
<template>
  <div id="app">
  <!-- 这里可以使用 router-link来设置点击跳转的操作 -->
  <!-- replace 当点击的时候会调用 router.replace() 而不是 router.push() 用户不可以进行返回操作 -->
  <!-- 默认样式不需要在 router-link中添加属性，而自定义样式则需要在router-link中添加属性-->
    <router-link to="/home" replace>首页</router-link>
    <router-link to="/about" active-class="zql-active">关于</router-link>
    <router-view />
  </div>
</template>

<script>
export default {
  name: "",
  data() {
    return {};
  },
};
</script>

<style scoped>
/*1. 切换时候的有样式显示，比如当 home 被选中的时候就是红色的，当选中 about的时候就是红色的 */
.router-link-active {
  color: red;
}
/*2. 自定义的切换样式，默认是 router-link-active*/
.zql-active {
  color: blueviolet;
}
</style>
```

![image-20221223133553602](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C02_vue-router%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8%E5%92%8C%E9%85%8D%E7%BD%AE.assets%5Cimage-20221223133553602.png)

### 路由懒加载

```js
// 1. 如果我们有个组件特别大的话，那么当我们需要使用这个组件的时候，我们可以使用异步组件，那么我们在对代码进行打包的时候，就可以对这个组件进行打包到一个文件中，当我们进入主页的时候，暂时不需要加载这个组件，就可以先不下载这个组件的文件，当我们需要的时候再去下载


// 2. 但是当我们有很多个组件，比如说有10个组件，用户第一次看到的应该是首页的组件，我们后面的组件是不需要加载的，但是我们对所有的组件进行打包的话，默认是都被打包在了App.js这个文件中，当我们在资源中进行部署的话，用户需要将整个App.js文件下载下来，那么效率就会很慢，因为一起打包的话，不需要的也被加载了

// 官方推荐使用的是路由懒加载，那么我们打包出来的就是每一个组件都有一个单独的js文件，我们用不到的js暂时可以不下载下来，当我们需要的时候是不用下载的
```

```js
// 没有使用路由懒加载的情况下打包出来的结果
// 这种方式我们是通过普通的 import 方式进行导入的，那么整个项目对于组件的依赖就是同步的，那么进行打包的时候就是打包在一起的
```

![image-20221223100535204](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C02_vue-router%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8%E5%92%8C%E9%85%8D%E7%BD%AE.assets%5Cimage-20221223100535204.png)

#### 使用路由懒加载

```js
// 使用路由懒加载的时候，我们可以不使用 Import 的方式导入，我们 routes 中传入的 component 属性可以使用一个函数的形式进行传入，但是重要的一点是 *** 这个函数返回的必须是一个 promise ***

// import 形式的函数返回的就是一个 promise 比如我们之前写过

import("../components/Home.vue").then(res=>{
    
})

// 这种其实就是通过 import 方式导入的 promise
```

```js
import Vue from 'vue'
import HotMusicList from '../views/HotMusicList'

const routes = [
  {
    path: '/hot',
    component: HotMusicList
  },
  {
    // 动态路径匹配 通过:id获取每一首歌不同的id
    path: '/music/:id',
    // 路由的懒加载,通过函数的形式,可以让项目中哪些不许一开始就要加载的组件,加载到项目中去
    // 只有浏览器跳转到当前路由时,该路由组件才会加载到项目中去
    // 这样做的好处是减少不必要的加载降低应用加载速度和运行带宽
    component: () => import('../views/MusicPlay') 
  }
]
```

![image-20221223103122216](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C02_vue-router%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8%E5%92%8C%E9%85%8D%E7%BD%AE.assets%5Cimage-20221223103122216.png)

#####  如果我们想给打包出来的 js 文件设置名字，那么我们可以使用 webpack的特性 魔法注释 margic comment

![image-20221223103948901](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C02_vue-router%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8%E5%92%8C%E9%85%8D%E7%BD%AE.assets%5Cimage-20221223103948901.png)

![image-20221223105938227](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C02_vue-router%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8%E5%92%8C%E9%85%8D%E7%BD%AE.assets%5Cimage-20221223105938227.png)

