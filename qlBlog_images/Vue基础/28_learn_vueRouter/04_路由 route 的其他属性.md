## 路由 route 的其他属性

#### route 的 name 和 meta 属性

```js
// 07_learn_vue_router\src\router\index.js

import { createRouter, createWebHistory, createWebHashHistory } from 'vue-router'

//1. 配置映射关系，但是现在还没有应用
// route
const routes = [
	// 8. 我们这里可以进行一个重定向，比如我们在访问 baidu.com的时候重定向到google.com上
	{ path: '/', redirect: "/home" },
	{
		path: '/home',
		// name 属性我们在页面跳转的时候可以添加name属性
		name: "home",
	component: () => import(/* webpackChunkName: "home-chunk"*/'../components/Home.vue'),
		// 我们可以在meta中添加其他的一些信息，我们后期可以拿到 route 对象，然后通过 meta 属性拿到这些信息，（比如说导航守卫）
		meta: {
			name: "zql",
			age: 18,
			height: 1.8
		}
	},
	{ path: '/about', component: () => import(/* webpackChunkName: "plugins/about-chunk"*/ '../components/About.vue') }
];

//3. 创建一个路由对象
// router 一个 router 里面可以有多个 route
const router = createRouter({
	// 传入一个对象
	// routes: routes
	routes,
	// 在vue-router 4 的情况下还可以传入另外一个参数
	// 指定一下当前是什么样的模式，第一种是hash，第二种是history
	// history:createWebHistory()/createWebHashHistory
	history: createWebHistory()
})

//4. 导出一个路由对象，需要在main.js中导入，并且使用实例对象app.use(router)
export default router
```

#### 动态路由基本匹配

```js
// 先创建好一个 user组件，然后在 router/index.js 中进行路由懒加载，然后在 App.vue中使用 router-link加载组件切换的 a 标签
```

```js
// 07_learn_vue_router\src\router\index.js
import { createRouter, createWebHistory, createWebHashHistory } from 'vue-router'

//1. 配置映射关系
const routes = [
	{ path: '/', redirect: "/home" },
	{
		path: '/home',
		name: "home",
		component: () => import('../components/Home.vue'),
		meta: {
			name: "zql",
			age: 18,
			height: 1.8
		}
	},
	{ 
    path: '/about', 
    component: () => import('../components/About.vue') 
	},
	{
		// 这样配置之后，那么App.vue中就不能写死，而是要提供一个 username
		path: '/user/:username',
		name: "user",
		component: () => import('../components/User.vue')
	}
];

//3. 创建一个路由对象
const router = createRouter({
	routes,
	history: createWebHistory()
})

export default router
```

```vue
<!-- 07_learn_vuerouter\src\App.vue -->
<template>
  <div id="app">
    <router-link to="/home" replace>首页</router-link>
    <router-link to="/about" active-class="zql-active">关于</router-link>
    <router-link to="/user/zql" replace>用户</router-link>
    <!-- 我们可以将name设置成动态的进行一个拼接 -->
    <router-link :to="'/user/' + name" replace>用户</router-link>
    <router-view />
  </div>
</template>

<script setup>
import { ref } from "vue";
const name = ref("zql");
</script>

<style scoped>
/* 切换时候的有样式显示，比如当 home 被选中的时候就是红色的，当选中 about的时候就是红色的 */
.router-link-active {
  color: red;
}
/* 自定义的切换样式，默认是 router-link-active*/
.zql-active {
  color: blueviolet;
}
</style>
```

```vue
<!-- 07_learn_vue_router\src\components\User.vue -->
<!-- 07_learn_vue_router\src\components\User.vue -->
<template>
  <!-- 在上面这里获取是不需要添加this的，直接使用 $route 即可-->
  <h2>User:{{ $route.params.username }}</h2>
</template>

<script>
import { useRoute } from "vue-router";
export default {
  created() {
    // 在这里获取的话是需要添加上this的
    console.log(this.$route);
  },
  // 上面是在声明周期中拿，但是如果我们是想在setup中拿到数据该怎么办呢？
  // 强调：setup中是没有this的，那么我们该如何拿到这个值呢？
  setup() {
    // 在 vue-router 4版本以上，给我们提供了一个 hook useRoute函数
    const route = useRoute();
    console.log(route.params.username);
    // setup是在 created 之前执行的
  },
};
</script>

<style scoped lang="less"></style>
```

#### 当然我们也可以传入多个数据

```js
{
		// 这样配置之后，那么App.vue中就不能写死，而是要提供一个 username
		path: '/user/:username/:id',
		name: "user",
		component: () => import('../components/User.vue')
}
```

### Not Found

```js
// 当我们匹配到不存在的路径的时候，我们可以匹配到一个 NotFound.vue的组件
```

```js
{
		// .* 正则 .表示任意字符 * 表示0个或者多个任意字符
		path: "/:pathMatch(.*)",
		component: () => import("../components/NotFound.vue")
}
```

```vue
<!-- 07_learn_vue_router\src\components\NotFound.vue -->
<template>
  <div>Page Not Found</div>
  <p>{{ $route.params }}</p>
</template>

<script>
export default {
  name: "NotFound",
  data() {
    return {};
  },
};
</script>

<style scoped lang="less"></style>
```

#### 效果图

![image-20221223121158571](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C04_%E8%B7%AF%E7%94%B1%20route%20%E7%9A%84%E5%85%B6%E4%BB%96%E5%B1%9E%E6%80%A7.assets%5Cimage-20221223121158571.png)

### 第二种写法

```js
{
		// .* 正则 .表示任意字符 * 表示0个或者多个任意字符，多添加一个*，可以把后面的放入一个数组中
		path: "/:pathMatch(.*)*",
		component: () => import("../components/NotFound.vue")
}
```

#### 效果图

![image-20221223121507180](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C04_%E8%B7%AF%E7%94%B1%20route%20%E7%9A%84%E5%85%B6%E4%BB%96%E5%B1%9E%E6%80%A7.assets%5Cimage-20221223121507180.png)

![image-20221223125314287](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C04_%E8%B7%AF%E7%94%B1%20route%20%E7%9A%84%E5%85%B6%E4%BB%96%E5%B1%9E%E6%80%A7.assets%5Cimage-20221223125314287.png)

### 路由的嵌套

```js
// 我们之前写的路由都是在顶层的，但是有的情况就是，当我们进去一个路由页面之后，下面还能够切换，那么我们就需要用到路由的嵌套
// 创建新的组件 HomeMessage.vue 和 HomeProduct.vue
```

```js
// 07_learn_vue_router\src\router\index.js
import { createRouter, createWebHistory, createWebHashHistory } from 'vue-router'

const routes = [
	{ path: '/', redirect: "/home" },
	{
		path: '/home',
		name: "home",
		component: () => import('../components/Home.vue'),
		meta: {
			name: "zql",
			age: 18,
			height: 1.8
		},
		children: [
			{
				// 注意这里也不可以写 /message
				path: "message",
				component: () => import("../components/HomeMessage.vue")
			},
			{
				path: "product",
				component: () => import("../components/HomeProduct.vue")
			}
		]
	},
	{ path: '/about', component: () => import('../components/About.vue') },
	{
		// 这样配置之后，那么App.vue中就不能写死，而是要提供一个 username
		path: '/user/:username/id/:id',
		name: "user",
		component: () => import('../components/User.vue')
	},
	{
		// .* 正则 .表示任意字符 * 表示0个或者多个任意字符
		path: "/:pathMatch(.*)*",
		component: () => import("../components/NotFound.vue")
	}
];

const router = createRouter({
	routes,
	history: createWebHistory()
})

export default router
```

```vue
<!-- 07_learn_vue_router\src\components\Home.vue -->
<template>
  <h2>Home</h2>
  <router-link to="/home/message">消息</router-link>
  <router-link to="/home/product">商品</router-link>
  <!-- 二级路由出口 -->
  <router-view></router-view>
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

![image-20221223133438037](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C04_%E8%B7%AF%E7%94%B1%20route%20%E7%9A%84%E5%85%B6%E4%BB%96%E5%B1%9E%E6%80%A7.assets%5Cimage-20221223133438037.png)

