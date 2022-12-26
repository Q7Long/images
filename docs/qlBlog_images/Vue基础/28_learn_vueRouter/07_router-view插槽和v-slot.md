## router-view插槽和v-slot

```vue
<!-- 07_learn_vue_router\src\App.vue -->
<template>
  <div id="app">
    <router-link to="/home">首页</router-link>
    <router-link to="/about">关于</router-link>

    <!-- 正常情况下如果要实现组件加载的动画 -->
    <transition>
      <component :is="User"></component>
    </transition>

    <router-view v-slot="props">
      <!-- 如果要实现动画效果，依然要用 transition 进行一层包裹 -->
      <transition name="zql">
        <!-- 可以对组件进行一个缓存和动画效果 -->
        <keep-alive>
          <component :is="props.Component"></component>
        </keep-alive>
      </transition>
    </router-view>
  </div>
</template>

<script setup>
import User from "./components/User.vue";
</script>

<style scoped>
.router-link-active {
  color: red;
}
.zql-active {
  color: blueviolet;
}
/* 动画效果 zql-enter-from 从哪里来  zql-leave-to 到哪里去*/
.zql-enter-from,
.zql-leave-to {
  /* 从透明度0开始，到透明度0结束 */
  opacity: 0;
}
/* 这里还需要一个动画效果，动画是怎么样进行的 */
.zql-enter-active,
.zql-leave-active {
  transition: opacity 3s ease;
}
</style>
```

```js
<router-view v-slot="props">
      <transition name="zql">
        <keep-alive>
          <component :is="props.Component"></component>
        </keep-alive>
      </transition>
   </router-view>

// 下面其实就是一种结构的写法，将 Component 从 props 中解构出来
```

![image-20221223155935236](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C07_router-view%E6%8F%92%E6%A7%BD%E5%92%8Cv-slot.assets%5Cimage-20221223155935236.png)

