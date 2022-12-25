### toRefs

![image-20221208150003542](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C26_Composition%20API%5C05_toRefs.assets%5Cimage-20221208150003542.png)

```vue
<template>
  <div>
    <h2>info:{{info.name}}- {{info.age}}</h2>
    <h2>解构后的info就会变成普通的值，不再响应式:{{name}}-{{age}}-{{height}}</h2>
    <button @click="age++">reactive解构后数据无法响应式，++无效</button>
    <button @click="height+=0.01">toRef可以对单一数据进行转换</button>
  </div>
</template>

<script>
import { reactive, toRefs, toRef } from 'vue'

export default {
  setup () {
    const info = reactive({
      name: "zql",
      age: 18,
      height: 1.8
    })

    //1.  reactive 被解构后会变成普通的值
    // const { name, age } = info

    // 有一种办法就是将结构后的数据使用toRefs方法，恢复响应式
    const { name, age } = toRefs(info)

    // 如果想要对单一数据进行响应式可以使用 toRef
    const height = toRef(info, "height")
    
    return {
      info,
      name,
      age,
      height
    }
  }
}
</script>

<!-- scope样式只在当前组件上面有效 -->
<style scoped>
</style>
```

![image-20221208151636595](D:%5Cworkspace%5CQiLongZhang%5CVue%5CQ7Long%5CVue3%5C%E7%AC%94%E8%AE%B0%5C26_Composition%20API%5C05_toRefs.assets%5Cimage-20221208151636595.png)

```js
// 默认情况下 reactive 和 ref 定义的数据都是深度响应式的
```

