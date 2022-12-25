### setup中的声明周期函数

#### 以前的做法

*Vue.app*

```javascript
<template>
  <div>AppContent</div>
</template>

<script>
export default {
  created () {

  },
  beforeMount () {

  },
  mounted () {

  },
  beforeUpdate () {

  },
  updated () {

  }
}
</script>

<!-- scope样式只在当前组件上面有效 -->
<style scoped>
</style>
```

#### 现在的做法

```javascript
<template>
  <div>AppContent</div>
</template>

<script>
// 但是这个地方没有 onCreated，onBeforeCreated
import { onMounted, onUpdated, onUnmounted } from 'vue'
export default {
  // setup是在 create 和 created 之前执行的
  setup () {
    // 执行setup函数的过程中，需要注册别的生命周期函数
    onMounted(() => {
      console.log("onMounted的生命周期")
    })
    // 可以注册多个
    onMounted(() => {
      console.log("onMounted的生命周期")
    })
    //
  }
}
</script>

<!-- scope样式只在当前组件上面有效 -->
<style scoped>
</style>
```

<img src="D:/workspace/QiLongZhang/Vue/Q7Long/LEARN%2520VUEJS/04_learn_composition/src/img/%25E5%25A3%25B0%25E6%2598%258E%25E5%2591%25A8%25E6%259C%259F%25E9%2592%25A9%25E5%25AD%2590.png" alt="生命周期钩子" style="zoom:150%;" />

