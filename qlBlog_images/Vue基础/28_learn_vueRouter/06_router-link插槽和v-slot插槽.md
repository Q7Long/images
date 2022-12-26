## 插槽的使用

```js
// 在vue-router3 的时候，router-link中有一个属性 tag，可以指定这个router-link的类型，但是在 vue-router 4之后的版本，这个属性就不能使用了，使我们的 router-link更加的灵活，可以作为一个插槽使用

<router-link to="/home" replace> <button>首页</button> </router-link>
```

#### router-link在 vue-router 4之后的扩展    router-link插槽和v-slot插槽

如果是使用编程式导航的话是基本用不到这些东西的

```vue
<!-- 07_learn_vue_router\src\App.vue -->
<template>
  <div id="app">
    <!-- props: href:跳转的链接 -->
    <!-- props: route对象 -->
    <!-- props:navigate导航 -->
    <!-- props:isActive是否处于活跃状态 -->
    <!-- props:isExactActive是否精确处于活跃状态 -->
    <router-link to="/home" v-slot="props">
      <button>{{ props.href }}</button>
      <!-- <p>{{ props.route }}</p> -->
      <button @click="props.navigate">navigate跳转</button>
      <!-- 比如这样我们就可以动态的添加 class属性 -->
      <span :class="{ active: props.isActive }">{{ props.isActive }}</span>
      <!-- 是否精准匹配到路径，比如说这里的props.isActive在 /home/message /home情况下都是 true 但是 props.isExactActive 只有在 /home 情况下才是 true -->
      <span :class="{ active: props.isExactActive }">{{
        props.isExactActive
      }}</span>
    </router-link>
    <router-link to="/about">关于</router-link>
    <router-view />
  </div>
</template>

<script setup></script>

<style scoped>
.router-link-active {
  color: red;
}
.zql-active {
  color: blueviolet;
}
.active {
  color: blue;
  font-size: 23px;
}
</style>

```

![image-20221223155702641](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C28_learn_vueRouter%5C06_router-link%E6%8F%92%E6%A7%BD%E5%92%8Cv-slot%E6%8F%92%E6%A7%BD.assets%5Cimage-20221223155702641.png)

