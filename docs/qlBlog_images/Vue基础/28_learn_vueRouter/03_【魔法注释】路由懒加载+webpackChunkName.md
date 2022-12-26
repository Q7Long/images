## 【魔法注释】路由懒加载+webpackChunkName

### webpackChunkName

之前在vue的路由配置文件中看到了 `/* webpackChunkName:"lodash" */` 这个注释

![image-20221223104954716](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C03_%E3%80%90%E9%AD%94%E6%B3%95%E6%B3%A8%E9%87%8A%E3%80%91%E8%B7%AF%E7%94%B1%E6%87%92%E5%8A%A0%E8%BD%BD+webpackChunkName.assets%5Cimage-20221223104954716.png)

####  `/* webpackChunkName:"lodash" */` 这个注释什么作用

在学习了`webpack`之后了解其做用，Webpack通过增加内联注释来告诉运行时，该有怎样的行为。通过向import中添加注释，我们可以执行诸如命名chunk或选择不同模式之类的操作。`webpack`在打包的时候，对异步引入的库代码（`lodash`）进行代码分割时（需要配置`webpack`的`SplitChunkPlugin`插件），为分割后的代码块取得名字

![image-20221223105230085](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C03_%E3%80%90%E9%AD%94%E6%B3%95%E6%B3%A8%E9%87%8A%E3%80%91%E8%B7%AF%E7%94%B1%E6%87%92%E5%8A%A0%E8%BD%BD+webpackChunkName.assets%5Cimage-20221223105230085.png)

#### 如何命名chunk的名称

`/* webpackChunkName: "hello" */`，意味着 `[name]` 等于hello。

```js
import(/* webpackChunkName: "plugins/myModule" */
    './myModule.js')
    .then(myModule => {
        console.log(myModule.default);
    });

// 上面的代码就会按照下面的方式来写，打包出来的chunk文件将会出现在plugins文件夹下，名字叫myModule.a2d1d5d8e7d5d4d4d4se.chunk.js。
```

![image-20221223105956691](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C03_%E3%80%90%E9%AD%94%E6%B3%95%E6%B3%A8%E9%87%8A%E3%80%91%E8%B7%AF%E7%94%B1%E6%87%92%E5%8A%A0%E8%BD%BD+webpackChunkName.assets%5Cimage-20221223105956691.png)

### 路由懒加载(动态chunkName)

[官网链接地址](https://link.juejin.cn/?target=https%3A%2F%2Frouter.vuejs.org%2Fzh%2Fguide%2Fadvanced%2Flazy-loading.html%23%E8%B7%AF%E7%94%B1%E6%87%92%E5%8A%A0%E8%BD%BD)

`import`异步加载的写法实现页面模块`lazy loading`懒加载（`Vue`中的路由异步加载）：

`Vue`中运用`import`的懒加载语句以及`webpack`的魔法注释，在项目进行`webpack`打包的时候，对不同模块进行代码分割，在首屏加载时，用到哪个模块再加载哪个模块，实现懒加载进行页面的优化。 路由懒加载作为性能优化的一种手段，它能让路由组件延迟加载。通常我们还会为延迟加载的路由添加“魔法注释”(webpackChunkName)来自定义包名，在打包时，该路由组件会被单独打包出来。

**作用就是webpack在打包的时候，对异步引入的库代码（lodash）进行代码分割时（需要配置webpack的SplitChunkPlugin插件），为分割后的代码块取得名字**

**Vue中运用import的懒加载语句以及webpack的魔法注释，在项目进行webpack打包的时候，对不同模块进行代码分割，在首屏加载时，用到哪个模块再加载哪个模块，实现懒加载进行页面的优化。**



### 手写的规格

```js
let router = new Router({
  routes: [
    {
      path:'/login',
      name:'login',
      component: import(/* webpackChunkName: "login" */
                                   `@/views/login.vue`)
    },
    {
      path:'/index',
      name:'index',
      component: import(/* webpackChunkName: "index" */
                               `@/views/index.vue`)
    },
    {
      path:'/detail',
      name:'detail',
      component: import(/* webpackChunkName: "detail" */ 
                                  `@/views/detail.vue`)
    }
  ]
})
```

上面这种写法没问题，但仔细一看它们结构都是相似的，其中唯一不同的就是部分的页面名称而已，作为一名出色的`ctrl+c/ctrl+v` 开发者，我们要做到可以偷懒的时候就尽量偷懒，我们可以使用 map 循环来解决这种重复性的工作。

```js
const routeOptions = [
  {
    path:'/login',
    name:'login',
  },
  {
    path:'/index',
    name:'index',
  },
  {
    path:'/detail',
    name:'detail',
  },
]

const routes = routeOptions.map(route => {
  if (!route.component) {
    route = {
      ...route,
      component: () => import(`@/views/${route.name}.vue`)
    }
  }
  return route
})

let router = new Router({
  routes
})
```

在书写更少代码的同时，我们也把“魔法注释”给牺牲掉了。总所周知，代码中没办法编写动态注释。这个问题很尴尬，难道就没有两全其美的办法了吗？

强大的 `webpack` 来救场了，从 `webpack 2.6.0` 开始，占位符 `[index]` 和 `[request]` 被支持为递增的数字或实际解析的文件名。我们可以这样使用“魔法注释”：

```js
const routes = routeOptions.map(route => {
  if (!route.component) {
    route = {
      ...route,
      component: () => import(/* webpackChunkName: "[request]" */
                       `@/views/${route.name}.vue`)
    }
  }
  return route
})
```

使用`[request]`来告诉`webpack`，这里的值是根据后面传入的字符串来决定，本例中就是变量`route.name`的值

